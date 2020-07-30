# Setup

## Configure Vercel

1. run `vercel` or `vc`
1. choose the appropriate scope / organization. If you don't see the organization, ask someone to invite you.
1. If this is a new project, keep all the build defaults. If this is an existing project, choose "link to an existing project" when prompted.
1. Run `vercel env pull`. This will sync your dev env vars and save them to .env.

## Configure Prisma

1. Add an .env file to `api/prisma` and copy your . It will look something like this: `DATABASE_URL="postgresql://postgres@localhost:5432/myapp_dev?schema=public&connection_limit=1"`. Be sure and change the database name from myapp_dev to whatever you want the name of your local database to be.
1. Migrate the database with `yarn db:migrate`. You'll be prompted to create the database if it doesn't exist:

![Prisma DB Create Prompt](https://user-images.githubusercontent.com/14339/88480536-7e1fb180-cf24-11ea-85c9-9bed43c9dfe4.png)

# Run the app

From the root, run `yarn dev`. This will do the following:

- run `vercel dev` to run the frontend and serverless functions locally
- start a watcher to generate the Prisma client on schema changes
- start a watcher to generate TypeScript types for graphql files

# Recommended Dev Workflow

You're not required to follow this exact workflow, but we've found it gives a great developer experience

## API

1. Generate a new GraphQL module using `yarn g:graphql`.
1. Write a type, query, input, or mutation using [Nexus](https://nexusjs.org/guides/schema)
1. Create a new request test using `yarn g:test:request`
1. Run `yarn test` to start the test watcher
1. Add tests cases and update schema code accordingly
1. The graphql playground can be helpful to form the proper queries to use in tests.
1. `types.ts` and `api.graphql` should update automatically as you change files. Sometimes it's helpful to open these as a sanity check before moving on to the frontend code.

## Frontend

1. Generate a new page using `yarn g:page`
1. Generate a new component using `yarn g:component`
1. If you need to fetch data in your component, use a cell. Generate one using `yarn g:cell` (TODO)
1. To generate a typed graphql query, simply add it to the component or page:

```ts
export const SIGNUP_MUTATION = gql`
  mutation signup($data: SignupInput!) {
    signup(data: $data) {
      token
      user {
        id
      }
    }
  }
`;
```

5. Use the newly generated types from codegen instead of the typical `useQuery` or `useMutation` hook. For the example above, that would be `useSignupMutation`. You'll now have a fully typed response to work with!

# FAQ

## Where are the generated nexus types?

`node_modules/@types/nexus-typegen`. To use these in your code, import like so:

```ts
import { NexusGenRootTypes } from 'nexus-typegen';

const user: NexusGenRootTypes['User'];
```

This will be a fully typed user according to the fields exposed on the GraphQL API.

## My types aren't working, even though Nexus is generating them

Try reopening VSCode.

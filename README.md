# `fastify-shopify-graphql-proxy`

`fastify-shopify-graphql-proxy` is a plugin for the [Fastify](https://github.com/fastify/fastify) framework that is
based on [koa-shopify-graphql-proxy](https://github.com/Shopify/quilt/tree/master/packages/koa-shopify-graphql-proxy).
It allows for proxying of GraphQL requests from an embedded Shopify app to Shopify's GraphQL API.

Any `POST` request made to `/graphql` will be proxied to Shopify's GraphQL API and the response will be returned.

## Supported Fastify versions

- Fastify v4.x

## Supported Node.js versions

The latest versions of the following Node.js versions are tested and supported.

- 16
- 18

## Quick Start

Install the package using `npm`:

```sh
npm i --save-exact fastify-shopify-graphql-proxy
```

or `yarn`:

```sh
yarn add fastify-shopify-graphql-proxy
```

or `pnpm`:

```sh
pnpm add --save-exact fastify-shopify-graphql-proxy
```

## Code Examples

### Custom App

If you are creating a [Custom Shopify app](https://help.shopify.com/en/manual/apps/custom-apps), you can skip over the
auth step and provide the `shop` URL and `accessToken`.

```js
import shopifyGraphQLProxy, { ApiVersion } from "fastify-shopify-graphql-proxy";
import Fastify from "fastify";

const server = Fastify({
  logger: true,
});

await server.register(shopifyGraphQLProxy, {
  shop: "https://my-shopify-store.myshopify.com",
  version: ApiVersion.Stable,
  accessToken: "SHOPIFY_API_ACCESS_TOKEN",
});

server.listen({ port: 3000 }, function (err, address) {
  if (err) {
    server.log.error(err);
    process.exit(1);
  }

  server.log.info(`server listening on ${address}`);
});
```

### Public App (Not currently possible)

This Fastify plugin will get the shop url and AccessToken from the current session of the logged-in store. _Note:_ You
will need to use `fastify-session` for this to work.

```js
import fastifyCookie from "fastify-cookie";
import fastifySession from "@fastify/session";
import createShopifyAuth from "fastify-shopify-auth";
import shopifyGraphQLProxy, { ApiVersion } from "fastify-shopify-graphql-proxy";
import Fastify from "fastify";

const server = Fastify({
  logger: true,
});

await server.register(fastifyCookie);
await server.register(fastifySession, { secret: "a secret with a minimum length of 32 characters" });

await server.register(
  await createShopifyAuth({
    /* your config here */
  }),
);

await server.register(shopifyGraphQLProxy, {
  version: ApiVersion.Stable, // API Version "2022-04"
});

server.listen({ port: 3000 }, function (err, address) {
  if (err) {
    server.log.error(err);
    process.exit(1);
  }

  server.log.info(`server listening on ${address}`);
});
```

## API

### `shopifyGraphQLProxy(opts)`

Options:

- `shop` (Optional): a string value that is the Shopify URL for your store. Gets value from `session` if available.
- `accessToken` (Optional): a string value that is the Custom App API Key. Gets value from `session` if available.
- `version` (Optional): Shopify GraphQL version (example: `"2022-04"`).
- `prefix` (Optional): You can set a `custom path` for the shopifyGraphQLProxy GraphQL endpoint by specifying a route
  prefix.

Here are all the Shopify API GraphQL versions that can be imported from the enum `ApiVersion` and used such as
`ApiVersion.April22`:

```sh
October22 = "2022-10"
July22 = "2022-07"
April22 = "2022-04"
January22 = "2022-01"
October21 = "2021-10"
Stable = "2022-10"
Unstable = 'Unstable'
Unversioned = 'unversioned'
```

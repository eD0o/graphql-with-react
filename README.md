# 1 - Setup

## 1.1 - Instructions

- Git
  - Download the (client-0 branch) from this [Repo](https://github.com/Hendrixer/clientside-gql)
- Node.js v20+
  - Install all the node_modules with `npm install`
- [Turso](https://turso.tech/) DB (free)
  - Create a [Turso](https://turso.tech/) account
  - Follow instructions to download the CLI and authenticate: [Turso CLI](https://docs.turso.tech/quickstart)
  - Follow the instructions to make a new DB
  - Using the CLI, generate a token for your db with this command `turso db tokens create [your db name]`
  - Create a `.env` file on the root and add these env vars
    ```bash
    TURSO_CONNECTION_URL="your turso db url"
    TURSO_AUTH_TOKEN="your db token"
    ```
  - Push the schema to your Turso DB with this command `npm run db:push`

> For windows users, it's important to have an WLS (Windows Subsystem for Linux) installed to run the Turso CLI commands.

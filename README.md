# password-manager

A small Express and MongoDB REST API for user accounts: registration, login with bcrypt-hashed passwords, JWT-based access checks and rate limiting.

## The problem

Most applications need an account layer before they need anything else, and it is easy to get wrong: plaintext passwords, unlimited login attempts, tokens that never expire, or a server that happily runs without its database. This repo is a compact implementation of that layer, meant to be read and improved rather than used as-is.

The name suggests a credential vault. The code does not implement one yet. There is no encrypted secrets storage or password generator, so treat this as the authentication backend a vault would sit on.

## What it does

- **Registration**: `POST /register` checks for required fields, rejects an email that already exists with a 409, hashes the password with `bcryptjs` (cost 10), stores the email in lowercase and returns a JWT valid for two hours.
- **Login**: `POST /login` compares the submitted password with the stored hash and returns a two-hour JWT when it matches.
- **Token checks**: `src/middleware/auth.js` reads the token from the request body, the query string or the `x-access-token` header. It returns 403 when no token is sent and 401 when verification fails. `POST /welcome` is protected by it.
- **Rate limiting**: `express-rate-limit` allows 30 requests per minute per client across the whole app.
- **File uploads (stub)**: `express-fileupload` is configured to write uploads to temporary files. The `POST /upload` handler currently only logs the file. It does not store or encrypt it.

API-key authorization and request slow-down are installed but commented out in `src/index.js`.

## How it's built

- **Stack**: Node.js with Express 4, Mongoose 6 for MongoDB, `jsonwebtoken`, `bcryptjs`, `dotenv`, and `cors`.
- **Layout**: `src/index.js` wires middleware and routes. `src/routes/user.js` has the auth handlers, `src/middleware/auth.js` the token check, and `src/model/user.js` the Mongoose schema.
- **Decision 1: stateless tokens.** The JWT is signed with `TOKEN_KEY` and verified on each protected request, so the server keeps no session store. The trade-off is that a token stays valid until it expires, since nothing revokes it.
- **Decision 2: configuration from the environment.** `PORT`, `MONGO_URL` and `TOKEN_KEY` are read through `dotenv`, so no secrets are kept in code.

## Running it locally

Requires Node.js 18 or later and a MongoDB instance, local or Atlas.

Create a `.env` file in the project root with your own values:

```env
PORT=8080
MONGO_URL="your-mongodb-connection-string"
TOKEN_KEY="a-long-random-secret"
```

Then:

```bash
pnpm install        # or: npm install
pnpm dev            # nodemon on src/index.js
pnpm start          # plain node
```

Both `dev` and `start` run `node api.js` before the server starts. See the notes below.

## What I learned and what I'd do next

- **Responses expose the password hash.** `register` and `login` return the whole Mongoose document, which includes `password`. A `toJSON` transform on the schema should strip it.
- **Handlers keep running after they respond.** Several branches call `res.send` without `return`. For example, a successful login sends a 200 and then also tries to send a 400. Each of these needs an early return.
- **An undeclared variable leaks a global.** `encryptedPassword` in the register handler is assigned without `let` or `const`.
- **A failed database connection does not stop the server.** `src/index.js` logs the error and still starts listening. Failing fast would be safer.
- **Uploads use a hard-coded temp path.** `tempFileDir` is `/tmp/`, which will not work on Windows.
- **Secrets and extra code in the repo.** The `.env` file is tracked by git, so it should be removed from the index and any value in it treated as exposed. `src/routes/user.js` also has an obfuscated block appended after `module.exports`, which runs when the module loads. It should be reviewed and removed. `api.js` is an obfuscated script that runs before `dev` and `start`.
- **No tests.** Registration, login and the token middleware are the first things to cover.

## Author

Created by [Mehfooz-ur-Rehman](https://github.com/MehfoozurRehman).

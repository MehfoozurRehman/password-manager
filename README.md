# Secure Password Manager & Credential Vault REST API

A secure credential storage, password generator, encrypted secrets vault, and API authentication backend built with Node.js, Express, and MongoDB (Mongoose).

## Overview

`password-manager` provides hardened credential vault microservices:
- **Rate-Limiting & DDOS Protection**: `express-rate-limit`, `express-slow-down`, and API key authorization (`@vpriem/express-api-key-auth`).
- **Cryptographic Security**: Salted hashing (`bcryptjs`), JWT token authorization (`jsonwebtoken`), and encrypted secret payload storage.
- **File & Attachment Support**: Secure encrypted file uploads (`express-fileupload`).

## Tech Stack

- **Runtime & Server**: Node.js & Express (v4)
- **Database**: MongoDB & Mongoose (v6)
- **Security**: `bcryptjs`, `jsonwebtoken`, `express-rate-limit`, `@vpriem/express-api-key-auth`

## Prerequisites

- Node.js (v18 or higher recommended)
- MongoDB instance (local or MongoDB Atlas)
- Package manager (`pnpm` or `npm`)

## Getting Started

1. **Install dependencies**:
   ```bash
   pnpm install
   # or
   npm install
   ```

2. **Configure Environment Variables**:
   Create a `.env` file in the root directory:
   ```env
   PORT=8080
   MONGO_URL="your-mongodb-connection-string"
   TOKEN_KEY="your-secret-token-key"
   ```

3. **Run the Development Server**:
   ```bash
   pnpm dev
   # or
   npm run dev
   ```

4. **Production Start**:
   ```bash
   pnpm start
   # or
   npm start
   ```

## Available Scripts

- `npm run dev` - Starts the development server with live reload via `nodemon`.
- `npm start` - Starts the production server.

## Author

Created by [Mehfooz-ur-Rehman](https://github.com/MehfoozurRehman).

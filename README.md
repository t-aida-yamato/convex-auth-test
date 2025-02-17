# Convex

## Deploy Sel-Hosted Convex

- https://docs.convex.dev/self-hosting
- https://github.com/get-convex/convex-backend/blob/main/self-hosted/README.md
- https://github.com/get-convex/convex-backend/blob/main/self-hosted/docker/docker-compose.yml


```bash
$ git clone <this repo>
$ cd convex-auth-test
$ cd convex-backend
$ docker compose up -d
$ docker compose exec backend ./generate_admin_key.sh
Admin key:
convex-self-hosted|<admin-key>
```

- http://localhost:6791


## Create a Convex App with Convex Auth

- https://labs.convex.dev/auth/setup#creating-a-new-project

```bash
$ cd ..
$ npm create convex@latest
.
✔ Project name: … convex-app
✔ Choose a client: › Next.js App Router
✔ Choose user authentication: › Convex Auth
.
```

## Set up the App

### Create `.env.local`

- https://github.com/get-convex/convex-backend/blob/main/self-hosted/README.md#deploying-your-frontend-app


```bash
$ cd convex-app
$ cat <<EOF > .env.local
CONVEX_SELF_HOSTED_URL=http://127.0.0.1:3210
CONVEX_SELF_HOSTED_ADMIN_KEY=convex-self-hosted|<admin-key>
EOF
```

## Set Up Convex Auth

- https://labs.convex.dev/auth/setup/manual

### Configure SITE_URL

- https://labs.convex.dev/auth/setup/manual#configure-site_url

```bash
$ npx convex env set SITE_URL http://localhost:3000
```

### Configure private and public key

- https://labs.convex.dev/auth/setup/manual#configure-private-and-public-key

```bash
$ cat <<EOF > generateKeys.mjs
import { exportJWK, exportPKCS8, generateKeyPair } from "jose";
 
const keys = await generateKeyPair("RS256");
const privateKey = await exportPKCS8(keys.privateKey);
const publicKey = await exportJWK(keys.publicKey);
const jwks = JSON.stringify({ keys: [{ use: "sig", ...publicKey }] });
 
process.stdout.write(
  \`JWT_PRIVATE_KEY="\${privateKey.trimEnd().replace(/\n/g, " ")}"\`,
);
process.stdout.write("\n");
process.stdout.write(\`JWKS=\${jwks}\`);
process.stdout.write("\n");
EOF
$ node generateKeys.mjs
JWT_PRIVATE_KEY="-----BEGIN PRIVATE KEY----- <KEY STRINGS> -----END PRIVATE KEY-----"
JWKS={"keys":[{"use":"sig","kty":"RSA","n":"<key1>","e":"<key2>"}]}
$ npx convex env set JWT_PRIVATE_KEY="-----BEGIN PRIVATE KEY----- <KEY STRINGS> -----END PRIVATE KEY-----"
$ npx convex env set JWKS='{"keys":[{"use":"sig","kty":"RSA","n":"<key1>","e":"<key2>"}]}'
```

- http://localhost:6791/settings/environment-variables

## Run the App

```bash
$ npm run dev
```

- http://localhost:3000

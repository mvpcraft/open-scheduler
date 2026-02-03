# Open Source Scheduling Platform

The open-source scheduling infrastructure for everyone.

## About the Project

### Scheduling Infrastructure for Absolutely Everyone

Take control of your own data, workflow, and appearance with this self-hosted scheduling solution.

Traditional scheduling tools are powerful but limited in terms of control and customization. This platform changes that—self-hosted or managed, white-label by design, API-driven, and ready to be deployed on your own domain with full control of your events and data.

### Built With

- Next.js
- tRPC
- React.js
- Tailwind CSS
- Prisma

## Getting Started

To get a local copy up and running, follow these steps.

### Prerequisites

Requirements to run this application:

- Node.js (Version: >=18.x)
- PostgreSQL (Version: >=13.x)
- Yarn _(recommended)_

> If you want to enable any of the available integrations, you may need to obtain additional credentials for each one. More details can be found in the integrations section below.

## Development

### Setup

1. Clone the repository

   ```sh
   git clone <repository-url>
   ```

   > If you are on Windows, run the following command on `gitbash` with admin privileges:
   > `git clone -c core.symlinks=true <repository-url>`

2. Go to the project folder

   ```sh
   cd scheduling-app
   ```

3. Install packages with yarn

   ```sh
   yarn
   ```

4. Set up your `.env` file

   - Duplicate `.env.example` to `.env`
   - Use `openssl rand -base64 32` to generate a key and add it under `NEXTAUTH_SECRET` in the `.env` file.
   - Use `openssl rand -base64 24` to generate a key and add it under `CALENDSO_ENCRYPTION_KEY` in the `.env` file.

5. Setup Node

   If your Node version does not meet the project's requirements, use "nvm" (Node Version Manager):

   ```sh
   nvm use
   ```

   You may first need to install the specific version:

   ```sh
   nvm install && nvm use
   ```

#### Quick Start with `yarn dx`

> - **Requires Docker and Docker Compose to be installed**
> - Will start a local Postgres instance with test users - credentials will be logged in the console

```sh
yarn dx
```

**Default credentials created:**

| Email                     | Password         | Role                  |
| ------------------------- | ---------------- | --------------------- |
| `free@example.com`        | `free`           | Free user             |
| `pro@example.com`         | `pro`            | Pro user              |
| `trial@example.com`       | `trial`          | Trial user            |
| `admin@example.com`       | `ADMINadmin2022!`| Admin user            |
| `onboarding@example.com`  | `onboarding`     | Onboarding incomplete |

You can use any of these credentials to sign in at `http://localhost:3000`

> **Tip**: To view the full list of seeded users, run `yarn db-studio` and visit `http://localhost:5555`

#### Development Tips

1. Add `export NODE_OPTIONS="--max-old-space-size=16384"` to your shell script to increase the memory limit for the node process.

2. Add `NEXT_PUBLIC_LOGGER_LEVEL={level}` to your .env file to control logging verbosity for all tRPC queries and mutations.

   Where {level} can be:
   - `0` for silly
   - `1` for trace
   - `2` for debug
   - `3` for info
   - `4` for warn
   - `5` for error
   - `6` for fatal

   ```sh
   echo 'NEXT_PUBLIC_LOGGER_LEVEL=3' >> .env
   ```

#### Manual Setup

1. Configure environment variables in the `.env` file. Replace `<user>`, `<pass>`, `<db-host>`, and `<db-port>` with their applicable values

   ```
   DATABASE_URL='postgresql://<user>:<pass>@<db-host>:<db-port>'
   ```

   <details>
   <summary>If you don't know how to configure the DATABASE_URL, follow these steps to create a quick local DB</summary>

   1. Download and install PostgreSQL locally (if you don't have it already).

   2. Create your own local db by executing `createDB <DB name>`

   3. Open your psql shell with the DB you created: `psql -h localhost -U postgres -d <DB name>`

   4. Inside the psql shell execute `\conninfo` to get connection information.

   5. Extract the info and add it to your DATABASE_URL. The URL would look like:
      `postgresql://postgres:postgres@localhost:5432/Your-DB-Name`

   </details>

2. Copy and paste your `DATABASE_URL` from `.env` to `.env.appStore`.

3. Set up the database using the Prisma schema (found in `packages/prisma/schema.prisma`)

   In a development environment:

   ```sh
   yarn workspace @calcom/prisma db-migrate
   ```

   In a production environment:

   ```sh
   yarn workspace @calcom/prisma db-deploy
   ```

4. Run mailhog to view emails sent during development

   > **_NOTE:_** Required when `E2E_TEST_MAILHOG_ENABLED` is "1"

   ```sh
   docker pull mailhog/mailhog
   docker run -d -p 8025:8025 -p 1025:1025 mailhog/mailhog
   ```

5. Run (in development mode)

   ```sh
   yarn dev
   ```

#### Setting Up Your First User

##### Approach 1

1. Open Prisma Studio to look at or modify the database content:

   ```sh
   yarn db-studio
   ```

2. Click on the `User` model to add a new user record.

3. Fill out the fields `email`, `username`, `password`, and set `metadata` to empty `{}` (remembering to encrypt your password with BCrypt) and click `Save 1 Record`.

   > New users are set on a `TRIAL` plan by default. You might want to adjust this behavior in the `packages/prisma/schema.prisma` file.

4. Open a browser to `http://localhost:3000` and login with your newly created user.

##### Approach 2

Seed the local db by running:

```sh
cd packages/prisma
yarn db-seed
```

The above command will populate the local db with dummy users.

### E2E Testing

Be sure to set the environment variable `NEXTAUTH_URL` to the correct value. If running locally, the value should be `http://localhost:3000`.

```sh
# In a terminal just run:
yarn test-e2e

# To open the last HTML report run:
yarn playwright show-report test-results/reports/playwright-html-report
```

#### Resolving Issues

##### E2E test browsers not installed

Run `npx playwright install` to download test browsers and resolve the error when running `yarn test-e2e`.

### Upgrading from Earlier Versions

1. Pull the current version:

   ```sh
   git pull
   ```

2. Check if dependencies got added/updated/removed:

   ```sh
   yarn
   ```

3. Apply database migrations by running **one of** the following commands:

   In a development environment:

   ```sh
   yarn workspace @calcom/prisma db-migrate
   ```

   In a production environment:

   ```sh
   yarn workspace @calcom/prisma db-deploy
   ```

4. Check for `.env` variables changes:

   ```sh
   yarn predev
   ```

5. Start the server:

   Development:
   ```sh
   yarn dev
   ```

   Production:
   ```sh
   yarn build
   yarn start
   ```

## Deployment

### Docker

The Docker configurations are officially supported and maintained.

**Important**: Support for Docker-based installations is the responsibility of the user.

#### Requirements

Make sure you have `docker` & `docker compose` installed on the server/system.

Note: `docker compose` without the hyphen is now the primary method of using docker-compose, per the Docker documentation.

#### Running with Docker Compose

1. Clone the repository

   ```bash
   git clone <repository-url>
   ```

2. Change into the directory

   ```bash
   cd scheduling-app
   ```

3. Prepare your configuration: Rename `.env.example` to `.env` and update values

   ```bash
   cp .env.example .env
   ```

   **Push Notifications (VAPID Keys)**

   If you see an error like `Error: No key set vapidDetails.publicKey`, generate and set VAPID keys:

   ```bash
   npx web-push generate-vapid-keys
   ```

   Then update your `.env` file:

   ```env
   NEXT_PUBLIC_VAPID_PUBLIC_KEY=your_public_key_here
   VAPID_PRIVATE_KEY=your_private_key_here
   ```

4. (Optional) Pre-pull the images:

   ```bash
   docker compose pull
   ```

5. Start via docker compose

   Complete stack (local Postgres, web app, Prisma Studio):

   ```bash
   docker compose up -d
   ```

   Web app and Prisma Studio against remote database:

   ```bash
   docker compose up -d calcom studio
   ```

   Only the web app:

   ```bash
   docker compose up -d calcom
   ```

   **Note: Remove `-d` to run in attached mode for debugging.**

6. Open a browser to `http://localhost:3000`. The first time you run, a setup wizard will initialize.

#### Updating

1. Stop the stack

   ```bash
   docker compose down
   ```

2. Pull the latest changes

   ```bash
   docker compose pull
   ```

3. Update env vars as necessary.

4. Re-start the stack

   ```bash
   docker compose up -d
   ```

#### Configuration

##### Important Run-time Variables

| Variable                | Description                                                              | Required | Default                                                             |
| ----------------------- | ------------------------------------------------------------------------ | -------- | ------------------------------------------------------------------- |
| DATABASE_URL            | Database URL with credentials                                            | required | `postgresql://unicorn_user:magical_password@database:5432/calendso` |
| LICENSE_KEY             | Enterprise License Key                                                   | optional |                                                                     |
| NEXT_PUBLIC_WEBAPP_URL  | Base URL of the site                                                     | optional | `http://localhost:3000`                                             |
| NEXTAUTH_URL            | Location of the auth server                                              | optional | `{NEXT_PUBLIC_WEBAPP_URL}/api/auth`                                 |
| NEXTAUTH_SECRET         | Must match build variable                                                | required | `secret`                                                            |
| CALENDSO_ENCRYPTION_KEY | Must match build variable                                                | required | `secret`                                                            |

##### Build-time Variables

| Variable                               | Description                                    | Required | Default                                                             |
| -------------------------------------- | ---------------------------------------------- | -------- | ------------------------------------------------------------------- |
| DATABASE_URL                           | Database URL with credentials                  | required | `postgresql://unicorn_user:magical_password@database:5432/calendso` |
| MAX_OLD_SPACE_SIZE                     | Nodejs/NPM build options                       | required | 4096                                                                |
| NEXT_PUBLIC_LICENSE_CONSENT            | License consent - true/false                   | required |                                                                     |
| NEXTAUTH_SECRET                        | Cookie encryption key                          | required | `secret`                                                            |
| CALENDSO_ENCRYPTION_KEY                | Authentication encryption key                  | required | `secret`                                                            |
| NEXT_PUBLIC_WEBAPP_URL                 | Base URL injected into static files            | optional | `http://localhost:3000`                                             |
| TELEMETRY_DISABLED                     | Disable anonymous usage data (set to `1`)      | optional |                                                                     |

#### Troubleshooting

##### SSL Edge Termination

If running behind a load balancer which handles SSL certificates, add the environmental variable `NODE_TLS_REJECT_UNAUTHORIZED=0`. Only do this if you trust the services/load-balancers directing traffic to your service.

##### Failed to commit changes: Invalid 'prisma.user.create()'

Certain versions may have trouble creating a user if the field `metadata` is empty. Using an empty json object `{}` as the field value should resolve this issue.

##### CLIENT_FETCH_ERROR

Configure `NEXTAUTH_URL=http://localhost:3000/api/auth` to help the backend loop back to itself.

## Integrations

### Obtaining Google API Credentials

1. Open Google API Console. Create a project if needed. Under Dashboard pane, select Enable APIs and Services.
2. Search for "calendar" and select the Google Calendar API.
3. Enable the selected API.
4. Go to the OAuth consent screen. Select the app type and enter basic app details.
5. On Scopes page, add scopes: `.../auth/calendar.events` and `.../auth/calendar.readonly`.
6. Add test users on the Test Users page.
7. Select Credentials, then Create Credentials > OAuth Client ID.
8. Select Web Application as the Application Type.
9. Under Authorized redirect URIs, add:
   - `<App URL>/api/integrations/googlecalendar/callback`
   - `<App URL>/api/auth/callback/google`
10. Download JSON and copy contents to `.env` as `GOOGLE_API_CREDENTIALS`.

#### Adding Google Calendar to App Store

After adding credentials, repopulate the App store:

```
cd packages/prisma
yarn seed-app-store
```

Then:
1. Add extra redirect URL `<App URL>/api/auth/callback/google`
2. Under 'OAuth consent screen', click "PUBLISH APP"

### Obtaining Microsoft Graph Client ID and Secret

1. Open Azure App Registration and select New registration
2. Name your application
3. Set **Who can use this application** to **Accounts in any organizational directory (Multitenant)**
4. Set the **Web** redirect URI to `<App URL>/api/integrations/office365calendar/callback`
5. Use **Application (client) ID** as `MS_GRAPH_CLIENT_ID` in .env
6. Click **Certificates & secrets**, create a new client secret and use as `MS_GRAPH_CLIENT_SECRET`

### Obtaining Zoom Client ID and Secret

1. Open Zoom Marketplace and sign in.
2. Click "Develop" => "Build App".
3. Select "General App", click "Create".
4. Name your App.
5. Choose "User-managed app".
6. Copy Client ID and Client Secret to `.env` (`ZOOM_CLIENT_ID` and `ZOOM_CLIENT_SECRET`).
7. Set "OAuth Redirect URL" as `<App URL>/api/integrations/zoomvideo/callback`.
8. Add the redirect URL as an allow list URL.
9. Add Scopes:
   - Meeting: `meeting:write:meeting`
   - User: `user:read:settings`
10. Click "Done".

### Obtaining Video Conferencing API Credentials

1. Contact the video provider partnership team
2. From your dashboard, go to the developers tab
3. Copy your API key to `.env` as `DAILY_API_KEY`
4. If you have a Scale Plan, set `DAILY_SCALE_PLAN=true` for features like video recording

### Obtaining HubSpot Client ID and Secret

1. Open HubSpot Developer and sign in or create an account.
2. Go to "Manage apps".
3. Click "Create legacy app" and select public app.
4. Fill in app information.
5. Go to "Auth" tab.
6. Copy Client ID and Client Secret to `.env` (`HUBSPOT_CLIENT_ID` and `HUBSPOT_CLIENT_SECRET`).
7. Set Redirect URL to `<App URL>/api/integrations/hubspot/callback`.
8. In "Scopes", select "Read" and "Write" for `crm.objects.contacts` and `crm.lists`.
9. Click "Save".

### Obtaining ZohoCRM Client ID and Secret

1. Open Zoho API Console and sign in or create an account.
2. Go to "Applications".
3. Click "ADD CLIENT" and select "Server-based Applications".
4. Fill in client details.
5. Go to "Client Secret" tab.
6. Copy Client ID and Client Secret to `.env` (`ZOHOCRM_CLIENT_ID` and `ZOHOCRM_CLIENT_SECRET`).
7. Set Redirect URL to `<App URL>/api/integrations/zohocrm/callback`.
8. In "Settings", check "Multi-DC" if using same credentials for all data centers.
9. Click "Save".

### Rate Limiting

This application supports optional rate limiting. To enable:

1. Sign up for a rate limiting service
2. Create a Root key with permissions for `ratelimit.create_namespace` and `ratelimit.limit`
3. Copy the root key to `.env` as `UNKEY_ROOT_KEY`

Note: The application works normally without rate limiting if not configured.

## Workflows

### Setting Up Email Reminders

1. Create an account with an email service provider
2. Create an API key
3. Copy API key to `.env` as `SENDGRID_API_KEY`
4. Verify a sender email address
5. Copy verified email to `.env` as `SENDGRID_EMAIL`
6. Add custom sender name to `.env` as `NEXT_PUBLIC_SENDGRID_SENDER_NAME`

### Setting Up SMS Reminders

1. Create an account with an SMS provider (e.g., Twilio)
2. Get a phone number
3. Copy Account SID to `.env` as `TWILIO_SID`
4. Copy Auth Token to `.env` as `TWILIO_TOKEN`
5. Copy phone number to `.env` as `TWILIO_PHONE_NUMBER`
6. Add sender ID to `.env` as `NEXT_PUBLIC_SENDER_ID`
7. Create a messaging service
8. Copy Messaging Service SID to `.env` as `TWILIO_MESSAGING_SID`
9. Create a verify service
10. Copy Verify Service SID to `.env` as `TWILIO_VERIFY_SID`

## Enabling Content Security Policy

Set `CSP_POLICY="non-strict"` env variable to enable Strict CSP except for unsafe-inline in style-src.

## Single Org Mode

Refer to the documentation for detailed setup with screenshots.

## License

This project uses a dual licensing model:

- **Open Source (AGPLv3)**: The core technology is fully open source. This license requires you to provide source code to users who interact with the software over a network.

- **Enterprise Edition**: Additional features for larger organizations requiring enterprise capabilities are covered under a commercial license.

| Feature                             | AGPLv3 | Enterprise |
| ----------------------------------- | ------ | ---------- |
| Self-host for commercial purposes   | ✅     | ✅         |
| Clone privately                     | ✅     | ✅         |
| Fork publicly                       | ✅     | ✅         |
| Requires CLA                        | ✅     | ✅         |
| Official Support                    | ❌     | ✅         |
| Derivative work privately           | ❌     | ✅         |
| SSO                                 | ❌     | ✅         |
| Admin Panel                         | ❌     | ✅         |
| Impersonation                       | ❌     | ✅         |
| Managed Event Types                 | ❌     | ✅         |
| Organizations                       | ❌     | ✅         |
| Payments                            | ❌     | ✅         |
| Platform                            | ❌     | ✅         |
| Teams                               | ❌     | ✅         |
| Video                               | ❌     | ✅         |
| Workflows                           | ❌     | ✅         |

## Contributing

Please see our contributing guide for details on how to contribute to this project.

### Good First Issues

We maintain a list of "help wanted" issues that contain small features and bugs with limited scope. This is a great place to get started and gain experience with the codebase.

### Translations

Don't code but still want to contribute? Join our discussions and help translate the application into your language.

## Acknowledgements

Special thanks to these amazing projects which help power this application:

- Next.js
- Day.js
- Tailwind CSS
- Prisma

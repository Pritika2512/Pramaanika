# MeasureSure — Frontend Teammate 1

React + Vite + Tailwind CSS + React Router + Lucide React + Recharts.

## Your scope

Only these eight routes are implemented:

| Route                 | Screen                                         | Access           |
| --------------------- | ---------------------------------------------- | ---------------- |
| /login                | Sign in                                        | Everyone         |
| /register             | Create inspector account                       | Everyone         |
| /dashboard            | Verification Hub                               | Inspector, Admin |
| /instruments          | Search, filter, sort, paginate and edit        | Inspector, Admin |
| /instruments/register | Instrument registration                        | Inspector, Admin |
| /instruments/:id      | Instrument details                             | Inspector, Admin |
| /admin                | Administration overview                        | Admin            |
| /admin/users          | Add, view, edit, activate and deactivate users | Admin            |

Inspection, certificate, QR verification and blockchain modules from the previous version have been removed. The previous source remains recoverable in Git history.

## Run locally

Use a current Node.js version compatible with Vite 8 (Node 22.12+ recommended).

```bash
npm install
npm run dev
```

Other commands:

```bash
npm run lint
npm run build
npm run preview
```

Open the local URL printed by Vite. All demo features work without an external backend.

## Demo accounts

| Role          | Email                 | Password    |
| ------------- | --------------------- | ----------- |
| Administrator | admin@example.com     | password123 |
| Inspector     | inspector@example.com | password123 |

The login page also has role buttons that fill these credentials.

Create-account registration always creates an inspector. Only an administrator can assign roles in User Management. Role guards exist at the route and mock-service level; this is a UI demonstration, not real security.

## Where to make changes

| What you want to change                                              | File                                |
| -------------------------------------------------------------------- | ----------------------------------- |
| App name, initials, logo, organization, locale, timezone, page size  | src/config/appConfig.js             |
| Primary color, sidebar color, page background, radius, sidebar width | src/config/themeConfig.js           |
| Sidebar items, URLs, page titles and role visibility                 | src/config/navigationConfig.js      |
| Verification Hub cards and chart colors                              | src/config/dashboardConfig.js       |
| Instrument types, fields, sections, filters and table columns        | src/config/instrumentConfig.js      |
| Roles, account statuses, user fields, filters and columns            | src/config/userConfig.js            |
| Badge labels and semantic styles                                     | src/config/statusConfig.js          |
| Sample user names, emails, credentials                               | src/data/mockUsers.js               |
| Sample instruments and owners                                        | src/data/mockInstruments.js         |
| Sample activity                                                      | src/data/mockActivities.js          |
| Verification Hub reporting months                                    | src/data/mockDashboard.js           |
| Shared table, sorting and pagination                                 | src/components/common/DataTable.jsx |
| Input validation and password strength                               | src/utils/validation.js             |
| Common form rendering                                                | src/components/common/Forms.jsx     |
| Overall CSS layout and component styling                             | src/index.css                       |
| Page routing and access guards                                       | src/routes/routes.jsx               |

### Example: change the app name

Edit `appConfig.name`. The sidebar, login branding, footer and browser tab titles use the same value.

For a custom logo, set `appConfig.logo.imageUrl` to an image placed in `public/`, for example `/my-logo.png`. Otherwise choose an icon name from `components/common/Icon.jsx`.

### Example: change the primary color

Change `themeConfig.primary`, `primaryDark` and `primarySoft`. They become CSS variables, and the buttons, links, charts and focus outlines use them.

### Example: add a table column

Add `{ key: 'model', label: 'Model' }` to `instrumentColumns`. No page markup change is required.

### Example: add a form field

Add an object to a section's `fields` array. The form, live validation, details screen and instrument update service use that configuration. Add its values to mock records if you want existing samples to display them.

Supported field types: text, email, tel, password, select and textarea. Supported validation: required, minLength, select options and matches.

### Example: add a Verification Hub card

Add an object to `dashboardStats`, referencing an existing statistic by `id`. For a new calculated value, add that calculation to `dashboardService.getDashboardStats()`.

## How the code fits together

```text
Page -> service function -> in-memory mock store
                       -> response -> page state -> feedback
```

Pages never edit mock arrays. Services own validation and mutations. Shared forms call the provided submit handler; they handle busy state and show errors. The same configurable DataTable serves instruments, users and activity.

## Demo data lifetime

Run the service regression checks with `node --test tests/services.test.mjs`.
They cover login, permissions, duplicates, record changes and Verification Hub totals
without extra dependencies.

Data mutations live in memory. They persist during navigation and sign-out/sign-in in the same loaded page, and reset on a full browser reload. Different tabs and visitors do not share records. Session storage remembers only the current mock user ID.

A newly registered account can be used immediately; reloading resets it along with other newly created records. Change files under `src/data/` to make permanent demo seed changes.

This intentional behavior keeps the frontend independent of a database. Do not enter real passwords or personal data into the demo.

## Connecting your teammate's backend later

Replace implementations in:

- services/authService.js
- services/instrumentService.js
- services/userService.js
- services/dashboardService.js

Keep the exported function names, argument shapes and returned record fields. Resolve with data or throw an Error with a user-friendly message. Do not add API requests inside pages.

The backend must enforce authentication and roles. Current route guards and session storage are only for the mock interface.

## Hosting

The existing public Site is retained. `scripts/create-worker.mjs` packages the built static frontend into a hosting adapter. It only serves files and SPA routes; it has no business API, authentication, database or data mutations.

Git history preserves the earlier full-scope application. This checkout contains only the new teammate scope.

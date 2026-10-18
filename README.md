# Wild Island: Landing page · Cloudflare (protected)

A one-page landing site on **Cloudflare**, with preview links **behind Cloudflare Access**. Static Vite +
TypeScript, no framework. The page reads a live list from the shared Supabase test project.
Row in the support matrix: impeccable-reviews#302.

## Deployment links

| Production | Preview gate |
|---|---|
| https://wild-island-4gze.pages.dev | yes: Cloudflare Access on `*.wild-island-4gze.pages.dev` |

## Names

Every prepared repository gets a random name of its own, never reused, for example
`amber-falcon-7k2q`. In a monorepo, each of the three apps also gets a random name (folder, package
name and page title). App names must not repeat across repositories: when `todo-monorepo-2` reused
`todo-monorepo`'s app names, the dashboard opened the old repository's apps.

## Prepare it for an eval

1. Copy (don't move) this folder to `tmp/eval-repos/<name>/`, `<name>` being the new random name.
2. Replace `template-landing` (package name) and `Template Landing` (page title) everywhere. Run `npm install` so `package-lock.json` has the new name.
3. `npm run build` passes, then `git init -b main`, commit, and push to a new repository
   `pristine-test-org/<name>` (`gh repo create`; push with the token on the URL). Then add a plain `origin`,
   `git remote add origin https://github.com/pristine-test-org/<name>.git`, with no token in it: the
   command quits without one.
4. Create a Git-connected Cloudflare Pages project named `<name>` with the `cf` CLI (not Wrangler):
   `cf pages create` with a GitHub source for `pristine-test-org/<name>`, production branch `main`,
   build command `npm run build`, output directory `dist`, and preview deployments for all
   branches. Use Abdul's **personal** Cloudflare account, not Renaissance Geek: inside `tmp/eval-repos/` the `cf` profile `personal` is bound, so check with `cf auth whoami` first. Find the exact flags with `cf cli search` and `cf pages create --help`. A
   `cf pages deploy` upload alone has no Git source and builds no PR previews.
5. Put Cloudflare Access in front of the previews: `cf zero-trust access applications create` for `*.<name>.pages.dev`, with an Allow policy for `abdul@renaissance-geek.ai`. This goes on Abdul's personal account, through the `cf` profile bound to `tmp/eval-repos/`. Check a preview URL asks for the Access login in a private window.
6. Push a commit to `main` and wait for the production build. `.node-version` pins
   Node 22 for the build.
7. Write the production URL into **Deployment links** above, delete the template
   notice at the top, and push that to `main`.

## Supabase

Every page reads the latest five rows of the `todos` table in the shared test project
(`https://hszqtfynyogshhltamep.supabase.co`), the same one the todo monorepos use. The URL and publishable key are
defaults in `src/supabase.ts`, so a host needs no environment variables.

**When a new deployment or preview URL comes up**, check Supabase lets it in:

- **Data (CORS):** nothing to add. Supabase's Data API answers any origin; on 2026-10-19 a
  request from a made-up preview origin came back allowed. Open the deployment and check the
  "Live from the shared list" strip shows rows, not "Couldn't reach the list".
- **Auth URLs:** the only per-URL setting Supabase has is Auth → URL Configuration (Site URL and
  Redirect URLs). It matters only for an app that signs people in through Supabase Auth with a
  redirect. These pages don't, so skip it. If an app ever does, add its preview URL pattern to
  Redirect URLs with the Supabase CLI (`supabase link --project-ref hszqtfynyogshhltamep`, then
  `supabase config push`). Careful: `config push` writes the shared project's whole auth config.

## Run the eval

A repo eval, not `npm run eval`: the steps are in `REPO-EVAL.md` beside the templates. In short:
open the repo's app on impeccable.reviews, wait for the scan (Rescan if apps are missing), click
Setup, run the command it gives in `tmp/eval-repos/<name>/`, and wait for Impeccable's review on
the test PR.

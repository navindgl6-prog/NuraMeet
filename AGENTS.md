# Nura Meet: project context for the coding agent

You are helping build **Nura Meet**, an official meeting-notes app for Nura Analytics
(offices: Chennai, India and Singapore). Nura Meet is a branded fork of the open-source
**Anarlog** app (https://github.com/fastrepl/anarlog).

## About the person you're working with
Raj Navin works on marketing, GTM and product design at Nura. He is NOT technical.
- Explain things in plain words. Never assume he knows terminal commands, Git or cloud consoles.
- Before any major decision (database, hosting, libraries, anything hard to undo), give 2-3
  options with pros, cons and YOUR recommendation, then wait for his choice.
- Give exact click-by-click or copy-paste steps whenever he has to do something himself.
- Never ask him to paste secrets (passwords, service_role keys) into chat.

## Product goal
1. Staff install a desktop app (Windows and Mac) and use it in meetings. No bot joins
   the call; audio is captured on the device.
2. Everything stays local: notes, transcripts and settings live only on the user's own
   computer. **No cloud, no server, no accounts.** (Raj changed direction on
   23 Nov 2026 — the earlier Supabase sign-in / Nura sync / admin dashboard plans are
   cancelled.)
3. Each person types their name once when they first open the app (a "simple local
   profile" stored on their machine). Notes show who wrote them.
Scale: about 5-6 regular users, at most ~30. Meeting volume is low.

## Decisions already made (do not re-open without a good reason)
- Build our own fork; not buying Anarlog Pro/Enterprise.
- **No cloud functionality of any kind.** No Supabase, no sign-in, no Nura sync, no
  admin dashboard. The app is an installable local-only desktop app.
- Name: **Nura Meet**. Logo and icons are in `branding/` (red hexagon mark from Nura One).
- Accounts = **simple local profile**: each person enters their name once on first
  launch; it is stored only on their computer (the app's local SQLite `humans` table)
  and appears as the author of their notes. Reuses the existing
  `usePersonalContact` / `savePersonalContact` local profile system — no new storage.
- The unfinished Nura-sync cloud work is shelved on branch `wip/nura-sync-cloud`
  (shelved commit "wip: shelve unfinished nura-sync cloud upload (no-cloud decision)").
  Do not delete it; it can be revived if Nura ever wants cloud features back.

## Licensing facts (verified from the repo)
- Everything outside `enterprise/` is MIT. We may fork, rebrand and use it. Keep the MIT
  license text and copyright notices.
- `enterprise/**` is under a commercial license. DO NOT copy, reuse or depend on anything
  in `enterprise/`. Build our own admin features from scratch.
- Do not add GPL/AGPL/unknown-license dependencies without telling Raj.

## What we learned about the codebase
- Monorepo (pnpm + Rust). Desktop app: `apps/desktop` = Tauri v2, React + TypeScript UI,
  Rust backend. Needs Node 22+, pnpm 11.1.1, Rust 1.94.0 (see README "Local development").
- Config files with branding: `apps/desktop/src-tauri/tauri.conf*.json`
  (productName, mainBinaryName, identifier "com.hyprnote.*", publisher "Fastrepl", icon paths).
  Icons live in `apps/desktop/src-tauri/icons/{dev,stable,staging,...}`.
- Supabase is deeply wired in (~250 files): `apps/desktop` (uses @supabase/supabase-js),
  `packages/supabase`, `plugins/auth`, `crates/supabase-auth`, `crates/api-sync`,
  `crates/supabase-storage`, `apps/api`, `apps/stripe`, billing, hosted AI, Nango integrations.
  All of it is **inert in our builds** because no Supabase credentials are bundled
  (`src/auth/client.ts` creates a client only when `VITE_SUPABASE_URL` and
  `VITE_SUPABASE_ANON_KEY` exist). Leave it that way; do not wire cloud features back
  without Raj explicitly reversing the no-cloud decision.
- The previous sandbox could NOT compile the app (no Rust toolchain). Builds must be
  verified by GitHub Actions or on a dev machine. Be honest about what was and wasn't tested.
- Anarlog has a local API, CLI, MCP server and webhooks that may help with sync.

## Deliverables (in order; stop for Raj's OK between phases)
1. **Fork + rebrand.** (DONE) Rename to Nura Meet (product name, binary, identifier
   `com.nuraanalytics.meet`, publisher "Nura Analytics", window titles, about screen,
   links), replace icons with `branding/`, remove or hide Anarlog/Fastrepl promos,
   upsells, update checks pointing at Fastrepl, and telemetry that sends data to
   Fastrepl or third parties.
2. **Local profile.** (DONE, phase 6) Replace the cloud sign-in step with a "Your name"
   form on first launch. The name is stored only on the user's computer and appears as
   the author of their notes. Remove the sign-in dialog/form and the cloud account
   page in Settings; keep the underlying (inert) cloud code in the tree.
3. **No cloud.** (DONE) No Supabase credentials are bundled, nothing is uploaded. The
   cloud "Share" button is hidden like the other upstream surfaces. If staff ever see a
   cloud feature, it is inert or unreachable.
4. **Build pipeline.** A GitHub Actions workflow (`nura-build.yaml`) that builds Windows
   (.exe) and macOS (.dmg, Apple Silicon) installers and attaches them to a GitHub
   Release. Unsigned is acceptable for the pilot; explain the Windows SmartScreen /
   macOS Gatekeeper warnings and how to proceed.
5. **SETUP-GUIDE.md** for Raj: exact steps to go live (update it to match what was
   actually built — the Supabase sections must be removed since there is no cloud).

## Security rules
- The app is local-only: do not add any network upload of meeting content, and do not
  bundle any credentials or keys. If a cloud feature is ever revived, only the Supabase
  URL and the public anon/publishable key may appear in client code; NEVER put the
  `service_role` key anywhere in the app, dashboard, repo, or chat.
- Do not collect or transmit anything beyond what the product goal lists.

## Quality bar
- Prefer small, reviewable commits. Keep a CHANGELOG-style note of what changed vs upstream.
- State plainly what you tested and what you could not test.
- When something is a policy/legal question (recording consent in India and Singapore,
  data protection), flag it for Raj to confirm with Nura's legal contact; do not decide it.

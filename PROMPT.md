# Paste this into OpenCode as your first message

(Open OpenCode in the project folder first. It reads AGENTS.md in the folder automatically,
which holds the full background.)

----- COPY FROM HERE -----

Read AGENTS.md in this folder fully before doing anything. We are building Nura Meet,
a branded fork of the open-source Anarlog app, for Nura Analytics.

Fill-ins from me:
- Company email domain for sign-in: {{EMAIL_DOMAIN}}
- Staff sign in with: {{GOOGLE_OR_MICROSOFT}}
- My Supabase project URL: {{SUPABASE_URL}}   (public key I'll add as an env var, not in chat)

Start like this:
1. Clone https://github.com/fastrepl/anarlog into this folder's subfolder "app" (a shallow
   clone is fine). Do not copy or use anything inside its enterprise/ folder.
2. Confirm you can see branding/ and supabase/01_schema.sql here.
3. Investigate the repo and give me a short plain-English plan for Phase 1 (fork and
   rebrand) and Phase 2 (sign-in): which files change, what you will switch off, and any
   decision where you need my input. Give options with pros, cons and a recommendation.
4. Wait for my OK before making changes.

I'm not technical, so explain things simply and tell me exactly what to click or run
whenever I need to do something. Never ask me to paste secret keys into chat.

----- COPY TO HERE -----

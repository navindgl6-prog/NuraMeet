# Nura Meet: step-by-step guide to go live

Written for a non-technical person. Some details (marked **[final wording after build]**)
will be confirmed by the coding agent once the app is built. Do the parts in order.

## Part 1. Accounts you need (all have free options to start)
1. **Supabase** (database and login): supabase.com. Make a project called `nura-notetaker`
   in the Singapore or Mumbai region. Save the database password in a password manager.
2. **GitHub** (stores the code and builds the installers): github.com. Create a **private**
   repository named `nura-meet`. Ask IT to own the account if possible.
3. **A place to host the admin dashboard**: Vercel, Cloudflare Pages or Netlify. Any of them
   has a free plan that is enough for 30 people. The agent will recommend one.
4. **Your staff sign-in provider** (Part 3): Google Cloud (Google Workspace) or
   Microsoft Entra (Microsoft 365). Someone who administers company email must help.

## Part 2. Set up the database (about 10 minutes)
1. Supabase project, then **SQL Editor**, then **New query**.
2. Open `supabase/01_schema.sql`. Find the line starting `-- insert into public.allowed_email_domains`,
   delete the leading `--`, and replace `YOUR-COMPANY-DOMAIN.com` with your staff email domain.
3. Paste the whole file into the editor and click **Run**. You should see "Success".
4. Check: **Table Editor** should list `profiles`, `meetings`, `admin_view_log`,
   `allowed_email_domains`.
5. Collect two values from **Project Settings, then API** (menu names may differ slightly):
   - **Project URL**
   - **anon / publishable key** (safe to put in the app)
   Do NOT use or share the `service_role` key. It is a master key.

## Part 3. Turn on staff sign-in
**If staff use Google Workspace**
1. Google Cloud Console, create a project, then **APIs & Services, then OAuth consent screen**:
   choose **Internal** (limits sign-in to your company).
2. **Credentials, then Create credentials, then OAuth client ID, then Web application**.
3. In Supabase, **Authentication, then Providers, then Google**: copy the **Callback URL**
   shown there into Google's "Authorized redirect URIs".
4. Paste Google's Client ID and Client Secret into Supabase and save.

**If staff use Microsoft 365**
1. Microsoft Entra admin center, then **App registrations, then New registration**
   (supported account type: this organization only).
2. Add the **Callback URL** from Supabase (**Authentication, then Providers, then Azure**) as a
   Web redirect URI.
3. Create a **client secret**, then paste the Application (client) ID, the secret and your
   tenant URL into Supabase and save.

Ask IT to do this part if you can. It needs company-admin permission.
**[final wording after build]** Also add the app's redirect addresses under
**Authentication, then URL Configuration**.

## Part 4. Build the installers (GitHub does the work)
1. Put the project code into your private `nura-meet` repository (the agent will tell you
   exactly how, or do it with you).
2. In GitHub: **Settings, then Secrets and variables, then Actions**: add the Supabase URL
   and anon key the agent names. Never add the service_role key.
3. **Actions tab, then "Build Nura Meet", then Run workflow**. It takes roughly 30-90 minutes
   the first time.
4. When it finishes, open **Releases** and download the Windows installer (`.exe` or `.msi`)
   and the Mac installer (`.dmg`).

## Part 5. Put the admin dashboard online
1. In your hosting service, choose **Import from GitHub** and pick the repository.
2. Set the two environment variables (Supabase URL and anon key) as the agent tells you.
3. Deploy. You get a web address like `https://something.vercel.app`.
4. Add that address in Supabase, **Authentication, then URL Configuration**, as the Site URL
   and an allowed redirect.
5. Optional: ask IT for a friendly address such as `meet-admin.yourcompany.com`.

## Part 6. Make yourself the first admin
1. Install Nura Meet (or open the dashboard) and sign in once with your work account.
2. In Supabase **SQL Editor**, run this with your own email:
   `update public.profiles set role = 'admin' where email = 'you@yourcompany.com';`
3. Sign out and back in. You should now see all meetings in the dashboard.

## Part 7. Install on staff laptops
- **Windows:** the first launch may show a blue "Windows protected your PC" box because the
  app isn't code-signed yet. Click **More info, then Run anyway**.
- **Mac:** if blocked, right-click the app, choose **Open**, then **Open** again.
- Signing the app removes these warnings. It costs money and some paperwork (an Apple
  developer account for Mac, a certificate for Windows). Worth doing before a wider rollout.

## Part 8. Pilot checklist (week 1, 3-5 people)
- [ ] Each person signs in and records a short test meeting.
- [ ] The meeting appears in the dashboard within a minute of ending.
- [ ] A normal member **cannot** see another member's meeting. Test this on purpose.
- [ ] An admin can open a meeting, and the owner can see that it was viewed.
- [ ] Tell staff clearly that summaries and transcripts are shared with Nura admins.
- [ ] Check recording-consent and data-protection rules for India and Singapore with Nura's
      legal contact, especially for client calls and anyone outside Nura on the call.

## Part 9. Before real use
- Upgrade Supabase to **Pro** (about $25/month): daily backups, no pausing.
- If the free project ever pauses, open the Supabase dashboard and click **Restore**.
- Decide who the admins are, and keep the list short.
- Plan how updates reach staff (reinstall the new version for now).

## Keep safe
- Never share the Supabase **database password** or **service_role key** in chat or email.
- If a secret leaks, rotate it in Supabase (Project Settings) and tell the agent.

# The Party Ledger

Live D&D 5e character sheets and inventories. Players log in and edit their own sheets. The DM sees the whole party update in real time, can adjust HP, hand out loot and gold, and keeps private notes players cannot see.

The site is plain static files (hosted free on GitHub Pages). Supabase (free tier) provides the logins and the database. Nothing here needs a server you run yourself.

## Try it first (no setup)

Open `index.html` in a browser. With `config.js` unfilled it runs in **demo mode**, storing everything in that browser only. Tick "I am the DM" when you create the first account. Open a second tab to be a player and watch the DM view update.

## Go live (about 20 to 30 minutes, once)

1. **Make a Supabase project.** Sign up at supabase.com, click New project, pick any name and a database password, and wait for it to finish setting up.
2. **Run the setup script.** In the project, open SQL Editor, click New query, paste the whole of `setup.sql`, and press Run. It should say it succeeded. This creates the tables and the access rules (players only reach their own sheet; only the DM reaches everything).
3. **Turn off email confirmation.** Authentication, then Sign In / Providers (or Providers), then Email, then switch **Confirm email** off and save. Otherwise every player has to click a link in an email before they can log in.
4. **Copy your keys.** Project Settings, then API (or Data API). Copy the **Project URL** and the **anon public** key into `config.js`. The anon key is meant to be public; the access rules are what protect the data. Never put the `service_role` key anywhere in these files.
5. **Put it on GitHub.** Make a new repository, upload `index.html`, `config.js` and `README.md` (the SQL file is optional), then Settings, then Pages, and set it to deploy from the main branch. GitHub shows your site address after a minute or two.
6. **Create your account, then make yourself the DM.** Open the site, choose Create account, and sign up. Then in Supabase's SQL Editor run this, with your email:

   ```sql
   update public.profiles set role = 'dm'
   where id = (select id from auth.users where email = 'you@example.com');
   ```

   Reload the site. You should see the badge change to DM and the party dashboard.
7. **Invite your players.** Send them the site link. They create accounts and characters themselves.
8. **Lock the door once everyone has joined (recommended).** Authentication, then Sign In / Providers, then turn off "Allow new users to sign up". You can switch it back on whenever someone new joins.

## Notes

- **Do not put your world bible in this repository.** GitHub Pages sites are public. Keep the bible on your own computer.
- **Roles can't be changed from the app.** A player cannot make themselves the DM; only the SQL in step 6 can.
- **Simultaneous edits.** Each sheet saves whole, last write wins. A player and the DM changing the same sheet in the same half second can overwrite each other. In practice, HP buttons and loot grants are quick enough that this rarely matters, and the app shows a "Load latest" bar if a sheet changes while a player has unsaved edits.
- **Forgot password:** use Supabase, Authentication, Users, to reset or remove an account.
- **Free tier:** a handful of players is far below the limits. Supabase pauses free projects after a week without activity; opening the dashboard and pressing Restore brings it back.

## Files

- `index.html` is the whole app.
- `config.js` holds your two Supabase values.
- `setup.sql` is run once in Supabase.

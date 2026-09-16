# Clerk identity remap for the production cutover

**Status:** prepared, not applied. Blocks the `findmoy.app` cutover.

## Why this is needed

Live production has always run on the Clerk **development** instance
(`pk_test_Y2VudHJhbC1jYXQtOTQ3Mi5jbGVyay5hY2NvdW50cy5kZXYk`). Every
`identity` row with `provider = 'clerk'` in the production database therefore
holds a development-instance user ID.

Production now points at the Clerk **production** instance on
`clerk.findmoy.app`, which issues different user IDs for the same people. Left
alone, each of the seven owners below would sign in, match no `identity` row,
receive a brand-new `app_user`, and lose access to their workspace. The data
stays in the database but becomes unreachable by its owner.

Proof of provenance: `user_3Iua1z2z7M9IfR7zwglBZ9whQqK` appears both as a
production identity and as the user created by signing in to **staging**, which
uses the development instance.

## The seven accounts

Every one is the `owner` of their workspace. Emails read from the Clerk
development instance dashboard; IDs and workspace data from Neon
`findmoy-production`.

| # | development user ID | email | display name | workspace | platform admin |
|---|---------------------|-------|--------------|-----------|----------------|
| 1 | `user_3Iv40sGNxxbNj8MHjmS9I0EBSPx` | kcms@uberip.com | KCMS Demo | KCMS user | yes |
| 2 | `user_3Iua1z2z7M9IfR7zwglBZ9whQqK` | chhuonnara002@gmail.com | KCMS user | KCMS user | no |
| 3 | `user_3IwwgGnAYEEZxjvno8nKMbgOig7` | sopheakchan200021@gmail.com | KCMS user | KCMS user | no |
| 4 | `user_3J5azplMsTQI7fNZdWRNeBpXn7J` | taot70210@gmail.com | KCMS user | KCMS user | no |
| 5 | `user_3IukEfmgDdWlWIJ0FcVJSh3PjLZ` | layheangrin@gmail.com | heang | KCMS user | no |
| 6 | `user_3J68Wf0XdLrOIqVAzTJ5BUxMWJJ` | kcms01@uberip.com | kcms01 | kcms01 | yes |
| 7 | `user_3JJjjCUlXYVrOipLWESlqzEHXjg` | yuneychhean@gmail.com | yuneychhean | yuneychhean | no |

`kcms-demo` / `kcms@gmail.com` exists in the development instance but has never
signed in and owns no production identity, so it needs no remap.

The 19 `provider = 'email'` identities are legacy email/password accounts and
are unaffected by the instance change.

## Procedure

1. Create all seven accounts in the Clerk **production** instance with the same
   email addresses, or have each person sign up once on `findmoy.app` before
   the remap.
2. Record each new production user ID against its email. Verify every address
   matches the table above exactly; a wrong pairing hands one person another
   person's workspace.
3. Apply one statement per account against Neon `findmoy-production`:

   ```sql
   UPDATE identity
      SET provider_id = '<new production user ID>'
    WHERE provider = 'clerk'
      AND provider_id = '<development user ID from the table>';
   ```

   Each must report `UPDATE 1`. `UPDATE 0` means the account was already
   remapped or the old ID is wrong — stop and recheck rather than re-running.

4. Confirm no development IDs remain:

   ```sql
   SELECT provider_id FROM identity
    WHERE provider = 'clerk'
      AND provider_id IN (
        'user_3Iv40sGNxxbNj8MHjmS9I0EBSPx','user_3Iua1z2z7M9IfR7zwglBZ9whQqK',
        'user_3IwwgGnAYEEZxjvno8nKMbgOig7','user_3J5azplMsTQI7fNZdWRNeBpXn7J',
        'user_3IukEfmgDdWlWIJ0FcVJSh3PjLZ','user_3J68Wf0XdLrOIqVAzTJ5BUxMWJJ',
        'user_3JJjjCUlXYVrOipLWESlqzEHXjg');
   ```

   Expect zero rows.

5. Have at least one non-admin owner sign in and confirm they see their existing
   workspace and comment history, not an empty new workspace.

## Verification counts

Before the remap, Neon `findmoy-production` holds 26 users, 19 workspaces, 109
comments, 4 page connections, 109 verdicts and 57 actions. These must be
unchanged afterwards: the remap rewrites `identity.provider_id` only.

## If it goes wrong

`identity` rows are small and the old IDs are recorded above, so the change is
reversible by running the same statements in the opposite direction. Take a Neon
branch before starting so there is a point-in-time copy to compare against.

## Platform administration

Not affected. `_sync_platform_admin` matches `PLATFORM_ADMIN_EMAILS` by email on
every sign-in, so it survives the instance change and needs no remap. Both
Workers are set to `chhuonnara002@gmail.com,kcms@uberip.com`, which grants rows
1 and 2 and revokes row 6 (`kcms01@uberip.com`) unless that address is added
back.

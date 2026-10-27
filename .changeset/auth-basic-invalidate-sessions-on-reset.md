---
"@aws-blocks/bb-auth-basic": patch
"@aws-blocks/blocks": patch
---

AuthBasic now invalidates every outstanding session when a user resets their
password. Previously `confirmResetPassword` only changed the credential, so a
token stolen before the reset kept authenticating until it expired — the reset
did not actually lock the attacker out. Each session JWT now carries the user's
current session epoch (an `ep` claim); `confirmResetPassword` bumps a per-user
epoch counter, and every auth check rejects a token whose epoch predates it
(reported as `SessionRevokedException`), so a password reset ends all prior
sessions at once rather than just the token that happens to sign out.

Tokens issued before this change have no epoch claim and are treated as the
lowest epoch, so the first reset invalidates them too. If the password is
changed but the session-invalidation write fails, `confirmResetPassword` throws
`SignOutIncompleteException` (503, retriable) rather than silently leaving the
old sessions alive. The umbrella `@aws-blocks/blocks` is bumped because the
bundled `bb-auth-basic` README/DESIGN docs it mirrors have changed.

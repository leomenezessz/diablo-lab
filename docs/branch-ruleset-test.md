# Branch ruleset CI test

This PR intentionally contains a whitespace error to verify that the `Validate static site` check fails and that the `main` ruleset prevents merging.

Expected: validation fails; merge is blocked.  

Remove the trailing spaces on the previous line after confirming the protection works.

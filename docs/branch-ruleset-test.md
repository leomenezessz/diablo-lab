# Branch ruleset CI test

This PR tests that the `Validate static site` check is required by the `main` branch ruleset.

The intentionally introduced trailing whitespace caused validation to fail and blocked merging.

The whitespace has now been removed. Expected: validation passes and merging becomes available.

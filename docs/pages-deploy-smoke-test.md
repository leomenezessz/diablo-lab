# GitHub Pages workflow smoke test

This documentation-only change tests the pull request validation and the deployment triggered by a merge into `main`.

Expected behavior:
- Pull request to `main`: run validation, skip deployment.
- Merge into `main`: run validation, then deploy GitHub Pages.

No website content or application behavior is changed.

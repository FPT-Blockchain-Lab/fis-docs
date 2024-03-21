**Quorum Middleware:**

1. How to update quorum middleware package?

- Fork repo and update scope in ci to publish to FIS npm.
- Update code in quorum middleware ([https://github.com/FPT-Blockchain-Lab/quorum-middleware](https://github.com/FPT-Blockchain-Lab/quorum-middleware)) and
  remember up version in file package.json of middleware when you want to up new version.
- When change done make Pull Request and merge into main branch.
- Release new tags and ci will auto publish new version quorum middleware package on npm.

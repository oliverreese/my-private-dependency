# my-private-dependency


###Wichtige Bestandteile:
1. ./github/workflows/npm-publish.yml
2. package.json
3. test/*


####1. npm-publish.yml

Die Datei ./github/workflows/npm-publish.yml enthält die Konfiguration für die Github Action beim Push auf den
Branch Master. Das Resultat ist eine neue Packageversion die im https://npm.pkg.github.com/ liegt.
NPM Yarn version vergibt eine neue Version und anschließend wird mit dieser ein Tag im Repository erzeugt.

Weitere Informationen:
<https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions>

```
name: publish Node.js Package

on:
  push:
    branches:
    - master

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - uses: actions/setup-node@v1
      with:
        node-version: 12
    - run: yarn install
    - run: yarn test

  publish-gpr:
    needs: build
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - uses: actions/setup-node@v1
      with:
        node-version: 12
        registry-url: https://npm.pkg.github.com/
        scope: '@oliverreese'
    - run: yarn install
    - run: git config --global user.email "$GITHUB_ACTOR@users.noreply.github.com" && git config --global user.name "$GITHUB_ACTOR"
    - run: yarn version --minor
    - run: git push --tags && git push
    - run: yarn publish
      env:
        NODE_AUTH_TOKEN: ${{secrets.GITHUB_TOKEN}}


```

###2. package.json

Wichtige Punkte hier der Name im Package.json

```
  "name": "@oliverreese/my-private-dependency",
```

Sowie der zusätzliche Eintrag:

```
  "publishConfig": {
    "registry": "https://npm.pkg.github.com/"
  }
  ```

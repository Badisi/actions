<h1 align="center">
    badisi/actions
</h1>

<p align="center">
    <i>😺 Reusable Github workflows mainly designed to be used for my own repositories</a></i>
</p>

<p align="center">
    <a href="https://github.com/badisi/actions/blob/main/CONTRIBUTING.md#-submitting-a-pull-request-pr">
        <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg" alt="PRs welcome" /></a>
    <a href="https://github.com/badisi/actions/blob/main/LICENSE">
        <img src="https://img.shields.io/badge/license-GPLv3-ff69b4.svg" alt="license GPLv3" /></a>
</p>

<hr/>

The following workflows are available:

#### - Setup

Runs the following steps in order:

```
1. Checkout sources
2. Setup chrome (optional)
3. Setup git
4. Setup node
5. Setup npm
6. Cache .angular and node_modules
7. Install npm dependencies
```

#### - Action

Runs the following steps in order and also apply a matrix strategy to run them on different types of machine and node versions:

```md
1. Run the setup workflow
2. Lint (optional)
3. Test (optional)
4. Build (optional)
5. Release dry-run (optional)
6. Release (optional)
```

## Usage

### [badisi/actions/setup](./setup/action.yml)

```yaml
jobs.<job_id>.steps[*]:
  - name: Setup
    uses: badisi/actions/setup@v1
    with:
      #
      # Node version to be used.
      #
      # @examples: 18, '12.x', '10.15.1', '>=10.15.0', 'lts/Hydrogen', '16-nightly', 'latest', 'node'
      # @default: 20
      #
      node-version: 20

      #
      # Node architecture to be used.
      #
      # @examples: x86, x64
      # @default: '', use system architecture by default
      #
      node-arch: ""

      #
      # Whether to install the latest Chrome version.
      #
      # @default: false
      #
      setup-chrome: false
```

### [badisi/actions/.github/workflows/action.yml](./.github/workflows/action.yml)

```yaml
jobs.<job_id>:
  uses: badisi/actions/.github/workflows/action.yml@v1
  with:
    #
    # The working directory of where to run the commands.
    #
    # @default: '.'
    #
    working-directory: "."

    #
    # Type(s) of machine to run the job on.
    #
    # @see: https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#choosing-github-hosted-runners
    # @default: '["ubuntu-latest"]'
    #
    runs-on: '["ubuntu-latest"]'

    #
    # Node version(s) to be used.
    #
    # @examples: '[18, 20]', '[12.x]', '[10.15.1]', '[>=10.15.0]', '[lts/Hydrogen]', '[16-nightly]', '[latest]', '[node]'
    # @default: '[20]'
    #
    node-versions: "[20]"

    #
    # Node architecture to be used.
    #
    # @examples: x86, x64
    # @default: '', use system architecture by default
    #
    node-arch: ""

    #
    # Whether to install the latest Chrome version.
    #
    # @default: false
    #
    setup-chrome: false

    #
    # Whether to run the command `npm run lint`.
    #
    # @default: false
    #
    lint: false

    #
    # Whether to run the command `npm run test:ci`.
    #
    # @default: false
    #
    test: false

    #
    # Whether to run the command `npm run build`.
    #
    # @default: false
    #
    build: false

    #
    # Whether to run the command `npm run release`.
    # If required, tokens such as NPM_TOKEN and GH_TOKEN can be passed as secrets.
    #
    # @default: false
    #
    release: false

    #
    # Whether to run the command `npm run release:dry-run`.
    # If required, tokens such as NPM_TOKEN and GH_TOKEN can be passed as secrets.
    #
    # @default: false
    #
    dry-run: false
```

## Examples

1. Setup everything on a project and runs e2e tests.

   ```yaml
   jobs:
     ci_e2e:
       runs-on: ubuntu-latest
       steps:
         - name: Setup
           uses: badisi/actions/setup@v1
           with:
             - setup-chrome: true
         - name: Run e2e tests
           run: ...
   ```

2. Runs `lint` and `test` jobs on a desired project, with specific `platforms` and `node versions`.

   ```yaml
   jobs:
     ci_tests:
       uses: badisi/actions/.github/workflows/action.yml@v1
       with:
         working-directory: projects/package-a
         runs-on: '["ubuntu-latest", "macos-latest", "windows-latest"]'
         node-versions: "[18, 20]"
         lint: true
         test: true
   ```

3. Runs `lint`, `test`, `build` and `release` jobs on a desired project.

   ```yaml
   jobs:
     ci_release:
       uses: badisi/actions/.github/workflows/action.yml@v1
       secrets:
         GIT_USER_NAME: "badisi"
         GIT_USER_EMAIL: "badisi@users.noreply.github.com"
         GH_TOKEN: ${{ secrets.YOUR_GITHUB_TOKEN }}
         NPM_TOKEN: ${{ secrets.YOUR_NPM_TOKEN }}
       with:
         working-directory: projects/package-a
         lint: true
         test: true
         build: true
         release: true
   ```

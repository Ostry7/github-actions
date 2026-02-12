# github-actions

## Task 1: Basic CI Workflow [v]

Create a GitHub Actions workflow that:
- Runs on every `push` to the `main` branch
- Prints the following information to the workflow logs:
  - Repository name
  - Branch name
  - Commit SHA

### TIP: 

There are two ways to get branch name (depends on event type):
- GITHUB_REF_NAME -> is used for push
- GITHUB_HEAD_NAME -> is used for pull


---


## Task 2: Matrix Build [v]

Create a workflow that:
- Runs on `pull_request` and `push`
- Uses a matrix strategy to test the application on:
  - Ubuntu
  - Windows
- Tests at least two different Node.js versions

---

Matrix strategy allows you to run the same job multiple times with different parameters (in our Task2 -> OS and Nodejs version) _in parallel_. 

Basic syntax:

```yaml
jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [18, 20]
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - name: Print info
        run: |
          node --version
          echo "OS: $RUNNER_OS"
```
### Output for above matrix:
1. ubuntu + Node 18.
2. ubuntu + Node 20.
3. windows + Node 18.
4. windows + Node 20.

_Key points:_
- each combination of matrix values runs as a separate job
- you can use `include` and `exclude` to customize combinations
- usefull for testing _cross-platform builds_, multiple Node/Python versions or different environments
- Matrix variables are accessed with `${{ matrix.VARIABLE_NAME}}

---

## Task 3: Conditional Steps [v]

Create a workflow where:
- One step runs **only if** the commit message contains `[skip-tests]`
- Another step runs **only if** the workflow was triggered manually (`workflow_dispatch`)

---

### Workflow description:
- A job runs only on push when the commit message contains skip tests
- A job runs only on manual trigger (workflow_dispatch)
- Event type is always checked before accessing event-specific fields

---

## Task 4: Dependency Caching [v]

Create a CI workflow that:
- Installs project dependencies
- Uses GitHub Actions cache
- Properly invalidates the cache when the lockfile changes

---

### Why caching?

Caching speeds up workflows by reusing dependency files between successive job runs. Github Actions runner is empty each time it runs so __without a cache downloading dependencies takes longer - the cache prevent this.__

### How it works?

1. First run (cache miss)
- the runner starts as a clean machine (without any cache).
- `actions/cache` tries to restore the cache -> no match is found.
- `npm ci` downloads dependencies from the npm registry
- after the job completes, `actions/cache`:
  - archives the specified patch (e.g. `~/.npm`)
  - uploads it to __GitHub Cache Storage__
  - Saves it under the provided `key`

At this point, the cache is created and available for future runs.

2. Subsequent runs (cache hit)
- the runner starts as a clean machine.
- `actions/cache` finds a cache matching the `key`.
- The archive is downloaded from __GitHub Cache Storage__.
- It is extracted to the specified path.
- `npm ci` reuses the local cachem reducing network downloads and speeding up installation.


## Task 5: Secrets and Environment Variables [v]

Create a workflow that:
- Uses a repository secret (e.g. `API_TOKEN`)
- Exposes the secret as an environment variable to a single step
- Ensures the secret value is never printed to logs

---

### How to set variables and secrets?

Under the `Settings` in Github we can set variables and secrets, based on environment or current repository.

### How to use variables and secrets in workflows?

- For variables use:
```yaml
{{vars.__variableName__}}
```

- For secrets use:
```yaml
{{secrets.__secretName__}}
```

Example:
```yaml

      - name: Read Environment variable
        run: echo "env_var ${{vars.VAR_API_KEY}}"

      - name: Read Secrets
        run: echo "secret ${{secrets.SECRET_API_KEY}}"
```
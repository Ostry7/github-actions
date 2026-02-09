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
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


## Task 2: Matrix Build

Create a workflow that:
- Runs on `pull_request`
- Uses a matrix strategy to test the application on:
  - Ubuntu
  - macOS
  - Windows
- Tests at least two different Node.js versions

---
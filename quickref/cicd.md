# Continuous Integration / Continuous Deployment

## GitHub Actions basics

- `workflow` is a github concept for some action. It's kicked off by some trigger you specify (push, pull request, button press, etc.)
- `jobs` is one part of a workflow. It's a set of steps that runs on the same runner
- `steps` is one step of a job
    - `uses` pulls in an external action
    - `run` specifies a specific command to run
- each workflow gets a `badge` that you can put in your readme showing status with a link, something like `https://github.com/dscruggs/learn-cicd-typescript-starter/actions/workflows/ci.yml/badge.svg`

## Format and lint

- format is code style consistency
- linting is somewhat similar but it looks for things that might actually affect code quality (like unused variables, rules on how to perform certain actions, etc.)

## Security

- JS eslint has a security plugin for linting, I wonder if python / ruff has something similar.

## CI vs CD

- the line is typically your CI handles all the quality checks and build process, and the final output is a package / image that you then hand off to CD. Sometimes the build process is in CD though
- CD handles pushing your changes to wherever your application is deployed

### Migrations

- DB migrations are a bit tricky to handle, you need to make sure your deployed app works with whatever changes are made on the DB. A lot of teams do migrations manually for this reason
- If you do want to automate a good way to do this would be to build your app image, then deploy the app changes right before you run migrations. Still a chance of downtime/issues but this minimizes it, and there's usually a better chance a DB migration is a breaking change than an app code change
- one nice way is to have DB migrations dry run during CI to catch issues, and running them requires manual approval but is otherwise automatic as far as CD

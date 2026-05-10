### Context objects
- Context objects in GitHub Actions (GHA) are structured, programmatic objects that provide metadata about the workflow run, runner environment, jobs, steps, and secrets. They act as containers of information, allowing you to create dynamic, responsive workflows by accessing data about the event that triggered the run (like a push or pull request) or details about the runner itself.

- They are accessed within your YAML workflow files using expression syntax: `${{ <context> }}`. 

- Key Context Objects
- **`github`**: Information about the workflow run and the event that triggered it.
- **`env`**: Contains environment variables set in a workflow, job, or step.
- **`secrets`**: Access to secrets stored in the repository or organization.
- **`runner`**: Information about the machine executing the job.
- **`job`**: Information about the current job.
- **`steps`**: Information about the steps that have run in the current job.
- **`needs`**: Contains outputs from jobs that the current job depends on.
- **`matrix`**: Information about the matrix configuration for the current job.
---
### Event activity and filters
**1. Event Activity Types**
- Activity types allow you to specify ==sub-actions within a major event== that should trigger your workflow.
- **Definition**: Many events that trigger workflows have multiple "activity types" (e.g., a pull request can be opened, closed, or labeled).
- **Usage**: You use the `types` keyword to define which activities matter.

> By default, a workflow only runs when a `pull_request` event's activity type is `opened`, `synchronize`, or `reopened`.

- **Example**: If you only want a workflow to run when a pull request is **opened** or **closed** (and not when it's just edited or labeled), you would configure it like this:
```YAML
on:
  pull_request:
    types: [opened, closed]
```


**2. Event Filters**
- Filters give you control based on ==metadata or properties of the event==, such as which branch was affected or which files were changed.
- **Definition**: Filters help prevent unnecessary builds by restricting runs to specific branches, tags, or file paths.
- **Common Filter Types**:
    - **Branches**: Use `branches` or `branches-ignore` to run only on specific Git refs.
    - **Paths**: Use `paths` or `paths-ignore` to run only if specific files (e.g., `**.js`) have changed.
    - **Tags**: Use `tags` or `tags-ignore` to trigger based on version tags.
- **Example**: Running a workflow only when code is pushed to the `main` branch and changes a file in the `src` directory:
```YAML
on:
  push:
    branches:
      - main
    paths:
      - 'src/**'
```

>This event only trigger the workflow if the changed file in the `src` directory, and the push on the main branch.

- [on.push.branches.tags.branches-ignore.tags-ignore](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#onpushbranchestagsbranches-ignoretags-ignore)

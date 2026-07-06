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
---
### Artifacts
##### Upload the artifacts
- The [actions/upload-artifact](https://github.com/actions/upload-artifact) GitHub Action ==allows you to save build outputs, logs, and files from your workflow run==. This makes data accessible to subsequent jobs or lets you download the results after the workflow completes.
- To upload files or directories in your `.github/workflows/*.yml` file, add a step that specifies an artifact `name` and the `path` to the files you want to store.
```yaml
- name: Upload Build Artifacts
  uses: actions/upload-artifact@v4
  with:
    name: my-build-artifact
    path: path/to/artifact/files/
    retention-days: 90
```

##### Download the artifacts
- You can download artifacts in GitHub Actions ==using automated workflow steps, the GitHub web interface, or the GitHub CLI==
- To download artifacts ==automatically== within a subsequent job of your workflow, use the official [actions/download-artifact](https://github.com/marketplace/actions/upload-a-build-artifact) action.
```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    needs: build # Ensures this job waits for the build job to finish
    steps:
      - name: Download Build Artifacts
        uses: actions/download-artifact@v4
        with:
          name: my-artifact-name # The exact name used during upload
          path: path/to/extracted/files # Directory where files will save
```
---
### Job outputs
- **Job outputs** in [GitHub Actions](https://docs.github.com/actions) ==allow you to pass data and string variables from one job to subsequent jobs in a workflow==. To make a job output work, you must write data to the `$GITHUB_OUTPUT` file in a step, map that step's value to a job-level output, and establish a dependency using the `needs` keyword in the receiving job.

- Here is a complete example from the [GitHub Docs](https://docs.github.com/actions/writing-workflows/choosing-what-your-workflow-does/passing-information-between-jobs) demonstrating how to share variables between an upstream job (`build`) and a downstream job (`deploy`)
```yaml
name: Shared Job Data Example

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    # Step 2: Map the step-level outputs to job-level outputs
    outputs:
      artifact_version: ${{ steps.set_version.outputs.version }}
      build_status: ${{ steps.compile.outputs.status }}
    
    steps:
      - name: Generate Version Number
        id: set_version
        # Step 1: Write variables to the $GITHUB_OUTPUT environment file
        run: echo "version=1.4.2" >> "$GITHUB_OUTPUT"

      - name: Compile Code
        id: compile
        run: echo "status=success" >> "$GITHUB_OUTPUT"

  deploy:
    runs-on: ubuntu-latest
    # Step 3: Establish dependency so this job waits for the first one to finish
    needs: build
    
    steps:
      - name: Use Upstream Job Outputs
        # Step 4: Access the data via the needs context
        run: |
          echo "Deploying version: ${{ needs.build.outputs.artifact_version }}"
          echo "The build status was: ${{ needs.build.outputs.build_status }}"
```

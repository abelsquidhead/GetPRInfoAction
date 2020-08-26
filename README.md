<p align="center">
  <a href="https://github.com/actions/typescript-action/actions"><img alt="typescript-action status" src="https://github.com/actions/typescript-action/workflows/build-test/badge.svg"></a>
</p>

# GitHub Actions for getting pull request information

This action gets the pull request url, the pull request title and pull request number from the pull request which triggered the workflow. It outputs these values as:

```
url    - URL to the pull request
title  - Title to the pull request
number - Pull request number

```

This action also outputs these values as environmental variables as:

```
PR_URL    - URL to the pull request
PR_TITLE  - Title to the pull request
PR_NUMBER - Pull request number
```


## Sample workflow that uses the GetPRInfoAction
```yaml
name: PR Workflow
on:
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
    - master

jobs:
  deployToStaging:
    if: (github.event_name == 'pull_request' && github.event.action != 'closed') 
    runs-on: ubuntu-latest
    steps:
      
      # download build artifacts
      - name: Download build artifacts
        uses: actions/download-artifact@master
        with:
          name: webapp

      # get values of the PR and store in env var
      - name: Get PR values
        uses: abelsquidhead/GetPRInfoAction@main
        with:
          githubToken: ${{ secrets.GITHUB_TOKEN }}
        id: getPRValuesStep

      # deploy web app
      - name: echoValues
        run: |
          echo 'PR Number is: ' ${{ steps.getPRValuesStep.outputs.number}}
          echo 'PR Title is: ' ${PR_TITLE}
```
The above example shows how to reference the output via the output variables and also how to use the environment variables in later steps. The GetPRInfoAction also requres your GitHub token to access the PR info. You can access your GitHub token from the workflow ith ${{ secrets.GITHUB_TOKEN }}

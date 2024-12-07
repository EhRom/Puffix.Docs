# GitHub CLI in Github Actions

To use Github CLI in a Github Action (Workflow), the `GH_TOKEN` environment variable must be linked to the action. The `GH_TOKEN` environment variable is linked to a secret, which contains a secret and a scope.

Example:
```yaml
name: Comment when opened
on:
  issues:
    types:
      - opened
jobs:
  comment:
    runs-on: ubuntu-latest
    steps:
      - run: gh issue comment $ISSUE --body "Thank you for opening this issue!"
        env:
          GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          ISSUE: ${{ github.event.issue.html_url }}
```

Source: [Using GitHub CLI in workflows](https://docs.github.com/en/actions/using-workflows/using-github-cli-in-workflows)

## Generate a Personnal Access Token

❗ **P**ersonal **A**ccess **T**oken are secrets and must be carefully stored in secure locations.
There are two types of **P**ersonal **A**ccess **T**oken:

To generate an access token, click on your profile photo, from any repository, and select **settings**. In the settings, click on **Developer Settings** (at the bottom of the left pane), an then on **Personnal Access Token** section.

You have the choice between:

- **Classic**: linked to the scope and rights of the user who generated the token, less secure,
- **Fine-grained**: to restrict the scope of the token to specific actions, more secure.

💡 It is recommended to prefer *Fine-grained* tokens.

Click on generate new token, validate the security questions, and set the following information:

- The token name,
- The owner,
- The expiration duration (prefer short duration),
- A description 
- The repositories which can you the PAT,
- The persmission scope

Once generated, the value of the token can be saved in a repository secret, or in any secure location.

💡 As the token expires shortly, a new token is to be generated often. Keep the information used for the generation in your repository documentation.

Source: [Managing your personal access tokens](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens)

## Next

Now you are ready to deep dive in the [GitHub CLI manual](https://cli.github.com/manual/) to bring GH CLI commands in your actions.

---

[Back](../README.md)

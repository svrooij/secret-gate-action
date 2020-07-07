# Secret (input) gate action

This actions allows you to check if secrets are set, and optionally fail. Usefull to have an action setup that still succeed even though someone did't configure the secrets correctly.

Missing secrets can also be intentionally left out to not yet enable some deployment, or to not deploy from forks.

## Inputs

### `inputsToCheck`

**Required** The inputs that need to be checked to have a value, comma separated.

### `failOnMissing`

**Optional** Set to true if you want to pipeline to fail if some input doesn't have a value. Default set to 'false'.

## Outputs

### `inputsChecked`

**true** if all inputs required checking also have a value. Otherwise **false**.

## Example usage (fail)

The secret gate action below will fail, so the rest won't be tried, resulting in a 'Failed' execution.

```yml
- uses: svrooij/secret-gate-action@v1
  with:
    inputsToCheck: 'NPM_TOKEN'
    failOnMissing: 'true'
  env:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
- name: Semantic Release
  uses: cycjimmy/semantic-release-action@v2
  id: semantic
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

## Example usage (continue)

By setting an `id` on the action, you can use it to decide if another action should be run or not.

If the workflow below would be in an repository that is forked, the Semantic Release will not even be tried until they have setup the `NPM_TOKEN` secret. But the workflow will still succeed if running in a forked repo.

```yml
- uses: svrooij/secret-gate-action@v1
  id: mygate
  with:
    inputsToCheck: 'NPM_TOKEN'
  env:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
- name: Semantic Release
  uses: cycjimmy/semantic-release-action@v2
  if: steps.mygate.outputs.inputsChecked == 'true'
  id: semantic
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

# Markdown Publish Schedule Action

GitHub Action to check whether a Markdown file has been scheduled for publishing.

The action searches for Markdown files inside the repository and looks for a `date` parameter in the YAML front matter. If the date value in any of the files is within the amount of minutes defined in the `interval` parameter the action will output "true". This action can be used in combination with your deployment process to schedule content.

## Inputs

| Input             | Required | Description                                                         |
|-------------------|----------|---------------------------------------------------------------------|
| interval          | yes      | How far back in minutes to check?                                   |
| content_directory | no       | Where to search for the Markdown files? (Default: whole repository) |

## Outputs

| Output       | Description                                                                                 |
|--------------|---------------------------------------------------------------------------------------------|
| is_scheduled | A **string value** of *"true"* or *"false"* indicating if a scheduled post was found or not |

## Netlify deployment example

The following workflow can be used to start a Netlify deployment. Note that you need to create a build hook URL in your Netlify site deploy settings and store it as the `NETLIFY_BUILD_HOOK` secret in the GitHub repository.

```yaml
name: Schedule post

on:
  schedule:
    - cron: "0 * * * *"

jobs:
  schedule:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: jannekem/publish-schedule-action@v1.2
      with:
        interval: 60
        content_directory: content
      id: publish
    - name: Trigger build
      if: steps.publish.outputs.is_scheduled == 'true'
      run: curl -X POST -d {} $NETLIFY_BUILD_HOOK
      env:
        NETLIFY_BUILD_HOOK: ${{ secrets.NETLIFY_BUILD_HOOK }}
```

## Links

- [Create a GitHub Action with JavaScript](https://pakstech.com/blog/create-github-action/)
- [Schedule Netlify Builds with GitHub Actions](https://pakstech.com/blog/netlify-publish-schedule/)

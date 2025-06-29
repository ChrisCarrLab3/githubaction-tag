# githubaction-tag
Tag a repository with the release branch name

Release 1.0.0
-------------

Release 1.0.1
-------------

Release 1.0.2
-------------

Release 1.0.3
-------------

Release 1.0.4
-------------

To get this to work, you must add the following to the top of the action - this grants write permission so it can write the tag

```
permissions:
  contents: write
```

The action runs on a pull request into main

```
on:
  pull_request:
    types:
      - closed
    branches:
      - main
```

Then the step checks that it's a valid PR merge before creating the tag

```
jobs:
  Explore-GitHub-Actions:
    runs-on: ubuntu-latest
    steps:
      - name: Tag release in GitHub on merge from release branch
        if: |
          github.event.pull_request.merged == true && 
          startsWith(github.event.pull_request.head.ref, 'release/')
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          RELEASE_BRANCH="${{ github.event.pull_request.head.ref }}"
          if [ -z "$RELEASE_BRANCH" ]; then
            echo "No release branch found in recent merges."
            exit 0
          fi
          RELEASE_NAME="${RELEASE_BRANCH#release/}"
          git config --global user.email "github-actions[bot]@users.noreply.github.com"
          git config --global user.name "github-actions[bot]"
          echo $RELEASE_NAME tag being created
          git tag "$RELEASE_NAME"
          git push "https://x-access-token:${GITHUB_TOKEN}@github.com/${GITHUB_REPOSITORY}.git" "$RELEASE_NAME"
```

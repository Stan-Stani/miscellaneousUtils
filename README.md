# miscellaneousUtils

## Using Husky hooks to keep NPM dependencies up to date
`.husky/post-checkout` and `.husky/post-merge`

```sh
!/bin/sh
# This script tracks changes in `yarn.lock` and `package.json` files
# and reruns `yarn` when changes are detected, using Git for change detection.
# Exit immediately if a command exits with a non-zero status.
set -e
# Get the hash of the last commit where yarn was run successfully
LAST_YARN_RUN=$(git config --get hooks.last-yarn-run || true)
# If there's no record of the last yarn run, use the initial commit
if [ -z "$LAST_YARN_RUN" ]; then
    LAST_YARN_RUN=$(git rev-list --max-parents=0 HEAD)
fi
# echo "Last yarn run: $LAST_YARN_RUN"
# Check if package.json or yarn.lock have changed since the last yarn run
if git diff --quiet "$LAST_YARN_RUN" HEAD -- package.json yarn.lock; then
    echo "No changes in package.json or yarn.lock since last yarn run. Skipping yarn."
else
    echo "package.json or yarn.lock has changed. Running yarn..."
    if yarn; then
        # Store the current commit hash as the last successful yarn run
        git config hooks.last-yarn-run $(git rev-parse HEAD)
        echo "yarn completed successfully."
    else
        echo "yarn failed. Please resolve the issues and try again."
        exit 1
    fi
fi
exit 0

Courtesy Claude Sonnet 3.5 with testing and prompting by Stan
```

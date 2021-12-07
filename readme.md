# overview

this is a place to share github action workflows so we can keep our ci dry and consistent.

## usage
TODO: give example
https://docs.github.com/en/actions/learn-github-actions/reusing-workflows#calling-a-reusable-workflow

## versioning
TODO: decide how to version these. most likely git tags

## workflows
### lambda-deploy
this just downloads a zip file called "build", which should have a file called "build.zip", which will get pushed to a lambda by name and region.  you have to pass in a github environment name to control how and when a deployment will go out. 

### typescript-build-and-test
this builds a typescript app up based on a very simple convention.  it runs the following commands on the lts version of node (14.x currently).
```bash
yarn install
yarn build
yarn test:unit
# remove dev deps before building zip
yarn install --production
zip -r build.zip build node_modules
# uploads build.zip to github artifact as "build"
```

### build-and-test-npm-package
this builds and tests a npm package. it runs the following commands on the lts version of node (14.x currently).
```bash
yarn install
yarn build
yarn test:unit

# validate the package version number
export PACKAGE_VERSION=v$(node -p -e "require('./package.json').version")
set +e
gh release view $PACKAGE_VERSION
export EXIT_CODE=$?
set -e
if [ $EXIT_CODE -eq 0 ]; then
    echo "$PACKAGE_VERSION already exists"
    exit 1
fi
```

### publish-npm-package
this will publish a npm package. it runs the following commands on the lts version of node (14.x currently).
```bash
yarn publish

# creates a github release using package version
export PACKAGE_VERSION=v$(node -p -e "require('./package.json').version")
gh release create $PACKAGE_VERSION --notes ""
```
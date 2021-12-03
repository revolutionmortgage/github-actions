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
echo "PACKAGE_VERSION=v$(node -p -e "require('./package.json').version")" >> $GITHUB_ENV
gh release view ${{ env.PACKAGE_VERSION }}
echo "PACKAGE_VERSION_EXISTS=1" >> $GITHUB_ENV
if [ ${{ env.PACKAGE_VERSION_EXISTS }} -eq 1 ]; then
    echo "${{ env.PACKAGE_VERSION }} already exists"
    exit 1
else
    exit 0
fi
```

### publish-npm-package
this will publish a npm package. it runs the following commands on the lts version of node (14.x currently).
```bash
# gets the version number to deploy from the package.json file and sets as an environment variable
echo "PACKAGE_VERSION=v$(node -p -e "require('./package.json').version")" >> $GITHUB_ENV

yarn publish

# creates a github release using the environment variable
gh release create ${{ env.PACKAGE_VERSION }} --notes ""
```
# Quick CI/CD with Netlify
## The CI/CD Pipeline SequencePermalink
__This guide sets up the following flow of events:__
1. You create a new branch in your local Git repository and make code changes to your Gatsby project.
2. You push your branch to your GitHub repository and create a pull request.
3. Netlify automatically creates a preview of the site with a unique URL that can be shared.
4. Travis CI automatically builds the site in an isolated container and runs any declared tests.
5. When all tests pass, you merge the PR into the repository’s master branch, which automatically triggers a deployment to your production Linode.

## Clone your gatsby starter repo
Install Gatsby
```sh
npm install -g gatsby-cli
```
Clone a gatsby starter template 
```sh
gatsby new gatsby-starter-default https://github.com/gatsbyjs/gatsby-starter-default
```
Verify our app runs as expected
```sh
cd gatsby-starter-default && npm run develop
```
Providing our app runs as expected we can commit our code to our github repo

## Hook up Netlify

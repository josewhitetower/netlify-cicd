# Quick CI/CD with Netlify :rocket:
__This guide sets up the following flow of events:__
1. Creating a ~~gatsby~~ NextJS project and push it to your github repository.
2. Hooking up Netflify to build and deploy your application.
3. Modify our app and opening a new pull request.
4. Netlify automatically creates a preview of the site with a unique URL that can be shared.
5. Travis CI automatically builds the site in an isolated container and runs any declared tests.
6. When all tests pass, you merge the PR into the repository’s master branch, which automatically triggers a deployment to your production Linode.

## Create a NextJS application
Generate a NextJS project and when prompted name it `netlify-cicd`
```js
npx create-next-app
```
Verify our app runs as expected
```sh
cd netlify-cicd && npm run dev
```
Update our build command within `package.json`
```js
"build": "next build && next export"
```
This will create a new `out` folder containing our final deployable app.

Create a new GitHub repo and name it `netlify-cicd` and commit our code to the repo we've just created
```sh
git add .
git commit -m "starter application"
git remote add origin https://github.com/your-username/netlify-cicd.git
git push -u origin master
```

## Setup Continuous Deployment with Netlify
Navigate to [Netlify](https://app.netlify.com/start) and login using your GitHub account

Create a 'New Site from Git' which includes three steps:
1. Connecting to GitHub
2. Pick your repo
3. Specify your build options (default should be fine) & Deploy!
   
![Create a new site](https://raw.githubusercontent.com/EoinTraynor/netlify-cicd/master/demo_assets/CreateSiteOnNetlify.png "Create a new site")

 * Branch to deploy: You can specify a branch to monitor. When you push to that particular branch, only then will Netlify build and deploy the site. The default is master.
 * Build Command: You can specify the command you want Netlify to run when you push to the above branch. The default is npm run build.
 * Publish directory: You can specify which folder Netlify should use to host the website, e.g., public, dist, build. The default is public.
 * Advanced build settings: If the site needs environment variables to build, you can specify them by clicking on Show advanced and then the New Variable button.

### Update our build options
Update our build command
```js
npm run build 
```
And our publish directory to target the `out` that will be generated from our build
```js
out/
```

Deploy logs can viewed at 'Site deploy in progress' and once this has finalized your site will be available at the generated URL :rocket:

## Making changes and creating a new pull request
Create a new local branch
```sh
git checkout -b "update/site-heading"
```

In our `pages/index.js` update to our page hero
```html
<div className='hero'>
  <h1 className='title'>Welcome to Netlify Previews!</h1>
  <p className='description'>
    To get started, edit <code>pages/index.js</code> and save to reload.
  </p>      
</div>
```

Now we can commit our changes to our repo and open a new pull request to our master branch.
```sh
git add pages/index.js
git commit -m "Updated Page Hero"
git push origin update/site-heading
```

Netlify will watch our repository for opened pull requests and build and deploy a preview version of our app with all of our new changes. We can see this in the pull request checks.

![Create a new site](https://raw.githubusercontent.com/EoinTraynor/netlify-cicd/master/demo_assets/PRChecks.png "Create a new site")

This enables teammates to see exactly what changes have been made without having to pull down the branch locally or before it's merged to our master branch :sunglasses:


If we accept and merge our pull request netlify will automatically redeploy our live app!

Continuous deployment ✅


## Implementing Continuous Integration with Travis
Create a new local branch
```sh
git checkout -b "update/add-continuous-integration"
```

> Status checks are based on external processes, such as continuous integration builds, which run for each push you make to a repository. You can see the pending, passing, or failing state of status checks next to individual commits in your pull request.

> If status checks are required for a repository, the required status checks must pass before you can merge your branch into the protected branch. For more information, see "About required status checks."
 
We need to build a simple but realistic integration into our build. So in this case test to ensure our project satisfies some standard linting rules with [ESlint](https://eslint.org). 
 
```sh
npm install eslint --save-dev
./node_modules/.bin/eslint --init
```

Add two run scripts to our `package.json`
```json
"lint": "eslint --ext .jsx,.js pages/",
"lint:fix": "eslint --ext .jsx,.js pages/ --fix"
```

Head over to [travis-ci.org](https://travis-ci.org/dashboard) and authorize your github account. Then activate your repository in your profile.

[Enabling required status checks](https://help.github.com/en/github/administering-a-repository/enabling-required-status-checks) on our repo.

Create a Travis file
```sh
touch .travis.yml
```
Specify the following configuration
```yml
language: node_js

node_js:
  - "stable"

before_script:
  - "npm install"

script:
  - "npm run test"
  - "npm run build" 
```

We should be seeing our build failing. This is expected 

Resolve our linting errors 
```sh
npm run lint:fix
```
As well as this we will have to update the extension of our `pages/index` from `.js` to `.jsx`. And now we can push our changes again 
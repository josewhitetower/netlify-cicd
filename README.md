# Quick CI/CD with Netlify :rocket:
## Create a NextJS application
Generate a NextJS project and when prompted name it `netlify-cicd`
```js
npx create-next-app
```
Verify our app runs as expected
```sh
cd netlify-cicd && npm run dev
```
Create a new GitHub repo and name it `netlify-cicd` and commit our code to the repo we've just created
```sh
git add .
git commit -m "starter application"
git remote add origin https://github.com/your-username/netlify-cicd.git
git push -u origin master
```



## Implementing Continuous Integration with Travis
Continuous integration aka. CI is the practice of automating tasks executed code changes or implications of a code change on a given project.

> Status checks are based on external processes, such as continuous integration builds, which run for each push you make to a repository. You can see the pending, passing, or failing state of status checks next to individual commits in your pull request. - *[About Status Checks, GitHub](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-status-checks)*

![Status Checks](https://help.github.com/assets/images/help/pull_requests/commit-list-statuses.png)

### Building our CI pipeline
We need to build a simple but realistic integration into our build. So, in this case, let's test to ensure our project satisfies some standard linting rules with [ESlint](https://eslint.org). 

```sh
git checkout -b "update/add-continuous-integration"
npm install eslint --save-dev
./node_modules/.bin/eslint --init
```

Add two run scripts to our `package.json`
```json
"lint": "eslint --ext .jsx,.js pages/",
"lint:fix": "eslint --ext .jsx,.js pages/ --fix"
```

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
  - "npm run lint"
  - "npm run build" 
```

### Authorize our application & enable required status checks
Head over to [travis-ci.com](https://travis-ci.org/dashboard) and authorize your GitHub account. Then activate your selected repository in your profile.

Next, we'll [enabling required status checks](https://help.github.com/en/github/administering-a-repository/enabling-required-status-checks) on our repo.
 for our project
> If status checks are required for a repository, the required status checks must pass before you can merge your branch into the protected branch. - *[About Status Checks, GitHub](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-status-checks)*


#### Test our Pipeline
Let's push our new Travis file along with our linter introduction to our remote branch
```sh
git add .
git commit -m "add CI"
git push origin add-continuous-integration
```

From GitHub we can now open a new pull request to our master branch. If we have configured everything correctly, we should now see our Travis status checks running 🤞
*Our build failing. This is expected.*

Let's get our checks passing!
```sh
npm run lint:fix
```
As well as this we will have to update the extension of our `pages/index` from `.js` to `.jsx`. And now we can push our resolved changes.

```sh
git add .
git commit -m "resolved linting errors"
git push origin add-continuous-integration
```

This will automatically re-run our integration pipeline, which should now be passing! ✅

Have we really even implemented CI if we don't have the badge to prove it? :joy: Head back over to [travis-ci.com](https://travis-ci.com) under your project and copy the markdown status of our build. 
[![Build Status](https://travis-ci.com/EoinTraynor/netlify-cicd.svg?branch=master)](https://travis-ci.com/EoinTraynor/netlify-cicd)
```md
[![Build Status](https://travis-ci.com/your-name/netlify-cicd.svg?branch=master)](https://travis-ci.com/your-name/netlify-cicd)
```

### Bonus round
Continuous integration can offer a lot more than just checking that we are new code is following linting standards or verify that we are not breaking tests. You might be familiar with the auditing tool '[lighthous](https://developers.google.com/web/tools/lighthouse)' which
> is an open-source, automated tool for improving the quality of web pages. You can run it against any web page, public or requiring authentication. It has audits for performance, accessibility, progressive web apps, and more.

This tool is available as an automated integration bot that we can build into our pipeline. Check it out here [Lighthouse Bot](https://github.com/GoogleChromeLabs/lighthousebot)

##  Continuous Deployment with Netlify
Firstly we will need to update our build command within `package.json`
```js
"build": "next build && next export"
```
This will create a new `out` folder containing our final static deployable app.

Navigate to [Netlify](https://app.netlify.com/start) and log in using your GitHub account

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

Deploy logs can be viewed at 'Site deploy in progress' and once this has finalized your site will be available at the generated URL :rocket:

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


If we accept and merge our pull request Netlify will automatically redeploy our live app!

Continuous deployment ✅

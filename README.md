# BKKHackMap &nbsp; [![Build Status](https://travis-ci.org/bkkhack/hackmap.svg?branch=master)](https://travis-ci.org/bkkhack/hackmap)

A tool to help BKKHack members collaborate on projects. Access it at https://bkkhack.github.io/hackmap.

# Development

If you want to contribute, we'd love it! Feel free to open issues or pull requests in this repository.
If you have any questions, ask will, djay or ches in the [bkkhack slack channel](https://thailanddevs.slack.com/messages/C04UFE0B9/).

## Overview

This webapp is a single page application, and contains client-side code only. The server-side functionality is provided by:

- Github Issues - This is the primary data store. The client-side code makes GitHub API calls to persist data as GitHub issue comments.
- Heroku - A very small, free instance running [GateKeeper](https://github.com/prose/gatekeeper) to hide the GitHub OAuth secret.

The client-side code uses written in ES6, with [Vue.js](https://vuejs.org/) for the UI. It uses webpack and babel for transpilation to ES5.

## Working on the code

To get started, clone this repo and run:

1. `npm install` -- downloads development and runtime dependencies.
2. `npm start` -- starts up webpack development webserver to serve the application, monitor changes and rebuild assets on change.

The following commands are also available:

- `npm test` -- starts the unit test runner, and reruns unit tests as files change.
- `npm run lint` -- checking code style according to eslint setup.
- `npm run build` -- builds the assets (js, sourcemaps, etc) into the `dist` folder.
- `npm run deploy` -- deploy dist folder to gh-pages branch.

## Editor Setup

This project uses [Vue.js Components](https://vuejs.org/v2/guide/single-file-components.html) (i.e. .vue files) to organize
the code. You'll probably want to install an extension to your editor to get syntax highlighting, code completion, etc.
[Most major editors are supported](https://github.com/vuejs/awesome-vue#source-code-editing).

Additionally, we use an `.editorconfig` file to automatically configure coding style (e.g. indent levels, tabs vs spaces).
Again, most major editors have an [editorconfig plugin](http://editorconfig.org/#download), or already support it natively.

## Navigating the code

```
src
├── main.js - main entry point for the UI rendering
├── App.vue - draws the application, handles communication between components.
├── components
│   ├── Center.vue - main screen with the map
│   ├── Left.vue - left sidebar, with the project list
│   └── Right.vue - right sidebar, with the project detail
├── floorplan
│   └── xxxxxxx.svg - floorplan image, we update this for each location
├── github-issues.js - main entry point for the application logic, handles polling.
├── github-oauth.js - contains the oauth dance logic for github authentication
└── github-serialization.js - converts projects to github comments for storage, and vice versa

test
└── unit tests

build
└── build scripts for webpack, from https://github.com/vuejs-templates/webpack
```

## Testing Authentication

When authenticating with GitHub, GitHub will check the referring URL, and it must match exactly with the URL configured for the OAuth integration. The URL configured for hackmap is `https://bkkhack.github.io/hackmap/` (unfortunately, GitHub does not support multiple URLs). This means that you cannot test authentication on `http://localhost:8080/hackmap/`, the URL that the webpack dev server uses. This can be fixed with a hosts file entry and proxying port 8080 to port 80. See the OS-specific guides below.

#### Debian/Ubuntu

First, add an entry in your hosts file to map 127.0.0.1 to bkkhack.github.io:

```
127.0.0.1       bkkhack.github.io
```

Second, enable the nginx configuration included in the repository. It will proxy port 8080 to port 80, and use a self-signed cert.

```
> cd /etc/nginx/sites-enabled/
> sudo ln -s ~/path/to/hackmap/config/nginx-bkkhack-config 
> sudo service nginx reload
```

Now, after starting the webpack development webserver, navigating to https://bkkhack.github.io/hackmap/ and accepting the self-signed cert, you should be able to access your code and test authentication.

## Initial Deployment

1. For GitHub authentication, create a GitHub app. You will receive a client id and a secret token.
    - The client id should be set in `src/github-oauth.js`
    - The secret token should be configured in the `bkkhackmap` heroku instance.
2. Create a new GitHub issue tagged with "BKKHack Main Thread". This issue will be used as the back-end data store.
3. Create a black and transparent SVG that represents the floor plan of BKKHack. Save it as `images/floorplan.svg`.

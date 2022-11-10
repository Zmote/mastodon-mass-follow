# mastodon_mass_follow

Mass Follow Accounts on a certain Mastodon host.

# Status

Currently, with Application Setup on your Account (Preferences > Development > New Application)
and providing the Application URL and Redirection URL to https://mastodon-mass-follow.netlify.app
(auto-deployed instances), you can by using client key and client secret retrieve
the appropriate token for follow operations. Also, you can load all local accounts
from a certain mastodon host and follow them one by one.

Missing part: Algorithm to loop through the loaded accounts to follow all of them.

Will finish that whenever I get to that.

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).

# Next SSR Firebase Deployment 

Instructions for deploying next app to firebase with SSR

### Setup Instructions

* `cd` inside your project
* `npm install -g firebase-tools`
* `npm install firebase-functions@latest firebase-admin@latest --save`
* `firebase login`
* Init Firebase project `firebase init`  select only `Hosting: Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys`

**Note: If firebase init has alrady been done, skip to the next step**

* Inside firebase.json add the following code: 
```{
 "hosting": {
   "public": "public",
   "ignore": [
     "firebase.json",
     "**/.*",
     "**/node_modules/**"
   ],
   "rewrites": [
     {
       "source": "**",
       "function": "nextServer"
     }
   ]
 },
 "functions": {
   "source": ".",
   "runtime": "nodejs14",
   "ignore": [
     "**/src/**",
     "**/.vscode/**",
     ".firebase/**",
     ".firebaserc",
     "firebase.json",
     "**/node_modules/**",
     "**/public/**",
     "**/.next/cache/**"
   ]
 }
}
```

* Create a functions.js file in the root folder and paste the following code: 
```
const { https } = require('firebase-functions');
const { default: next } = require('next');
const isDev = process.env.NODE_ENV !== 'production';
const server = next({
 dev: isDev,
 conf: { distDir: '.next' },
});
const nextjsHandle = server.getRequestHandler();
exports.nextServer = https.onRequest((req, res) => {
 return server.prepare().then(() => nextjsHandle(req, res));
});
```

* Paste this line `"main": "functions.js"` into the package.json file
* Run: `npm run build`
* `firebase deploy`

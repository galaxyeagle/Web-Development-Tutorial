
You can use tools like `ngrok` to expose your local server to the internet during testing. But Deployment or Hosting your App is crucial to transition from Development to Production Mode.

# Frontend

## Firebase

For Firebase hosting, cd to your `/frontend` folder in the powershell and run these commands : 

```js
// Install the Firebase CLI globally using npm:
1. npm install -g firebase-tools

//Login to Firebase
2. firebase login

// Initialize Firebase in Your Project
3. firebase init
/*During the initialization process:
- Select "Hosting: Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys" using the arrow keys and press Space to select.
- Choose your Firebase project from the list.
- Set your public directory to build (this is where React builds the production files).
- Configure it as a single-page app (rewrite all URLs to index.html).
Do not set up automatic builds and deploys with GitHub for now.*/

//Create a production Build of your React App
4. npm run build

//Deploy your app to Firebase Hosting
5. firebase deploy --only hosting
//This command uploads the contents of the build directory to Firebase Hosting
```

After deployment, Firebase will provide a hosting URL. Open this URL in your browser to verify that your app is live.

If you don't want to deploy to the final app URL but to a preview URL for testing and sharing, you need to replace Step 5 above with `firebase:hosting:channel deploy channel-id` as mentioned [here](https://firebase.google.com/docs/hosting/test-preview-deploy).



# Backend

## Render

1. Push your code into a public Git repository (eg. Github)
```bash
# Initialize git 
git init

# Add all files
git add .

# Make your first commit
git commit -m "Initial commit"

# Rename current branch to main if needed
git branch -M main

#Add the remote repository
git remote add origin https://github.com/galaxyeagle/backend.git

# Push it
git push -u origin main
```
2. Go to the Render Dahboard webpage and click on the "New" button and select "Web Service".
3. Select your Git provider (e.g., GitHub) and connect your repository to Render.
4. Then a configuration page will open. Enter the build (npm install) and start (npm run dev) commands of your Express App there.
5. Add any necessary environment variables in the Render dashboard under the "Environment" section. ( nb. Render ensures that these variables are only accessible to your application at runtime and are not visible to the public since they're not in the sourcecode.)
6. Click on "Create Web Service" to start the deployment process.
Render will pull your code from the repository, build it, and deploy it.
7. After deployment, Render will provide a hosting URL. Open this URL in your browser to verify that your app is live.

Render supports auto-deploy from your Git repository. So any changes pushed to the connected Github branch will trigger a new deployment.
# Firebase Setup Instructions for Comments Section

Your comments section is ready! You just need to set up a free Firebase project and add your configuration.

## Step 1: Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add project" or "Create a project"
3. Enter a project name (e.g., "step-tracker-comments")
4. Disable Google Analytics (not needed for this project)
5. Click "Create project"

## Step 2: Register Your Web App

1. In your Firebase project, click the **Web icon** (`</>`) to add a web app
2. Give it a nickname (e.g., "Step Tracker")
3. **Do NOT** check "Set up Firebase Hosting"
4. Click "Register app"
5. Copy the `firebaseConfig` object shown on the screen

## Step 3: Set Up Firestore Database

1. In the Firebase Console, go to **Build** → **Firestore Database**
2. Click "Create database"
3. Choose **Start in test mode** (we'll secure it in the next step)
4. Select a location closest to you
5. Click "Enable"

## Step 4: Configure Firestore Security Rules

To allow anyone to read comments but prevent spam/abuse:

1. In Firestore, go to the **Rules** tab
2. Replace the rules with:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /step-tracker-comments/{comment} {
      // Anyone can read comments
      allow read: if true;

      // Anyone can create comments with validation
      allow create: if request.resource.data.name is string &&
                      request.resource.data.name.size() > 0 &&
                      request.resource.data.name.size() <= 50 &&
                      request.resource.data.text is string &&
                      request.resource.data.text.size() > 0 &&
                      request.resource.data.text.size() <= 500 &&
                      request.resource.data.timestamp == request.time;

      // No updates or deletes allowed (prevents comment editing/deletion)
      allow update, delete: if false;
    }
  }
}
```

3. Click "Publish"

## Step 5: Add Your Firebase Config to index.html

1. Open `projects/steps/index.html`
2. Find this section (around line 275):

```javascript
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
  projectId: "YOUR_PROJECT_ID",
  storageBucket: "YOUR_PROJECT_ID.appspot.com",
  messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
  appId: "YOUR_APP_ID"
};
```

3. Replace it with your actual Firebase config from Step 2

## Step 6: Test It Out!

1. Open your `index.html` file in a browser
2. Scroll down to the comments section
3. Try posting a test comment
4. Check the Firebase Console → Firestore Database to see your comment appear!

## Features Included

✅ **No accounts needed** - Users just enter their name
✅ **Real-time updates** - New comments appear instantly
✅ **Clean UI** - Matches your minimalist monospace theme
✅ **Spam protection** - Character limits and validation rules
✅ **XSS protection** - Comments are sanitized before display
✅ **Mobile-friendly** - Responsive design

## Firebase Free Tier Limits

The free "Spark" plan includes:
- 50,000 reads/day
- 20,000 writes/day
- 20,000 deletes/day
- 1 GB stored data

This is **more than enough** for a personal project with friends commenting!

## Optional: Monitor Your Comments

To view or delete comments:
1. Go to Firebase Console → Firestore Database
2. Navigate to the `step-tracker-comments` collection
3. You can manually delete any spam comments from there

## Troubleshooting

**Comments not loading?**
- Check browser console for errors
- Verify your Firebase config is correct
- Make sure Firestore rules are published

**Can't post comments?**
- Verify Firestore security rules are set correctly
- Check that the collection name matches: `step-tracker-comments`

**Need help?**
Check [Firebase Documentation](https://firebase.google.com/docs/firestore) or feel free to modify the security rules as needed!

# Client Portal Setup Instructions

## Overview
This client portal provides secure authentication using Google, Apple, and Microsoft accounts through Firebase Authentication. No passwords are stored on your servers.

## Setup Steps

### 1. Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Create a project" or select an existing one
3. Enter your project name and follow the setup wizard

### 2. Enable Authentication Providers

1. In Firebase Console, go to **Authentication** > **Sign-in method**
2. Enable the following providers:

#### Google Sign-In
- Click on Google
- Toggle "Enable"
- Add your project support email
- Click "Save"

#### Microsoft Sign-In
- Click on Microsoft
- Toggle "Enable"
- You'll need to:
  1. Go to [Azure Portal](https://portal.azure.com/)
  2. Register a new application in Azure Active Directory
  3. Get the Application (client) ID and Directory (tenant) ID
  4. Create a client secret
  5. Add redirect URI: `https://YOUR-PROJECT.firebaseapp.com/__/auth/handler`
  6. Enter these details in Firebase

#### Apple Sign-In
- Click on Apple
- Toggle "Enable"
- You'll need:
  1. An Apple Developer account ($99/year)
  2. Create an App ID with Sign in with Apple capability
  3. Create a Service ID
  4. Create a private key
  5. Enter these details in Firebase

### 3. Get Your Firebase Configuration

1. Go to **Project Settings** (gear icon)
2. Scroll to "Your apps" section
3. Click "Add app" > Web icon
4. Register your app with a nickname
5. Copy the configuration object

### 4. Update the Configuration Files

Replace the placeholder configuration in these files with your actual Firebase config:
- `portal-login.html` (lines 106-113)
- `portal-dashboard.html` (lines 59-66)
- `firebase-config.js`

```javascript
const firebaseConfig = {
    apiKey: "YOUR_ACTUAL_API_KEY",
    authDomain: "YOUR_ACTUAL_AUTH_DOMAIN",
    projectId: "YOUR_ACTUAL_PROJECT_ID",
    storageBucket: "YOUR_ACTUAL_STORAGE_BUCKET",
    messagingSenderId: "YOUR_ACTUAL_MESSAGING_SENDER_ID",
    appId: "YOUR_ACTUAL_APP_ID"
};
```

### 5. Set Up Firestore Database (Optional)

To store user subscriptions and app data:

1. Go to **Firestore Database** in Firebase Console
2. Click "Create database"
3. Choose production or test mode
4. Select a location
5. Create a collection called "users"

Example user document structure:
```json
{
  "uid": "user_unique_id",
  "email": "user@example.com",
  "displayName": "John Doe",
  "subscribedApps": [
    {
      "id": "nextcloud",
      "name": "Nextcloud",
      "description": "File storage and collaboration",
      "icon": "☁️",
      "url": "https://nextcloud.yourdomain.com",
      "color": "#0082c9"
    }
  ],
  "subscriptionLevel": "premium",
  "createdAt": "2024-01-15T10:00:00Z"
}
```

### 6. Security Rules

Add these Firestore security rules:
```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can only read their own data
    match /users/{userId} {
      allow read: if request.auth != null && request.auth.uid == userId;
      allow write: if false; // Only admin can write
    }
  }
}
```

### 7. Domain Configuration

1. Add your domain to Firebase:
   - Go to **Authentication** > **Settings** > **Authorized domains**
   - Add your production domain

2. Update OAuth redirect URIs:
   - For each provider, ensure your domain is in the redirect URIs

## Testing Locally

1. You can test locally, but OAuth providers require HTTPS
2. Consider using Firebase Hosting for easy HTTPS deployment
3. Or use a tool like ngrok for local HTTPS testing

## Production Deployment

1. Ensure all Firebase config values are updated
2. Set up HTTPS on your domain
3. Add your domain to all OAuth provider configurations
4. Test each authentication method thoroughly

## Managing User Subscriptions

Currently, the dashboard shows sample apps. To connect real user data:

1. Create an admin interface to manage user subscriptions
2. Store subscription data in Firestore
3. Update `portal-dashboard.html` to fetch real data (uncomment lines 90-92)

## Security Considerations

- Never commit Firebase config with real API keys to public repositories
- Use environment variables for sensitive configuration in production
- Implement proper Firestore security rules
- Consider adding rate limiting for authentication attempts
- Enable Firebase App Check for additional security

## Support

For issues with:
- Firebase setup: [Firebase Documentation](https://firebase.google.com/docs)
- Microsoft OAuth: [Azure AD Documentation](https://docs.microsoft.com/en-us/azure/active-directory/)
- Apple Sign In: [Apple Developer Documentation](https://developer.apple.com/sign-in-with-apple/)

## Next Steps

1. Set up Firebase project and authentication
2. Update configuration files with your Firebase config
3. Deploy to a server with HTTPS
4. Create admin interface for managing user subscriptions
5. Customize the dashboard UI to match your branding
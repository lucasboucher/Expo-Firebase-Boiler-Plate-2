# Getting Started

## Overview

This starter module provides the foundational setup for a cross-platform React Native application using Expo, Firebase, and robust navigation. It handles initial configuration, environment-based security, authentication context, and seamless integration of storage, database, and navigation features. The starter is designed to offer a scalable structure for modern mobile apps, ensuring quick onboarding and secure connectivity to Firebase services.

## Key Features

- **Expo Bootstrapping**: Rapid app development foundation leveraging Expo for cross-platform deployment and native capabilities.
- **Firebase Integration**: Centralized initialization and export of Firebase Authentication, Firestore, and Storage services, ready for use throughout the app.
- **.env Configuration**: Secure, environment-based management of sensitive Firebase credentials using `.env` files and the `react-native-dotenv` plugin.
- **Multiple Context Providers**: Global state management for user data and authentication, offering modular separation of concerns.
- **Navigation Architecture**: Conditional navigation flow between authentication and main app stacks, powered by React Navigation.
- **SVG Asset Support**: Native support for using SVG assets in the app via custom Metro bundler configuration.

## System Errors

- **Missing/Invalid Environment Variables**:  
  Occurs if required variables (e.g., `APIKEY`, `PROJECTID`, etc.) are not set in the `.env` file.
  - **Resolution**: Verify your `.env` file is complete and follows the `.env.exemple` template; restart your development server after changes.

- **Firebase Initialization Errors**:  
  Can occur due to a misconfigured or incomplete Firebase config.
  - **Resolution**: Double-check environment variables and ensure your Firebase project is correctly set up.

- **SVG Asset Import Failures**:  
  If SVG images aren't loaded or throw errors, Metro bundler may not be correctly configured.
  - **Resolution**: Ensure `metro.config.js` includes the SVG transformer and that the correct dependencies are installed.

## Usage Examples

```javascript
// Using initialized Firebase services anywhere in your app:
import { FB_AUTH, FB_DB, FB_STORE } from './firebaseconfig';

// Accessing authenticated user or context data
import { useAuth } from './context/AuthContext';

const { currentUser, loading } = useAuth();

if (currentUser) {
  // User is authenticated, interact with Firestore:
  await addDoc(collection(FB_DB, 'users'), { name: currentUser.displayName });
}
```

```javascript
// Adding secure environment variables
// .env file (do not commit this to version control)
APIKEY=your-firebase-api-key
PROJECTID=your-firebase-project-id
AUTHDOMAIN=your-app.firebaseapp.com
STORAGEBUCKET=your-app.appspot.com
MESSAGINGSENDERID=your-sender-id
APPID=your-app-id
```

```sh
# Starting the application
npm install
npm run start
# or, for platform-specific start
npm run android
npm run ios
npm run web
```

## System Integration

```mermaid
flowchart LR
  Firebase["Firebase Services"]
  EnvVars[".env Variables"]
  Navigation["React Navigation"]
  Contexts["Auth/User Context Providers"]
  SVG["SVG & Asset Support"]

  EnvVars --> firebaseconfig["firebaseconfig.js"]
  firebaseconfig --> Firebase
  firebaseconfig --> App["App.js"]
  Contexts --> App
  Navigation --> App
  SVG --> MetroConfig["metro.config.js"]
  MetroConfig --> App
  
  App --> "Your App Components"
```

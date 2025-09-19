# Expo Firebase Boilerplate API Overview

## Overview

This module provides a foundational authentication and user session system for React Native apps bootstrapped with Expo, using Firebase as the backend. It manages user sign-in, sign-up, and session persistence, offers user context to descendant components, and handles navigation between authentication and the main app flows.

The goal is to seamlessly support user registration, login, profile retrieval, and navigation state, with clear separation between authentication routes and main application routes.

## Key Features

- **Authentication Context**: Provides a context-driven approach to manage authentication state, exposing current user information and common authentication methods (sign up, sign in, sign out, password reset).
- **User Profile Context**: Automatically provides user profile data (such as first name, last name) from Firestore to the app via context, keeping it in sync with authentication state.
- **Conditional Navigation Routing**: Dynamically routes users to the authentication flow (sign up, sign in) or the main application interface based on their authentication status.
- **Error Feedback and Validation**: Validates user input and delivers actionable error messages for common authentication scenarios (e.g., wrong credentials, email in use, weak password).
- **Persistent Session Management**: Integrates with Firebase Auth for real-time session persistence and auto-login, remembering users across app restarts.

## System Errors

- **auth/email-already-in-use**: Attempt to sign up with an email already registered.  
  *Resolution*: Use a different email or attempt password reset if the email is yours.

- **auth/invalid-email**: Provided email is not in a valid format.  
  *Resolution*: Enter a correctly formatted email address.

- **auth/weak-password**: Password does not meet Firebase's security criteria (minimum 6 characters).  
  *Resolution*: Choose a stronger password with at least 6 characters.

- **auth/invalid-credential**: Invalid login credentials provided during sign in.  
  *Resolution*: Check the email and password are correct; if unsure, reset the password.

- **User document not found**: No Firestore user profile exists for the authenticated UID.  
  *Resolution*: Ensure sign up completes successfully and profile is created in Firestore.

- **Network or Server Errors**: Any operation may fail due to network issues or Firebase unavailability.  
  *Resolution*: Retry after confirming network connectivity.

## Usage Examples

```jsx
// In App.js, wrap your app in providers for Auth and User context:
export default function App() {
  return (
    <AuthProvider>
      <UserProvider>
        <NavigationContainer>
          <AppNavigator />
        </NavigationContainer>
      </UserProvider>
    </AuthProvider>
  );
}

// Inside a screen, access authentication and user profile:
import { useAuth } from '../context/AuthContext';
import { useUser } from '../context/UserContext';

function ProfileScreen() {
  const { currentUser, logOut } = useAuth();
  const { profile } = useUser();

  return (
    <View>
      <Text>Email: {currentUser?.email}</Text>
      <Text>Name: {profile.FirstName} {profile.LastName}</Text>
      <Button title="Log Out" onPress={logOut} />
    </View>
  );
}

// To sign in or sign up in screens:
const { signIn, signUp } = useAuth();

// Sign In
signIn(email, password)
  .then(/* handle success */)
  .catch(err => /* handle errors */);

// Sign Up and create Firestore profile record
signUp(email, password)
  .then(userCredential => setDoc(doc(FB_DB, 'users', userCredential.user.uid), {
    FirstName: firstName,
    LastName: lastName
  }))
  .then(/* handle success */)
  .catch(/* handle errors */);
```

## System Integration

```mermaid
flowchart LR
  FirebaseAuth[("Firebase Auth Service")]
  Firestore[("Firestore Database")]
  Navigation[("React Navigation")]
  
  AppJS[App.js]
  AuthProvider[AuthProvider<br/>(AuthContext.js)]
  UserProvider[UserProvider<br/>(UserContext.js)]
  AuthStack[AuthStack Navigator]
  MainStack[MainStack Navigator]
  Screens["Screens (SignIn, SignUp, Home, Browse, Profile, FirstScreen)"]
  
  FirebaseAuth <-->|User login/signup/logout| AuthProvider
  Firestore <-->|Profile read/write| UserProvider
  AppJS --> AuthProvider --> UserProvider --> Navigation
  Navigation --> AuthStack
  Navigation --> MainStack
  AuthProvider -->|Auth state| Screens
  UserProvider -->|Profile data| Screens
  AuthStack -->|Unauthenticated| Screens
  MainStack -->|Authenticated| Screens
```

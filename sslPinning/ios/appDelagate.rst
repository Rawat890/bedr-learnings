[App Launch]
     |
     v
[ClearKeychainIfNecessary()]
     |
     v
[Check Firebase Initialized?]
     |
     v
[Configure Firebase]
     |
     v 
[Show LaunchScreen.storyboard]
     |
     v
[fetchTrustKitPublicKeys()]
     |
     v
[Firestore -> Get 'sslPinningKeys']
     |
     v
[storeTrustKitConfigFromFirestoreData()]
     |
     v
[TrustKit initialized with dynamic keys]
     |
     v
[Set window + Call super didFinishLaunchingWithOptions]
     |
     v
[React Native App Starts]


✅ Code Flow Explanation (Step-by-Step)
1. Imports

Loads required modules: React Native, Firebase, TrustKit (SSL pinning), Orientation utils.

2. ClearKeychainIfNecessary()

Runs only once (first launch of the app).

Clears sensitive data from the Keychain if the app has never been launched before.

3. application:didFinishLaunchingWithOptions:

🔐 Step 1: Clears keychain if needed.

🚀 Step 2: Sets self.moduleName = @"app" (React Native root).

⚙️ Step 3: Configures Firebase ([FIRApp configure]) if not already configured.

🖼️ Step 4: Creates a window and displays the LaunchScreen.storyboard.

⏳ Step 5: Shows splash screen while waiting for SSL pinning keys to load.

🔑 Step 6: Calls fetchTrustKitPublicKeys: to fetch SSL keys from Firestore.

Once keys are received, configures TrustKit and:

Calls super application:didFinishLaunchingWithOptions: to finish app startup.

4. fetchTrustKitPublicKeys:

Connects to Firestore.

Reads the document publicKeys/sslPinningKeys.

If data exists, calls storeTrustKitConfigFromFirestoreData:.

5. storeTrustKitConfigFromFirestoreData:

Builds TrustKit config from Firestore key data.

Saves config to NSUserDefaults.

Initializes TrustKit with SSL pinning domains.

Sets pinningValidatorCallback:

Cancels network requests that fail SSL pinning validation.

6. NSURLSessionDelegate

Intercepts HTTPS requests.

Uses TrustKit to validate SSL certificates.

Falls back to default handling if TrustKit doesn't handle the challenge.

7. React Native Config

Sets supported interface orientations.

Configures JS bundle URL based on build type (DEBUG vs. production).
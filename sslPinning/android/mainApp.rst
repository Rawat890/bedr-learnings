Package: com.package_name

Class: MainApplication

Extends: Application (Android app lifecycle)

Implements: ReactApplication (required for React Native)


App Starts
   ↓
MainApplication.onCreate()
   ↓
→ SoLoader.init()                      // Load native libraries
→ SSLPinnerFactory.createAsync()      // Create SSL pinning
→ OkHttpClientProvider.setOkHttp...   // Set custom network client
→ load()                              // If using new architecture
→ ApplicationLifecycleDispatcher      // Manage app lifecycle events
   ↓
ReactNativeHostWrapper initialized
   ↓
→ React packages are loaded
→ Hermes engine / DevSupport config


Let's go through - 
class MainApplication : Application(), ReactApplication
* Application → Base class for maintaining global application state.
* ReactApplication → Needed to integrate React Native with native Android code.

override fun getPackages(): List<ReactPackage> =
    PackageList(this).packages.apply {
        add(Package1Module()) // Manually add non-autolinked packages
    }

* Loads auto-linked packages via PackageList.
* Adds a custom native module Package1Module manually.

🔥 Application Lifecycle: 
onCreate()override fun onCreate() {
    super.onCreate()
    }
SoLoader.init(this, false)


runBlocking {
    val sslFactory = async(Dispatchers.IO) { SSLPinnerFactory.createAsync() }.await()
    OkHttpClientProvider.setOkHttpClientFactory(sslFactory)
}

* Uses Kotlin Coroutines to asynchronously create a custom SSL Factory for SSL pinning.
* Applies the factory to the React Native networking stack via OkHttpClientProvider.
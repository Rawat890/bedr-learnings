[App Starts / React Native Loads]
        |
        v
[SSLPinnerFactory.createAsync()]
        |
        v
[fetchPublicKeyFromFirestore()]
        |
        |-- Success --> [Use fetched key from Firestore]
        |
        |-- Failure --> [Log warning & use DEFAULT_KEY]
        |
        v
[Create CertificatePinner with key]
        |
        v
[Return OkHttpClient with pinned cert]
        |
        v
[Secure HTTPS Communication]


It ensures secure HTTPS communication by rejecting connections that don’t match the pinned certificate. If fetching the key fails, it falls back to a default SSL key.

🔍 Code Breakdown (Step-by-Step)
🔐 1. Class Declaration
class SSLPinnerFactory(private val prodPublicKey: String) : OkHttpClientFactory


A custom implementation of React Native’s OkHttpClientFactory.

Stores the SSL public key (prodPublicKey) used for pinning.

🌐 2. createNewNetworkModuleClient()
override fun createNewNetworkModuleClient(): OkHttpClient


Creates a custom OkHttpClient that uses the CertificatePinner.

Pins the specified hostname (backend.bookaneyedoctor.co.uk) using SHA-256 hash of the public key.

📦 3. companion object

Contains utility functions and constants:

a. createAsync()
suspend fun createAsync(): SSLPinnerFactory


Async method that fetches public key from Firestore.

Returns a new SSLPinnerFactory instance using fetched or default key.

b. fetchPublicKeyFromFirestore()
private suspend fun fetchPublicKeyFromFirestore(): String?


Connects to Firestore → publicKeys/sslPinningKeys.

Reads the prod.keyArray[0] value.

Returns the key (or null if missing/error).

Logs every step for debugging (useful during development).

c. DEFAULT_KEY

A fallback key if Firestore key fetch fails.


🧠 Integration in MainApplication (React Native)

In your MainApplication.kt or .java, you would typically initialize the custom SSL pinning like:

val sslPinnerFactory = SSLPinnerFactory.createAsync()
OkHttpClientProvider.setOkHttpClientFactory(sslPinnerFactory)


This makes sure React Native network traffic (fetch, axios, etc.) uses your pinned OkHttp client.
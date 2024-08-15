
App Service provides built-in authentication, but you can opt not to use it. The benefit of using it is that you don't need to code to integrate different auth capabilities and multiple login providers.

**Federated Identity** means that there are different identity providers available by default such as Google and Facebook. When you enable them, their sign-in endpoint will be available for user authentication and token validation. They essentially take care of everything, from managing user identities and the whole authentication flow.

#### How it works
The authentication / authorization module runs in the same sandbox as your application code. It works like a `WebApplication` middleware in the sense that when it's enabled, every HTTP request passes through this module first doing several things such as authentication, token validation, session management, and identity injection in the HTTP request headers.

#### Providers
- MS identity platform
- Facebook
- Google
- Twitter
- any OpenID Connect provider
- GitHub

#### Authentication Flow
1. If the app has a browser wherein it can present the provider's sign-in page, the app service won't have to use the provider's SDK. The server can then manage the sign-in process, something called **server-flow**.
2. If the app doesn't have a browser e.g. mobile apps, the application manually signs the user in to the provider and submits the auth token to the app service for validation. The application code manages the authentication flow, something called **client-flow**.
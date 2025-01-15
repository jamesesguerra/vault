One of the selling points of web apps is that they con provide you with a dynamic experience customized to individual users. When you sign in, you get a different experience tailored to your account.

You have 2 aspects to consider when adding users to your app:
- **authentication** - the process of creating users and letting them log in your app
- **authorization** - customizing the experience and controlling what users can do based on who the currently logged-in user is

#### Users and Claims
The Kestrel web server creates an `HttpContext` object for every request that comes in. In this object, a `User` property is exposed. This property contains the **principal** for a request. A principal can be thought of as a user of your app. In ASP.NET Core, principals are implemented using the `ClaimsPrincipal` class which has a collection of **claims** such as its email, first name, last name, and whether it has admin access. These are pieces of information about a user.

Kestrel assigns a principal for every request that arrives at your app. But initially, this principal is a generic, unauthenticated principal with no claims.

### Traditional Web App Authentication Process
The same general authentication process applies to all apps
1. The client sends an identifier (email) and a secret (password) to the app to identify as the current user 
2. The app verifies that the identifier is a known user by the app and that their secret is correct
3. The app sets the user as the principal for the current request, and also uses cookies to store an encrypted version of the user for subsequent requests

In subsequent requests, the browser sends the cookie along with the request. When Kestrel sees a new request and sees this cookie, it still doesn't do anything with it and still attaches the unauthenticated `ClaimsPrincipal` class. It's only until the request reaches the `AuthenticationMiddleware` that the current user is set for a request. This piece of middleware reads the cookie, decrypts it, and deserializes it into the claims class. With the user attached, all subsequent pipelines can now read it and alter their behavior accordingly.

Another thing to consider is where to store the authentication details for users. You can load them from your app's database, but that's only one option. Another option is to delegate this responsibility to a third-party identity provider like Okta, Auth0, or Azure AD B2B / B2C.

#### ASP.NET Core Identity
Identity is a system that simplifies building the user-management aspect of your app. It handles all the boilerplate for saving and loading users to a database, as well as best practices for security, password hashing, and MFA.


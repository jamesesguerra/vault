> authentication is the process of establishing the identity of a person, service, or device; they provide a credential that says they are who they say they are

![[image 4.png]]

### single sign-on (sso)
- remember only 1 id and password
- enables a user to sign in once and use that credential to access multiple resources and apps from different providers
- users dont have to remember all those passwords and help desks dont have to be hampered by lockouts and password reset requests

### entra / multifactor authentication (mfa)
- process of prompting a user for an extra form of identification when signing in
- different elements:
  - smth the user knows - a challenging question / password
  - smth the user has - a code that’s sent to their phone
  - smth the user is - biometrics
- increases security by limiting the impact of credential exposure (stolen passwords)
- should be enabled whenever possible

### passwordless authentication
- MFA is not that convenient
- passwordless replaces the password with something you have + something you are / something you know (other than a password)
- needs to be set up on a device such as a computer, which is something you have and is registered to you, now that azure knows its associated with you, you can be authenticated by just using a fingerprint / PIN not a password
- options
  - **windows hello for business**- ideal for workers that have their own designated windows pc; uses biometric and PIN credentials that are tied to the user’s PC
  - **microsoft authenticator** - turns a phone into a passwordless credential by receiving a notification, matching a number, and using biometrics / PIN
  - **fido2 security keys** - fast identity online; open standard for passwordless auth; users can select a hardware device as their security key (such as usb, nfc, bluetooth)
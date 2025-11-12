## microsoft entra id
- directory service that enables you to sign in and access microsoft cloud apps and apps you develop; basically an identity and access management service
- also helps maintain on-prem active directory deployment
- entra id = cloud based active directory w/ extra features (such as detecting sign-in attempts) and using newer not legacy auth (oauth instead of kerberos)

### services
- **authentication** - verifies identity to access applications and resources
- **single sign-on** - enables you to remember only 1 username and password to access multiple apps; a single identity is tied to a user
- **application management** - manage apps for better UX
- **device management** - intune

### microsoft entra connect
- connect on-premises AD with entra ID
- synchronize changes between them and use features under both systems such as SSO, MFA
- managed domain performs 1 way synchronization from entra ID to entra DS
- entra ID can synchronize 2 way with azure ad

## entra domain services
- provides managed legacy domain services such as LDAP (querying), kerberos/ntlm (auth) without having to maintain the infrastructure supporting it (domain controllers)
- you can run legacy apps in the cloud that can’t use modern auth methods / when you don’t want lookups to go all the way back to AD DS
- 2 windows server DCs are deployed in a region (a replica set) and host the domain name / unique namespace you define (stealthmonitoring.com)

### azure ad
- identity provider
- as a user, authenticate to azure ad, many diff apps trust the azure ad = get more features 

## directory services
- AD services instance, domain services = core directory services capability
- accounts-shared database
- facilitated by domain controllers
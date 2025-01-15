Bearer tokens consist of 2 parts that describe its use:
- **token** - a security token that provides access to a protected resource
- **bearer** - a bearer token is one in which anyone who has the token (the bearer) can use it like anyone else; it's a bit like money because when it's in your possession, you can spend it

#### JWT
They don't have to have any particular value, they can be any string. But OpenIDConnect specifies the most common format is a **JWT**. It consists of 3 parts:
- a JSON header describing the token
- a JSON payload containing the claims
- a binary signature created from the header and the payload

Each part is base64-encoded and concatenated with `.` into a single string that can be passed in HTTP headers. The signature is created using key material that must be shared by the provider that created the token to ensure that the JWT can't be tampered with.


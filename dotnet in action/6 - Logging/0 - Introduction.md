**Logging** is the process of recording events or activities in an app, and it often involves writing a record to a console, a file, or some other system.

There are generally 2 kinds of things you can record in a log:
- **informational messages** - a standard event occurred e.g. a user logged in, a new post was created
- **warnings and errors** - an error or unexpected condition occurred

Historically, a common problem with logging in larger applications was that each library and framework would general logs in a slightly different format, making it harder to pinpoint problems. Luckily, ASP.NET Core includes a new generic logging interface that you can plug into. With this logging framework, you can control the verbosity of logs and where they're written to.

#### Reasons to add logs
- to audit or data analytics reasons
- to log an error
- to leave a breadcrumb trail for when an error does happen
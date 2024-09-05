Email is an asynchronous communication medium wherein people can choose to send, read, reply to messages at their own convenience.

Email consists of 3 major parts:
- user agents (Outlook, Apple Mail)
- mail servers
- Simple Mail Transfer Protocol (SMTP)

### The Email process
If Alice wants to send an email to Bob, her email first gets sent to her mail server. Once it gets there, it gets placed in the mail server's outgoing message queue. When her mail server sees this, it tries to establish a TCP connection with Bob's mail server. If it can't connect, it stays in her message queue.

Once the mail is delivered, it goes to Bob's mailbox. If Bob wants to read his email, the mail server fetches Bob's mailbox from his mail server first.

![[email-process.png]]

### SMTP
Email is another client-server application wherein a mail server runs both the client and the server side of SMTP. When it sends emails, it acts as an SMTP client. When it receives email, it acts as an SMTP server. SMTP is the protocol used to send an email from one mail server to another.

Once a TCP connection has been made, the client and server side exchange messages such as `HELO`, `MAIL FROM`, `RCPT TO`, `DATA`, and `QUIT`.

#### HTTP vs SMTP
Both application layer protocols are similar in that they're both used to transfer something. HTTP transfers objects while SMTP transfers email messages. They also both use TCP with persistent connections. However, a big difference is that HTTP is a **pull protocol** ie Web servers are filled with content, and then HTTP clients can pull the content at their own convenience. SMTP, on the other hand, is a **push protocol** since clients are the ones pushing the content to the server. Therefore, the one that initiates the connection in this case is the one who wants to send a file.

Another difference is that HTTP doesn't require its entity body to be in 7-bit ASCII, whereas SMTP does, and so it requires some encoding if its content isn't already in ASCII.

#### Header lines
Like HTTP, SMTP also uses header lines to outline the data in a message. The `To:` and `From:` header lines are required.

```
From: alice@crepes.fr
To: bob@hamburger.edu
Subject: Searching for the meaning of life.

<message-body>
```

### POP3 and IMAP
SMTP is used to push emails to your own / your recipient's mail server. But if the recipient wants to read their mail, they can't do that from their user agent with SMTP because it's a push protocol.

This is where the protocols POP3 and IMAP come in, protocols used to transfer messages from a mail server to a user agent.

**POP3** is a fairly simply and trivial protocol used for pulling email from an email server. **IMAP** is more complicated in that it provides more features, such as the ability to create remote folders and assign messages to folders, as well as the ability to fetch certain parts of an email only. Another difference between POP3 and IMAP is that POP3 doesn't maintain user state information whereas IMAP does.


![[bob-alice-process.png]]

#### Web-based email
HTTP can also be used to push and pull content to / from a web server.

using SMTP, and POP3 / IMAP:
`alice ---SMTP--> alice mail server ---SMTP--> bob mail server ---POP3 / IMAP--> bob`

using SMTP and HTTP
`alice ---HTTP--> alice mail server ---SMTP---> bob mail server ---HTTP---> bob`
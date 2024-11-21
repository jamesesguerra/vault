To protect your users, your app should encrypt the traffic between the user's browser and your app as it travels over the network by using the HTTPS protocol. This is similar to HTTP but it uses an SSL/TLS certificate to encrypt requests and responses so attackers can't read or modify the contents.

To enable HTTPS, you need to obtain and configure a TLS certificate for your server. This is made easy by Let's Encrypt, and also cloud providers with a one-click process, and Cloudflare with its CDN service.

#### SSL / TLS Offloading
When you have a reverse proxy sitting in front of your app, your app can often get away with not using HTTPS. This is because the message can be encrypted all the way to the reverse proxy, which decrypts the data and forwards the unencrypted message to your app so your app doesn't have to do the decryption.

![[tls-offloading.png]]

This isn't the only option though when using HTTPS with a reverse proxy. You can also use **SSL / TLS passthrough** which encrypts the data all the way to your app.

#### 2 approaches

##### Supporting TLS in your app
Depending on your infrastructure, TLS could be offloaded to a dedicated device on your network, or a third-party service like Cloudflare, or a reverse proxy like Nginx. But in some cases, you may need to handle TLS directly in your app for a few reasons:
- if you're exposing Kestrel directly to the internet
- if having HTTP between the proxy and your app is unacceptable
- if you're using technology that requires HTTPS e.g. gRPC

In each of these scenarios, you'll need to configure a TLS certificate for your app so Kestrel can receive HTTPS traffic.

**Development certificates**
The first time you run a `dotnet` command, the .NET SDK installs an HTTPS dev certificate on your machine. Any .NET app you create with templates use this certificate. However, it isn't trusted by default, so you have to "force" this trust. It's like saying "I know this certificate doesn't look quite right, but ignore that".  You can do that with this command:

```sh
dotnet dev-certs https --trust
```




##### SSL / TLS Offloading
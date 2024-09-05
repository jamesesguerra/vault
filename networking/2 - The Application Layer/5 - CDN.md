A **content distribution network (CDN)** manages the distribution of static content (documents, images, audio, and video) in multiple geographically distributed locations to direct users to a CDN location that will provide the best experience. The goal of a CDN is to lessen the links and routers between a server and a client because content such as videos require a high access link bit rate. By lowering the number of links, the likelihood that the end-to-end throughput will be lower than the consumption rate becomes lower as well.

### How CDNs work
Let's say you have a content provider Google that employs CDN provider Cloudflare to distribute its videos to their customers.

1. Each video is assigned a URL that can be requested by HTTP clients. This URL contains a subdomain "video" and a unique identifier. For example: `https://video.google.com/6Y8VB0JK`

2. The HTTP request uses DNS to resolve the `google.com` subdomain. The local DNS (LDNS) queries the authoritative server for Google, which then observes the "video" prefix in the URL.

3. It uses this prefix to "handover" the request to the CDN by returning an NS record instead of an A record. The value of the NS record would be the hostname of Cloudflare's CDN, for example, a1105.cloudflarecdn.com.

4. From here, the request enters Cloudflare CDN's infrastructure and takes control. It will eventually return an A record to one of Cloudflare's content servers.

5. The LDNS forwards the IP address of the content server to the client.

6. The client establishes a TCP connection with the Cloudflare CDN content server.

![[cdn-process.png]]

#### Selection strategy
The cluster / content distribution server's IP address to be returned by the CDN authoritative DNS server is determined by a selection strategy. Most strategies are proprietary but in general, the DNS server uses a location database to determine the geographically closest content distribution server to the LDNS.

However in some cases, the network between the content distribution server and the LDNS may not be the fastest. For those cases, the CDN's can use a periodic real-time measurement by probing LDNS servers.
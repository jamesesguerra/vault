> storage account provides a unique namespace for your azure storage data that’s accessible from anywhere in the world over HTTP/S

## storage services
### blob storage
- object storage to store massive amounts of unstructured data such as text / binary data
- ideal for serving images to a browser, archives, streaming video/audio, DR, 
- includes support for big data analytics via azure data lake gen2
- types
  - block blob - object storage
  - page blob - random read/write access
  - append blob - append at the end

### file storage
- fully-managed file shares in the cloud
- like connecting to shares on your local network

### disk storage
- provides disks for VMs and apps to access and use as they need
- SSD and HDD

### table storage
- nosql data store for key-value pairs using large-scale datasets
- use this to store petabytes of semi-structured data

### queue storage
- asynchronous message queuing for communication between application components
- communication cloud, desktop, mobile devices, on-prem

## blob storage tiers
only hot and cool tiers are available at the account level 
- **hot** - accessed frequently e.g. images via website
- **cool** - accessed infrequently, stored for at least 30 days e.g. customer invoices
- cold - accessed infrequently, stored for at least 90 days
- **archive** - accessed rarely, stored for at least 180 days e.g. long-term backups

## storage account types
### standard
- blob (including data lake), queue, table, azure files
- lrs, grs, ra-grs, zrs, gzrs, ra-gzrs
- recommended for most scenarios; use premium file shares if you need support for network file system (NFS)

### premium block blob
- blob storage (including data lake), block and append blobs
- lrs and zrs
- recommended for scenarios with high transaction rates or that use smaller objects or require low storage latency

### premium file shares
- azure file shares only
- lrs and zrs
- recommended for enterprise or high-performance scale apps; use if you want a storage account that supports Server Message Block (SMB) and NFS file shares

### premium page blobs
- page blobs only
- lrs

## storage account endpoints
- one of the benefits of azure storage accounts is having a unique namespace in azure for your data
- namespace: `https://<storage-account-name>.<azure-service-endpoint>`

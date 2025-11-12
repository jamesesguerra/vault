> azure storage always stores multiple copies ( at least 3 copies ) of your data for high availability and durability

## redundancy in the primary region
### lrs
- replicates data 3 times within a single datacenter in the primary region
- lowest cost, least durability

### zrs
- replicates data synchronously across 3 availability zones in the primary region
- data is accessible for read write operations even if a zone becomes unavailable, azure takes care of remounting and networking updates

## redundancy in the secondary region
- paired region of the primary region of the storage account is used as the backup region (can’t change this)
- data isn’t available in the secondary region by default unless you failover to it, in which case it becomes the new primary region
### grs
- lrs in both primary and secondary region

### gzrs
- zrs in primary and lrs in secondary

### ra-grs and ra-gzrs
- even if primary region is running optimally i.e. you haven’t failed over to secondary region, you can still read data from the secondary region
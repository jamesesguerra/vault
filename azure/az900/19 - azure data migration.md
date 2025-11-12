> understanding how to get your data and information into azure

## azure migrate
- movement of VMs, DBs etc.
- real-time migration service to migrate from an on-prem environment to the cloud
- also functions as a hub to help manage assessment of migration
- provides:
  - unified migration platform - single portal to start and track migration
  - range of tools - Discovery and Assessment, Server Migration
  - hub to assess and migrate on-prem infrastructure
  - investigates VMs / workloads, assesses it, recommend right SKU to use in azure

## azure data box
- offline movement
- **disk**
  - import only; not export
  - azure sends physical disks, copy the data, ship it back, bring it into storage account
- **box**
  - import / export
  - 50 lbs 80 tb
  - ship box, plug into network, copy into it using smb/nfs, specify storage account
- **box heavy**
  - 500 lbs 770tb
  - huge amount of data to move online

--- 
# azure file movement
> small scale data movement 

## az copy
- useful for automation scenarios
- can be used to copy/sync data
- copies between different storage accounts / different cloud providers

## azure storage explorer / browser
- GUI for adhoc interactions of uploading/downloading folders and files
- interact with storage services and accounts visually
- small amount of data

## azure file sync
- create sync group -> register server endpoints (windows file servers) -> have 1 cloud endpoint via azure files -> put server endpoints into sync group
- all of the file shares synchronize/replicate to the file share in azure
- cloud tiering - offload least accessed data to azure files if limit is reached
- good solution for moving small data, synchronized windows-based file servers
<TODO> fix 

Storage can be divded into: 
1) Object Storage: storage is managed in terms of objects (ie. files). Objects are grouped into buckets, with each object being addressable by a URL. Object storage is not tied to any server or disk, is serverless. Objects can be stored in a replicated fashion for high availbility and durability. Does not support OS or file system-level access (use protocols like HTTP to interact with the objects) ex. Cloud Storage 
2) File Storage: a hierchial file storage system, similar to an operating system-like file access. ex. Cloud Filestore (a NFS (Network File System) storage system) 
3) Block Storage: storage is organized as blocks (a fixed data structure). A file system is applied on top of the block level storage to map files onto a sequence of blocks. Available on disks attached to VMs.
4 KB is a common block size in Linux, however block storage size can vary. 
4) Cache: volatile in memory data store for speedy access to data (low latency) 



can use gsutil to interact with cloud Storage buckets #TODO: look into

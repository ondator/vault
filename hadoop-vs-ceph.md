# HDFS vs Object Storages

Here is some comparision in proc and cons format between HDFS(distributed file system from Hadoop ecosystem) and S3-like Object Storage Systems(Amazon S3, Azure Blob Storage and Ceph)

| HDFS | S3 |
|------|----|
| - works effectively only with files larger than block size | + can work effictively with all file size(has no block size)|
| + has large ecosystem over it | + a lot big data tools works with S3 as well as hdfs|
| + out of the box crosskey consistency guarantees | |
| + has no single point of failure because name node autofailover | + has no single point of failure (name node)|
| - fucked up on large files or huge amount of small files | - fucked up on huge amount of small files (minio\ceph) |
| - append only | |




Why would OpenStack instances fail to wget a URL and work perfectly on others ? For instance:

> $ wget -O - 'https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/autobuild.asc'
  Connecting to ceph.com (ceph.com)|208.113.241.137|:443... connected.
  HTTP request sent, awaiting response... 200 OK
  Length: unspecified [text/plain]
  Saving to: `STDOUT'
  
  [<=>      

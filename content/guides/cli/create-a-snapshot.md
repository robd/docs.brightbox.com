---
layout: default
title: Creating a snapshot
section: CLI
---

Snapshotting a server takes an instant copy of the server's disk which
can then be used as the [Image](/reference/server-images/) for new
servers.

The snapshots can also be downloaded from the image library.

### Create the snapshot

Snapshots are created using the `brightbox servers snapshot`
command. Just give it the identifier of the server you want to snapshot:

    $ brightbox servers snapshot srv-gwgk2
    Snapshotting server srv-gwgk2

### View the snapshot image info

Taking the snapshot is instantaneous, but it then takes a little while
to be copied into the image library depending upon the amount of data
on the server. You can see the resulting image in the image list:

    $ brightbox images list
    
     id         owner      type      created_on  status    size   name                                       
    ----------------------------------------------------------------------------------------------------------
     img-99q79  brightbox  official  2010-10-02  public    10244  CentOS 5.5 server (i686)                   
     img-pnqnc  brightbox  official  2010-10-03  public    10240  CentOS 5.5 server (x86_64)                 
     img-qjuex  brightbox  official  2010-10-14  public    10244  Fedora 14 Beta Base (i686)                 
     img-vyeeg  brightbox  official  2010-10-15  public    10244  Fedora 14 Beta Base (x86_64)               
     img-1okdf  brightbox  official  2010-11-19  public    20480  FreeBSD 8.1 minimal (i686)                 
     img-aoubd  brightbox  official  2010-11-19  public    20480  FreeBSD 8.1 minimal (x86_64)               
     img-tjjt6  brightbox  official  2010-09-27  public    1409   Ubuntu Lucid 10.04 server (x86_64)         
     img-hm6oj  brightbox  official  2010-10-01  public    1409   Ubuntu Lucid 10.04 server  (i686)          
     img-9vxqi  brightbox  official  2010-10-14  public    1409   Ubuntu Maverick 10.10 server (i686)        
     img-t4p09  brightbox  official  2010-10-14  public    1409   Ubuntu Maverick 10.10 server (x86_64)      
     img-y22uq  acc-h3nbk  snapshot  2010-12-19  creating  20480  Snapshot of srv-gwgk2 19 Dec 12:57 (i686)  
    ----------------------------------------------------------------------------------------------------------

The snapshot has been given the image identifier `img-y22uq` and is
currently creating. The name field indicates the image is the result
of a snapshot of `srv-gwgk2`.

    $ brightbox images show img-y22uq 
                    id: img-y22uq
                  type: snapshot
                 owner: acc-h3nbk
            created_at: 2010-12-19T12:57:37Z
                status: private
                  arch: i686
                  name: Snapshot of srv-gwgk2 19 Dec 12:57 (i686)
           description: 
          virtual_size: 20480
             disk_size: 421
    compatibility_mode: false
              official: false
           ancestor_id: img-9vxqi

Here you can see that the snapshot copy has now completed and the
status is private, which means only your account has access to it. The
virtual_size is how big the entire disk is and the disk_size is how
big the actual snapshot is. The disk_size will vary depending on how
much data is written to your server.

Snapshots are limited to a maximum disk_size of 50GB. The system aborts
snapshot requests that try to create images bigger than that, and sets
the status to 'failed'.

### Create a new server using the snapshot

The snapshot image can be used like any other image. You can build a
clone of the original server like this:

    $ brightbox servers create -n "myclone" img-y22uq
    Creating a nano (typ-4nssg) server with image Snapshot of srv-gwgk2 19 Dec 12:57 (img-y22uq)
    
     id         status    type  zone   created_on  image_id   cloud_ips  name   
    -----------------------------------------------------------------------------
     srv-v2fym  creating  nano  gb1-b  2010-12-19  img-y22uq             myclone
    -----------------------------------------------------------------------------

The new server will be given a new identifier and IP address, but
otherwise will be identical to the one you originally snapshotted.


### Downloading a copy of the image 

The snapshot image is automatically available for download from the
image library via ftp. This allows you to take a copy of a server to
run on your local development environment, or to migrate it to another
host.

You'll need your image library ftp credentials (see the [Image Library
guide](/guides/cli/image-library/) for more details).

    $ lftp acc-h3nbk@ftp.library.gb1.brightbox.com
    Password: xxxxxxxxxxxx
    lftp acc-h3nbk@ftp.library.gb1.brightbox.com:/> cd images
    lftp acc-h3nbk@ftp.library.gb1.brightbox.com:/images> ls -h
    -rw-r--r--   1 acc-h3nbk library    420.1M Dec 19 13:00 img-y22uq
    lftp acc-h3nbk@ftp.library.gb1.brightbox.com:/images> get img-y22uq 

The image is in `qcow2` format. You can boot it directly using kvm
virtualisation software, or use the `qemu-img` tool to convert it into
other formats.

## Would you like to know more?  

Here you created a snapshot, then created a new server using the
snapshot image and finally downloaded the image via ftp.

You might want to learn more about how to [upload your own image to the
image library](/guides/cli/image-library/).

---
layout: post
category: cloudstack
highlight: primary
title: Building CloudStack SystemVMs
redirect_from: "/logs/building-systemvms/"
---

CloudStack uses virtual appliances as part of its orchestration. For example, it
uses virtual routers for SDN, secondary storage vm for snapshots, templates etc.
All these service appliances are created off a template called a systemvm
template in CloudStack's terminologies. This template appliance is patched to create
secondary storage vm, console proxy vm or router vm. There was an old way of building
systemvms in `patches/systemvm/debian/buildsystemvm.sh` which is no longer maintained
and we wanted to have a way for hackers to just build systemvms on their own box.

[James Martin](mailto:jmartin@basho.com) did a great job on automating DevCloud appliance
building using [veewee](https://github.com/jedi4ever/veewee/), a tool with
which one can build appliances on VirtualBox. The tool itself is easy to use, you
first define what kind of box you want to build, configure a preseed file and add
any post installation script you want to run, once done you can export the appliance in
various formats using `vhd-util`, `qemu-img` and `vboxmanage`. I finally fixed a
solution to this [problem](https://issues.apache.org/jira/browse/CLOUDSTACK-1066)
today and the code lives in `tools/appliance` on master branch but this post is
not about that solution but about the issues and challenges of [setting up an
automated jenkins job](http://jenkins.cloudstack.org/job/build-systemvm-master)
and on replicating the build job.

I used Ubuntu 12.04 on a large machine which runs a jenkins slave and connects
to `jenkins.cloudstack.org`. After little housekeeping I installed VirtualBox from
`virtualbox.org`. VirtualBox comes up with its command line tool, `vboxmanage`
which can be used to clone, copy and export appliance. I used it to export it to
ova, vhd and raw image formats. Next, installed qemu which gets you `qemu-img` for
exporting a raw disk image to the qcow2 format.

The VirtualBox vhd format is compatible to HyperV virtual disk format, but for
exporting VHD for Xen, we need to export the appliance to raw disk format and
then use `vhd-util` to convert it to Xen VHD image.

Unfortunately, the vhd-util [I got did not work for me](http://download.cloud.com.s3.amazonaws.com/tools/vhd-util),
so I just compiled my own from an approach suggested on [this blog](http://blogs.citrix.com/2012/10/04/convert-a-raw-image-to-xenserver-vhd/):

    sudo apt-get install bzip2 python-dev gcc g++ build-essential libssl-dev
    uuid-dev zlib1g-dev libncurses5-dev libx11-dev python-dev iasl bin86 bcc
    gettext libglib2.0-dev libyajl-dev
    # On 64 bit system
    sudo apt-get install libc6-dev-i386
    # Build vhd-util from source
    wget -q http://bits.xensource.com/oss-xen/release/4.2.0/xen-4.2.0.tar.gz
    tar -xzf xen-4.2.0.tar.gz
    cd xen-4.2.0/tools/
    wget https://github.com/citrix-openstack/xenserver-utils/raw/master/blktap2.patch -qO - | patch -p0
    ./configure --disable-monitors --disable-ocamltools --disable-rombios --disable-seabios
    cd blktap2/vhd
    make -j 2
    sudo make install

Last thing was to setup rvm for the jenkins user:

    $ \curl -L https://get.rvm.io | bash -s stable --ruby
    # In case of dependency or openssl error:
    $ rvm requirements run
    $ rvm reinstall 1.9.3

One issue with `rvm` is that it requires a login shell, which I fixed in `build.sh`
using `#!/bin/bash -xl`. But the build job failed for me due to missing env variables.
`$HOME` needs to be defined and rvm should be in path. The shell commands used to
run the jenkins job:

    whoami
    export PATH=/home/jenkins/.rvm/bin:$PATH
    export rvm_path=/home/jenkins/.rvm
    export HOME=/home/jenkins/
    cd tools/appliance
    rm -fr iso/ dist/
    chmod +x build.sh
    ./build.sh

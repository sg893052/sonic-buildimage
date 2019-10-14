## SONiC Package

A SONiC installer image (e.g sonic-broadcom-cloud-base.bin) is a self extracting firmware file which contains the branding and version information embedded in it. As the SONiC installer image is a single binary file, it not possible to change the branding information without unpacking it, modifying it and repacking it. This results in a different binary file compared to the one released by Broadcom.

However, some customers who receive just the binary image from Broadcom would want some flexibility to influence some changes to the SONiC installer file.

This is made possible by creating a SONiC package which is also a self extracting binary image. The SONiC package file can be used to install SONIC image using ONIE and the sonic\_installer command just like the SONiC installer file. SONiC package file is typically named with *.pkg* file extension (e.g sonic-cloud-base.pkg)*.*

A new tool *build\_pkg.sh* is available to create a SONiC package file. The *build\_pkg.sh* is available in the source code as
*sonic-buildimage/build\_pkg.sh.*

Below is an example to create a SONiC package file to include a user provided *sonic\_branding.yml* which contains user defined Product Name.

**Command Usage:**

```
build_pkg.sh target/sonic-broadcom-cloud-base.bin sonic_branding.yml
```

**Example Usage:**

```
bash-4.2$ cat sonic_branding.yml
product_name: SONiC by ACME Corporation Cloud Standard

bash-4.2$ build_pkg.sh target/sonic-broadcom-cloud-base.bin sonic_branding.yml
Creating a package file to combine target/sonic-broadcom-cloud-base.bin and sonic_branding.yml
Successfully created package file: sonic-broadcom-cloud-base.pkg
```
When user installs the sonic-broadcom-cloud-base.pkg, the sonic\_branding.yml in /etc/sonic directory of the image is replaced with the one provided by user and thus *show version* shows the product
name chosen by the user.

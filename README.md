# AMD Sensor Fusion Hub backport for Linux

The AMD Sensor Fusion Hub (SFH) is used in Ryzen-based laptops to support sensors
such as the accelerometer.  The driver for this hardware was only merged in Linux 5.11,
which is too recent to be available in distributions.

This repository is a backport that allows to build the driver as an out-of-tree module.
It should work on any distribution: it was tested on Debian bullseye and on Ubuntu 20.04.

Hopefully, a proper backport will be done for stable kernels, but in the meantime,
this can be useful.


## How to build

Make sure you have headers installed for your kernel (`linux-headers-something` package).

Then build the module:

    DRIVER=$(realpath drivers/hid/amd-sfh-hid)
    make -C /lib/modules/$(uname -r)/build M=$DRIVER CONFIG_AMD_SFH_HID=m

Install the module:

    sudo make -C /lib/modules/$(uname -r)/build M=$DRIVER CONFIG_AMD_SFH_HID=m modules_install

Regenerate modules and load the new module:

    sudo depmod -a
    sudo modprobe amd_sfh

To rebuild for a new kernel (needs to be done on each kernel update), start by cleaning:

    DRIVER=$(realpath drivers/hid/amd-sfh-hid)
    make -C /lib/modules/$(uname -r)/build M=$DRIVER CONFIG_AMD_SFH_HID=m clean

and then build again.


## How this backport was made

List of commits in upstream kernel:

    4f567b9f8141a86c7d878fadf136e5d1408e3e61
    4b2c53d93a4bc9d52cc0ec354629cfc9dc217f93
    1434f9fc0e476362f3f3d153d48c0fc5c940a50a
    4b393f0f76c8e3ce80b3bd524dc40bd674a29101
    6e6eae04f5123b7b2f4265f7a702b5200fa5863b
    de30491e8bfeeba1500bba293333eb51ece529d5

To apply each commit, run this from the kernel repository:

    git format-patch -1 --stdout $commit drivers/hid/amd-sfh-hid/ | git -C ~/code/amd-sfh-hid-backport/ am

Using git-filter-repo would be nicer, but it's too slow to handle the whole Linux repository.


## License

Same as the original code (GPL-2.0-or-later), this is just a backport.

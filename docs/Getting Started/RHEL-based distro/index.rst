RHEL-based distro
=======================

Contents
--------
.. toctree::
  :maxdepth: 1
  :glob:

  *

`DKMS`_ or `kABI-tracking kmod`_ style packages are provided for RHEL and
CentOS based distributions from the OpenZFS repository. These packages are
updated as new versions are released. Only the current repository for each
major release is updated with new packages. Packages are available for the
following configurations:

| **EL Releases:** 6, 7.9, 8.6, 9.0
| **Architectures:** x86_64

To simplify installation a *zfs-release* package is provided which includes
a zfs.repo configuration file and public signing key. All official OpenZFS
packages are signed using this key, and by default yum or dnf will verify a
package's signature before allowing it be to installed. Users are strongly
encouraged to verify the authenticity of the ZFS on Linux public key using
the fingerprint listed here.

| **Location:** /etc/pki/rpm-gpg/RPM-GPG-KEY-zfsonlinux
| **Archived Repositories:** `see repo page <https://github.com/zfsonlinux/zfsonlinux.github.com/tree/master/epel>`__

| **Signing key1 (Centos 8 and older, Fedora 36 and older)**
  `direct link <https://raw.githubusercontent.com/zfsonlinux/zfsonlinux.github.com/master/zfs-release/RPM-GPG-KEY-openzfs-key1>`__
| **Fingerprint:** C93A FFFD 9F3F 7B03 C310 CEB6 A9D5 A1C0 F14A B620

| **Signing key2 (Centos 9+, Fedora 37+)**
  `direct link <https://raw.githubusercontent.com/zfsonlinux/zfsonlinux.github.com/master/zfs-release/RPM-GPG-KEY-openzfs-key2>`__
| **Fingerprint:** 7DC7 299D CF7C 7FD9 CD87 701B A599 FD5E 9DB8 4141

For RHEL/CentOS versions 6 and 7 run::

 yum install https://zfsonlinux.org/epel/zfs-release-2-2$(rpm --eval "%{dist}").noarch.rpm

And for RHEL 8-9::

 dnf install https://zfsonlinux.org/epel/zfs-release-2-2$(rpm --eval "%{dist}").noarch.rpm

After installing the *zfs-release* package and verifying the public key
users can opt to install either the DKMS or kABI-tracking kmod style packages.
DKMS packages are recommended for users running a non-distribution kernel or
for users who wish to apply local customizations to OpenZFS.  For most users
the kABI-tracking kmod packages are recommended in order to avoid needing to
rebuild OpenZFS for every kernel update.

DKMS
----

To install DKMS style packages issue the following commands. First add the
`EPEL repository`_ which provides DKMS by installing the *epel-release*
package, then the *kernel-devel* and *zfs* packages. Note that it is
important to make sure that the matching *kernel-devel* package is installed
for the running kernel since DKMS requires it to build OpenZFS.

For RHEL/CentOS versions 6 and 7, separately run::

 yum install -y epel-release
 yum install -y kernel-devel
 yum install -y zfs

And for RHEL/CentOS 8 and newer, separately run::

 dnf install -y epel-release
 dnf install -y kernel-devel
 dnf install -y zfs

.. note::
   When switching from DKMS to kABI-tracking kmods first uninstall the
   existing DKMS packages. This should remove the kernel modules for all
   installed kernels, then the kABI-tracking kmods can be installed as
   described in the section below.

kABI-tracking kmod
------------------

By default the *zfs-release* package is configured to install DKMS style
packages so they will work with a wide range of kernels. In order to
install the kABI-tracking kmods the default repository must be switched
from *zfs* to *zfs-kmod*. Keep in mind that the kABI-tracking kmods are
only verified to work with the distribution provided kernel.

For RHEL/CentOS versions 6 and 7 run::

 yum-config-manager --disable zfs
 yum-config-manager --enable zfs-kmod
 yum install zfs

And for RHEL/CentOS 8 and newer::

 dnf config-manager --disable zfs
 dnf config-manager --enable zfs-kmod
 dnf install zfs

By default the OpenZFS kernel modules are automatically loaded when a ZFS
pool is detected. If you would prefer to always load the modules at boot
time you must create an ``/etc/modules-load.d/zfs.conf`` file::

 echo zfs >/etc/modules-load.d/zfs.conf

.. note::
   When updating to a new RHEL/CentOS minor release the existing kmod
   packages may not work due to upstream kABI changes in the kernel.
   After upgrading users must uninstall OpenZFS and then reinstall it
   from the matching repository as described in this section.

It might be necessary to rebuild ZFS module::

 for directory in /lib/modules/*; do
   kernel_version=$(basename $directory)
   dkms autoinstall -k $kernel_version
 done

If for some reason, ZFS kernel module is not successfully built,
you can also run the above command to debug the problem.

Testing Repositories
--------------------

In addition to the primary *zfs* repository a *zfs-testing* repository
is available. This repository, which is disabled by default, contains
the latest version of OpenZFS which is under active development. These
packages are made available in order to get feedback from users regarding
the functionality and stability of upcoming releases. These packages
**should not** be used on production systems. Packages from the testing
repository can be installed as follows.

For RHEL/CentOS versions 6 and 7 run::

 yum-config-manager --enable zfs-testing
 yum install kernel-devel zfs

And for RHEL/CentOS 8 and newer::

 dnf config-manager --enable zfs-testing
 dnf install kernel-devel zfs

.. note::
   Use *zfs-testing* for DKMS packages and *zfs-testing-kmod*
   kABI-tracking kmod packages.

RHEL-based distro Root on ZFS
-------------------------------
Start from "Preparation".

.. toctree::
  :maxdepth: 1
  :glob:

  RHEL-based distro Root on ZFS/*

.. _kABI-tracking kmod: https://elrepoproject.blogspot.com/2016/02/kabi-tracking-kmod-packages.html
.. _DKMS: https://en.wikipedia.org/wiki/Dynamic_Kernel_Module_Support

.. _EPEL repository: https://fedoraproject.org/wiki/EPEL

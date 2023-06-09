===================================================
 VCT desktop RaspberryPi, AllwinnerPi BSP Manifest
===================================================

The various branches available here will configure a mortsgna distro repo build
for the appropriate branches in each repository and clone them in the typical fashion,
ie, with either poky or oe-core as the parent directory and the additional metadata
layers underneath (as documented in the upstream setup).

The main branches build a mortsgna reference distro on oe-core with your choice
of machine.  Note that oe-core will build "distroless" by default, however, you should set
DISTRO = "mortsgna" in your local.conf to build the associated desktop images.

machine variants
----------------

Current machine variants on kirkstone branch:

* supported rpi devices (rpi/rpi2/rpi3/rpi0/rpi4/rpi-cm)
* select orangepi, nanopi, bananapi devices
* various a20 - a50 H3/H616 Allwinner-based devices
* see `meta-sunxi/conf/machine`_ for default MACHINE names

.. _meta-sunxi/conf/machine: https://github.com/linux-sunxi/meta-sunxi/tree/master/conf/machine


Build branches
--------------

Select the build branch using the github branch button above, which will select the
correct manifest branches and BSP/metadata using the respective branches in this
repo as shown below.

Follow the steps in the readme for the branch you select, then use the same branch
name with the "repo init" command.  This will checkout the
corresponding branch HEAD commits on the configured branch for each repository.

You can get status and more using the ``repo`` command in the top level directory
once you've run the ``repo init`` and ``repo sync`` commands.  Try::

  $ repo info
  $ repo show
  $ repo status

See the default.xml file for repo and branch details; if this is the 'main'
branch, then the following example is generic so go back and select a build
branch from this repo.

Install the repo utility
------------------------

::

  $ mkdir ~/bin
  $ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
  $ chmod a+x ~/bin/repo

Create the BSP workspace
------------------------

::

  $ PATH=${PATH}:~/bin
  $ mkdir clonepi-bsp
  $ cd clonepi-bsp
  $ repo init -u https://github.com/VCTLabs/vct-mortsgna-distro-bsp-platform -b oe-kirkstone
  $ repo sync

At the end of the above commands you have all the metadata you need to start
building with poky and meta-oe on kirkstone branches.

Update existing workspace
-------------------------

In order to bring all the repositories up to date with upstream::

  $ cd clonepi-bsp
  $ repo sync

If you have local changes, you might also need::

  $ repo rebase

Repo tips
---------

Some info on how to customize your sync:

  | ``-j JOBS, --jobs=JOBS``  projects to fetch simultaneously (default 4)
  | ``--no-clone-bundle``     disable use of /clone.bundle on HTTP/HTTPS
  | ``--no-tags``             don't fetch tags

Fastest full sync::

  $ repo sync --no-tags --no-clone-bundle

Smallest/fastest sync::

  $ repo init -u https://github.com/VCTLabs/vct-mortsgna-distro-bsp-platform -b oe-kirkstone --no-clone-bundle --depth=1
  $ repo sync --no-tags --no-clone-bundle --current-branch

Configure and build
-------------------

To start a simple image build for a specific target::

  $ cd oe-core
  $ source ./oe-init-build-env build-dir  # you choose name of build-dir
  $ ${EDITOR} conf/local.conf             # set MACHINE to <your_machine> (see above)
  $ ${EDITOR} conf/bblayers.conf          # only use ONE BSP layer here (see example below)
  $ bitbake core-image-minimal

In the above example, <your_machine> should be something like ``orange-pi-pc`` (an 
Allwinner H3). Example ``bblayers.conf`` for an Allwinner pi board::

  # LAYER_CONF_VERSION is increased each time build/conf/bblayers.conf
  # changes incompatibly
  LCONF_VERSION = "7"

  BBPATH = "${TOPDIR}"
  BBFILES ?= ""

  OEROOT = "${HOME}/build/clonepi-bsp/oe-core"

  BBLAYERS ?= " \
    ${OEROOT}/meta \
    ${OEROOT}/meta-small-arm-extra \
    ${OEROOT}/meta-sunxi \
    ${OEROOT}/meta-openembedded/meta-oe \
    ${OEROOT}/meta-openembedded/meta-networking \
    ${OEROOT}/meta-openembedded/meta-python \
    "

You can use any directory (build-dir above) to host your build. The above
commands will build an image for <your_machine> using the BSP
machine config and the default mainline linux kernel.

The full source code tree is checked out in the bsp dir above, and the build
output dir will default to oe-core/build-dir unless you choose a different
path above.

Source code
-----------

Download the manifest source here::

  $ git clone https://github.com/VCTLabs/vct-mortsgna-distro-bsp-platform

Using Development and Testing/Release Branches
----------------------------------------------

Replace the repo init command above with one of the following:

For developers - hardknott

::

  $ repo init -u https://github.com/VCTLabs/vct-mortsgna-distro-bsp-platform -b oe-hardknott

For intrepid developers and testers - master

Patches are typically merged into master-next and then are merged into master
after a testing and comment period. It’s possible that master has
something you want or need.  But it’s also possible that using master
breaks something that was working before.  Use with caution.

::

  $ repo init -u https://github.com/VCTLabs/vct-mortsgna-distro-bsp-platform -b oe-master



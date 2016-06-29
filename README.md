# Openstack Development Workflow Primer

## Purpose
This document is a primer for new engineers who wants to start contributing to the  development of Openstack charms and Juju. 

## TABLE OF CONTENTS

##### OpenStack Charms Development Primer
##### Release Information
##### Canonical Openstack Product Information
##### Test Tools
##### Release Management
##### Package Management
##### Building
##### Dependencies
##### Updating
##### Release Package Status
##### Repositories
##### Ecosystem
##### BLOGs, Youtube, etc.

## OpenStack Charms Development Primer

The purpose of the document is to provide developers a single and easy method in understanding and then contributing to the Canonical Openstack charm development. In order to ramp up on the contribution, one needs to understand the whole process from tooling, testing, and then packaging.

1. Setup Ubuntu Desktop on a laptop using LXD. 
  * Current release is the best and most accurate way of developing with Juju and Openstack Charms.
  * Ubuntu OS Location: https://wiki.ubuntu.com/XenialXerus/ReleaseNotes 
  * LXD Introduction and Setup Guide: https://linuxcontainers.org/lxd/ 
  * LXD is Canonical’s hypervisor that will enable users to setup Openstack on their laptop. 
  * Nova-LXD Introduction and Setup Guide: https://linuxcontainers.org/lxd/getting-started-openstack/ 
  * Conjure-Up: https://insights.ubuntu.com/2016/06/09/conjure-up-an-introduction/ 
     * Easy way to get Openstack up and running using this new tool released in April.

2. Target Releases for quickstart development as of April 2016:
  * Ubuntu OS 16.04 (Xenial Xerus)
  * Openstack Mitaka
  * Juju 2.0
  * Openstack Charms 16.04

3. Meetings
  * Openstack Charms Team has a bi-weekly IRC meetings on Wednesdays- https://wiki.ubuntu.com/ServerTeam/OpenStackCharmsMeeting 
  * IRC Channel: #ubuntu-meeting
4. IRC: Download an irc client for your laptop
  * External IRC Servers and Channels
    * #juju
    * #juju-dev
    * #openstack - new user/ openstack component centric channel
    * #openstack-dev - detail development discussion of openstack components
    * #openstack-charms (Coming Soon) [RECOMMENDED] - channel to discuss openstack charming and charm items.
    * #ubuntu-meeting
    * #lxcontainers
    * #ubuntu-server
5. Mailing List
  * openstack@lists.openstack.org
   * General User Questions
   * Many projects use this mailing list.  For Charm-specific discussion, begin each message subject with [charms] to reach the desired audience, and to separate mailing list traffic.
  * openstack-dev@lists.openstack.org
   * Specific OpenStack Charm Questions and Openstack Component Development
   * Many projects use this mailing list.  For Charm-specific discussion, begin each message subject with [charms] to reach the desired audience, and to separate mailing list traffic.
  * juju@lists.ubuntu.com
   * General Juju Questions
  * juju-dev@lists.ubuntu.com
   * Specific Juju development Questions
6. Juju Development - https://jujucharms.com/docs/stable/getting-started 
7. Read Opensource/Openstack Developers Guide - http://docs.openstack.org/infra/manual/developers.html# 
8. Developing Openstack Charms - https://github.com/openstack-charmers/openstack-community/
9. Setup Launchpad profile which includes the GPG and SSH key
  * Need keys to be setup to login to systems within Canonical and the Opensource community.
  * GPG keys are used to identify you and to verify that you have signed the Code of Conduct in Launchpad. GPG is an encryption system which has two components. These are your own private key (which you need to protect carefully) and a public key which is available to anyone. When you generate a key, both parts are created. The public key is usually uploaded to a keyserver network, so that anyone who wants to use it can access it. If a message is encrypted with your public key, only you can decrypt it, using your private key. You could think of it as being like a locked mailbox - anyone can put things in, but only the keyholder can get them out.
10. Openstack Charm Gerrit Dashboard
  * Setup
   * https://gist.github.com/ryan-beisner/bb593ac2bd1d70b42af1 
  * Code Review Dashboard
   * https://review.openstack.org
11. Bug Submission and Triaging Process:
  * Ubuntu Process - https://wiki.ubuntu.com/Bugs/Triage 
  * Openstack Component Process - https://wiki.openstack.org/wiki/BugTriage 
  * Ubuntu Openstack Charm Bug Submission: https://launchpad.net/charms 
  * Openstack Bug List: ubuntu.openstack.bugs
12. Development Areas:
  * Openstack - https://github.com/openstack-charmers/openstack-community 
  * Charms - https://github.com/openstack-charmers/charm-guide 
  * Packaging - https://wiki.ubuntu.com/OpenStack 
  * Nova-LXD - https://linuxcontainers.org/lxd/getting-started-openstack/ 

## Release Information
  * Ubuntu Release Schedule - https://wiki.ubuntu.com/Releases 
  * Openstack Release Schedule - http://releases.openstack.org 

## Canonical Openstack Product Information
  * Openstack.org Canonical Ubuntu Openstack Information: https://www.openstack.org/marketplace/distros/distribution/canonical/ubuntu-openstack

## Canonical Product Page:
  * http://www.ubuntu.com/cloud/openstack 

## Test Tools
  * https://github.com/openstack-charmers/openstack-community/blob/master/README.dev-charms.md 

  * pep8/lint checks (including charm proof)
   * unit tests
  * charm smoke check 
   * (single charm, deployed in default configuration, against current LTS plus latest Ubuntu release)
  * amulet smoke check 
   * (single Ubuntu + OpenStack test combo: current LTS + latest OpenStack release, relevant to the amulet tests defined within the charm)
  * Functional Testing - Tempest Testing
   * http://docs.openstack.org/developer/tempest/

We have altering gating process - initially we’ll run a subset of amulet tests to provide immediate feedback to the contributor/reviewers; the full set of amulet tests will be executed once a reviewer has +2’ed the review prior to final landing.

Code will be automatically landed by the OpenStack development process - no need to complete manual merges any longer!

## Release Management
http://docs.openstack.org/project-team-guide/release-management.html 

### Package Management
This is the process we undertake to bring our OpenStack packages up to date with their upstream counterparts. 

#### Anatomy

Each package is made up of two fundamental pieces: the packaging repository, and the upstream repository. Upstream is where the OpenStack contributors make changes, implement new features, and fix bugs in the actual functioning of the project (i.e. implement a new storage backend for ceilometer). 

The packaging repository is where the changes to the package that installs the project live. It is effectively a tag of the upstream repo with a “debian” directory in it that describes to apt how to compile the project from source if necessary, where each file gets installed, how to generate or place configs, how to run tests, the list of dependencies, etc. It's important to understand that no changes to the fundamental operation of the project occur in a commit to the packaging repository (except merging in upstream tags). 

For the most part, we make little to no changes to the upstream repository unless we come across a bug that’s appropriate for an upstream fix, in which case we will submit the bug in the upstream bug report tool (as well as the fix if possible, and submit for review).


##### Process

Effectively our package updates occur as follows: 

  * Merge in the new upstream tag to the appropriate branch of the package (either a stable branch, or master). 
  * Check the upstream diff (the difference between the current version in the package repository and the version you're currently merging) for changes to the requirements.txt 
   * All of our packages are python-based so requirements.txt is where the dependencies live.
  * Update the debian/control file to reflect any version changes in the requirements.txt or add new dependencies that have been added in the diff. 
  * Build the package and pray the tests all pass (they probably wont). 
  * Write/rebase/remove patches as necessary to get the tests to pass. Simply excluding failing tests is an option however it should be the last possible choice if nothing else is acceptable. 
  * Once all is green submit the package to a local repository and ask for review and push or push directly if you have permissions.  

Our main over-arching goal is to keep the Ubuntu OpenStack packages as up to date with their upstream projects as possible. We don’t necessarily chase each commit (that’s what CI is for) but we do jump on top of any type of milestone or release candidate. We want our users to be able to install a new OpenStack revision the same day it gets released upstream and by keeping up with the milestones and release candidates we ensure most (if not all) of our packaging bugs are taken care of come release day. We consider this to be the best way to harden our release and is the primary reason over 55% of OpenStack users use Ubuntu. 

#### Building
##### Dependencies

These are packages that are required on your build system to run the bulk of the commands below successfully. This list is not exhaustive:

  * sbuild
  * ubuntu-dev-tools
  * python-dev (& python3-dev)
  * git-buildpackage
  * debhelper
  * dh-systemd
  * openstack-pkg-tools

##### Updating

Note: Change “openstackver” with the current openstack revision (i.e. newton).
 
Updating an openstack client or oslo package (i.e. python-cinderclient):

  * git clone debian git repo and switch to (or create) debian/openstackver branch:
   * git clone git+ssh://git.debian.org/git/openstack/python-cinderclient.git
   * git checkout -b debian/openstackver
  * clone upstream git source directory and checkout desired branch/tag
   * git clone https://github.com/openstack/python-cinderclient.git
   * git tag --list
   * git checkout tags/1.2.1 (the latest release)
   * git diff 1.2.0 1.2.1 (determine requirements.txt and test-requirements.txt delta)
  * new upstream release?
   * merge upstream code into package
    * ./debian/rules fetch-upstream-remote
    * git merge -X theirs <git-tag>
  * 'dch -i'; update changelog and version (non-ubuntu) and commit that (debcommit -a)
  * update debian/control file based on upstream's requirements.txt and test-requirements.txt
   * Don't update d/control with ALL versioning info. Check trusty & jessie & cloud archive (though trusty is usually enough). If versions are high enough, we don't need to specify the version in the dependency. Also you can delete the Pre-Depends: dpkg (>= 1.15.6~) these days.
    * rmadison -u ubuntu,debian <all deps>
  * grab tarball for the upstream release:
   * ./debian/rules gen-orig-xz (or ./debian/rules gen-orig-bz2)
   * depends on whether debian/gbp.conf contains 'compression = xz' or 'compression = bzip'
  * Other tidbits:
   * if you created new branch, update debian/gbp.conf debian-branch (e.g. 'debian-branch = debian/openstackver)
   * build debian 'experimental' and ubuntu 'xenial' from git branch

##### Updating an openstack core package (i.e. cinder):

This document explains the process: https://wiki.ubuntu.com/OpenStack/CorePackages with a few caveats: 

  * During the `uscan` process, the command suggested in the doc rarely works correctly, this is what we use (from inside the checked-out package directory):
   *  uscan --verbose --rename --download-version 7.1.1 --force
    * Replace 7.1.1 with the current upstream tag you’re working with.
  * After you do the import-orig process there are a few more steps before you commit the repo:
   * quilt push -a
   * This will push all the available patches, check for fuzzy or mis-aligned patches as they will cause problems during the build process. Rebase, delete, or modify as necessary.
   * gbp buildpackage -S
    * Build the .dsc file which will allow you to actually build the package, either via a PPA or locally with sbuild. (.dsc will be in ../build-area/)
   * Build to verify:
    * Sbuild locally: https://wiki.ubuntu.com/SimpleSbuild 
    * Or: build with a ppa. Create the PPA with the proper dependencies in launchpad then you can use this command to submit the .dsc:
     * I.e. backportpackage -S ~ppa`date +%Y%m%d%H%M` -d xenial --upload ppa:ddellav/xenial-mitaka ../build-area/cinder*.dsc 
   * Install the package to make sure everything works, this is done best in a VM.
   * If that builds and installs successfully you push to git and ask for review or push it directly if you have permissions. 

## Release Package Status
http://reqorts.qa.ubuntu.com/reports/ubuntu-server/cloud-archive/ 
These packages are updated every 45 minutes.

## Repositories

Here are various repositories that is worth understanding. In various scenarios, packages are made available through specific location based on the priority and need. As an example, a specific bug fix that only affects one customer may be made available through a developer pps repository. 

Github - 
https://github.com/openstack-charmers 

Cloud Archive 
OpenStack packages for Ubuntu are published in Ubuntu main repository and maintained by Ubuntu core developers. Canonical’s Ubuntu Cloud archive allows users the ability to install newer releases of OpenStack on Ubuntu Server 12.04 LTS (and the dependencies) as they become available up through the next Ubuntu LTS release (presumably 14.04) https://wiki.openstack.org/wiki/Packaging/Ubuntu 

Openstack packages are made available to users for download and install using the following steps:

sudo apt-add-repository cloud-archive=[OPENSTACK-RELEASE] & 
sudo apt-get install openstack 

The following link provides information on the status and version of each openstack component in the cloud archive. 

http://reqorts.qa.ubuntu.com/reports/ubuntu-server/cloud-archive/


Personal Package Archive (PPA) - Various PPAs with bleeding edge packages are maintained by the team. https://launchpad.net/~openstack-ubuntu-testing 

Ubuntu Repositories
The Ubuntu software repositories are organized into four separate areas or "components", according to the level of support offered by Ubuntu and whether or not the program in question complies with Ubuntu's Free Software Philosophy. 
The repository components are: 
Main - Officially supported software. 
Restricted - Supported software that is not available under a completely free license. 
Universe - Community maintained software, i.e. not officially supported software. 
Multiverse - Software that is not free.
More information on repositories can be found at the following location:
https://help.ubuntu.com/community/Repositories/Ubuntu 


## Ecosystem

* Using Juju with Openstack - https://jujucharms.com/openstack
* MAAS, Juju, and Landscape with Openstack - https://help.ubuntu.com/lts/clouddocs/en/Intro.html?_ga=1.29609215.1923604178.1456780510 
* Bootstack

## BLOGs, Youtube, etc.

Contents that the ecosystem team has developed and may be useful to further understand the inner workings and projects of the team.

#### Blogs:
http://blog.juju.solutions 

#### Youtube:
https://www.youtube.com/channel/UCSsoSZBAZ3Ivlbt_fxyjIkw 

#### Insights:
https://insights.ubuntu.com 

#### Discourse:
http://discourse.ubuntu.com 




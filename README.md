# fh-openshift-templates

## Building
```shell
$ npm install
```

Produce a tar with the templates:

```shell
$ grunt
```
The produced tar will be in the ```dist``` directory.


## Workflow Guidelines

* Use the parameters section to define key values and then reference them in the rest of the template.
* Keep version in template and package.json in sync.

## Productization procedure

Engineering will produce builds frequently, and update the master branch with these.
Productization will have new images less frequently, and will follow this flow:

### fh-openshift-templates

* Create and push a branch at the tip of master, named with the productization build

    * E.g. `RHMAP-4.0.0-DR9`

* Modify the template to include productized images

* Modify the version in template and package.json to a productized version

* Create a pull request in Github against the already created branch

    * This will generate a new build, and publish corresponding artifact on S3

### fhcap

* Create a corresponding branch in fhcap that a PR will be created against

    * Either from master, or from a relevant release branch

* Use [fhcap-cli](https://github.com/fheng/fhcap-cli) to do a component update for fh-openshift-templates

    * E.g. `fhcap component update fh-openshift-templates <version> <buildnumber>`

    The version and build number should correspond to the build on Jenkins.

* Create a pull request on Github against the new branch

* If part of a release, notify @jasonmadigan (engineering release manager)



## setup a local core

* versions of images are currently stored in scripts/core/common.sh update the versions to versions you want to use
* create a new project oc new-project core
* cd scripts/core
* in your OSE vm run ./download_images.sh
* run ./infra.sh  (in the ui wait till the mongo intiator is no longer visible)
* as root user in your OS vm run oc create -f ../../gitlab-shell/scc-anyuid-with-chroot.json
* then as root in your OS vm run oc adm policy add-scc-to-user anyuid-with-chroot system:serviceaccount:<YOUR_PROJECT>:default
* run ./backend.sh (wait till all images are running and blue)
* run ./front.sh
* visit http://rhmap.local.feedhenry.io
* login with rhmap-admin@example.com

## OMG I have no permissions

Just as with fhcap occasionally you can end up with no permissions at the first login

````
oc get pods

oc rsh <mysql-pod>

mysql -u root

use shard0

update sys_Sub set aaaActive=0 where guid !=""

```

In the OSE vm 

```
sudo yum install telnet

```
get the ip of the memcached container

```
oc describe svc memcached

telnet <10.x.x.x> 11211

flush_all

ctrl + ]

exit


```

logout hard refresh and log back in



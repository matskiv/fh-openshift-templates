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

## Testing
To run tests on all templates, run
```shell
$ npm run test
```

To run tests on a specific group of templates or an individual template pass in a `--suite` value
to the tests.
```shell
$ npm run test --suite=generated # Runs tests on all templates

$ npm run test --suite=aaa # Runs tests on all fh-aaa template
```

The list of available template suites which can be passed into `npm run test` can
be found in files within the `suites/` directory. For example, the `backend.js` file
contains the key `aaa`, this relates to the `fh-aaa-single-template.json` file
and specifies some configuration options. To run the tests on the `fh-aaa` template
just run `npm run test` with the `aaa` key specified as the `suite`. E.g.
```shell
npm run test --suite=aaa
```
This can be done with any key in any module in the `suites/` directory.


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

* create a new project oc new-project core
* cd scripts/core
* make sure docker login is configured as in [fhcap-documentation](https://github.com/fheng/fhcap/tree/master/flavours/rhel_openshift3#mounting-docker-credentials-into-the-vm-to-allow-pulling-of-private-images)
* in your vm as a system administrator run ./preqequisites.sh (sets up secrets)
* run ./infra.sh  (in the ui wait till the mongo intiator is no longer visible)
* as root user in your OS vm run oc create -f ../../gitlab-shell/scc-anyuid-with-chroot.json
* then as root in your OS vm run oc adm policy add-scc-to-user anyuid-with-chroot system:serviceaccount:<YOUR_PROJECT>:default (sets up access for gitlab-shell)
* run ./backend.sh (wait till all images are running and blue)
* run ./front.sh
* visit http://rhmap.local.feedhenry.io
* login with rhmap-admin@example.com

Note: When using scripts on mac ssed tool should be installed. By default sed command is used for different purpose:

	brew install ssed
	alias sed='ssed'

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

Note some times it can help to restart millicore

oc scale dc/millicore --replicas=0
wait for it to stop
oc scale dc/millicore --replicas=1
wait for it to fully start
```

logout hard refresh and log back in

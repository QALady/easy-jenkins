### Introduction

<!-- (2016-02-02 07:58 CET) -->

Instructions for building from sources my fork of the [GENIVI Demo Platform](http://projects.genivi.org/genivi-demo-platform/home).

This is mainly used as a regression test suite for the [gmacario/easy-jenkins](https://github.com/gmacario/easy-jenkins) project.

The following instructions were tested on

* Docker client: itm-gmacario-w7 (MS Windows 7 64-bit, Docker Toolbox 1.10.0)
* Docker engine: mv-linux-powerhorse (Ubuntu 14.04.3 LTS 64-bit, Docker Engine 1.10.0)

You may also run both Docker client and engine on your (OS X or Windows) laptop using [Docker Toolbox](https://www.docker.com/products/docker-toolbox).

### Preparation

On a bash shell on the Docker Client, deploy master branch of [gmacario/easy-jenkins](https://github.com/gmacario/easy-jenkins) to docker-machine `mv-linux-powerhorse`

```
$ git clone https://github.com/gmacario/easy-jenkins
$ cd ~/easy-jenkins
$ eval $(docker-machine env mv-linux-powerhorse)
$ docker-compose stop; docker-compose rm -f; docker-compose build --pull
$ ./runme.sh
```

Some information messages should be displayed on the launching terminal:

```
gmacario@ITM-GMACARIO-W7 MINGW64 ~/easy-jenkins (master)
$ ./runme.sh
INFO: Docker machine mv-linux-powerhorse exists, skipping docker-machine create
INFO: Using Docker machine mv-linux-powerhorse
Creating easyjenkins_myjenkins_1
Creating easyjenkins_build-yocto-slave_1
INFO: Browse http://192.168.12.22:9080/ to access the Jenkins dashboard
INFO: Run the following command to configure your shell:
INFO: eval $(docker-machine env mv-linux-powerhorse)

gmacario@ITM-GMACARIO-W7 MINGW64 ~/easy-jenkins (master)
$
```

Take note of the `${JENKINS_URL}` (in our example, <http://192.168.12.22:9080/>) where the Jenkins dashboard could be accessed.

(Optional) Watch docker-compose logs until line `INFO: Jenkins is fully up and running` is displayed:

```
$ docker-compose logs
...
myjenkins_1         | INFO: Completed initialization
myjenkins_1         | Feb 02, 2016 7:05:31 AM hudson.WebAppMain$3 run
myjenkins_1         | INFO: Jenkins is fully up and running
...
```

Browse `${JENKINS_URL}` as explained by the `INFO: Browse xxx to access the Jenkins dashboard` message above and verify that the Jenkins dashboard is displayed correctly.

Browse `${JENKINS_URL}/job/seed`, then click **Build Now**

Result: The following items will be generated and show up in the Jenkins dashboard:

1. add_jenkins_slave
2. build_gdp
3. configure_git

### Configure git on Jenkins node `master`

<!-- (2016-02-04 12:16 CET) -->

Workaround for [issues/26](https://github.com/gmacario/easy-jenkins/issues/26)

Browse `${JENKINS_URL}/job/configure_git/`, then click **Build Now**

Verify in the Console Output that the job was run on the master node (at this point there should not be any slave nodes yet)

### Create Jenkins node `build-yocto-slave`

<!-- (2016-02-04 12:17 CET) -->

Workaround for [issues/16](https://github.com/gmacario/easy-jenkins/issues/16)

Browse `${JENKINS_URL}/job/add_jenkins_slave/` then click **Build with Parameters**

- AgentList: `build-yocto-slave`
- AgentDescription: `Auto-created Jenkins agent`
- AgentHome: `/home/jenkins`
- AgentExecutors: `2`

TODO: Should add Text parameter `AgentLabels` - tracked as [issues/23](https://github.com/gmacario/easy-jenkins/issues/23)

then click **Build**

Result: SUCCESS

Browse `${JENKINS_URL}`, verify that node `build-yocto-slave` is running.

### Configure labels on Jenkins node `build-yocto-slave`

<!-- (2016-02-04 12:18 CET) -->

Workaround for [issues/23](https://github.com/gmacario/easy-jenkins/issues/23)

Browse `${JENKINS_URL}/computer/build-yocto-slave/`, then click **Configure**

- Labels: `yocto`

Then click **Save**.

### Configure git on Jenkins node `build-yocto-slave`

<!-- (2016-02-04 12:19 CET) -->

Workaround for [issues/27](https://github.com/gmacario/easy-jenkins/issues/27)

Browse `${JENKINS_URL}/job/configure_git/`, then click **Configure**

- Restrict where this project can be run: Yes
  - Label Expression: `build-yocto-slave`

Click **Save**, then click **Build Now**

Verify in the Console Output that the job was run on the slave.

### Configure project `build_gdp`

<!-- (2016-02-04 12:20 CET) -->

Workaround for [issues/25](https://github.com/gmacario/easy-jenkins/issues/25)

Browse `${JENKINS_URL}/job/build_gdp/`, then click **Configure**

- Restrict where this project can be run: Yes
  - Label Expression: `yocto`

Then click **Save**.

### Build project `build_gdp`

<!-- (2016-02-04 12:20 CET) -->

Browse `${JENKINS_URL}/job/build_gdp/`, then click **Build Now**

<!--
(2016-02-04 14:33 CET)

NOTE: Running task 3426 of 4394 (ID: 1578, /home/jenkins/workspace/build_gdp/gdp-src-build/../meta-ivi/meta-ivi/recipes-extended/persistence-administrator/persistence-administrator_git.bb, do_configure)
NOTE: recipe persistence-administrator-7.0+gitAUTOINC+3a6cc3228b-r0: task do_configure: Started
-->

Result: TODO

<!-- EOF -->
# Install Grafana and Prometheus on CentOS 7 #

Although Prometheus is very good at collection, alerting and searching for metrics. But it does not include a native tool for creating custom dashboards. Although, we can create custom dashboards for the metrics collected by Prometheus by using another free and open source software i.e. Grafana.

Grafana is a free and open-source, general-purpose graph and dashboard composer. It supports many third party software applications such as Prometheus, PNP, InfluxDB, Graphite, etc. Grafana runs as a web application at default port 3000/tcp.

In this article, we are installing Grafana and Prometheus on CentOS 7. And we will use Grafana Web UI to create a dashboard for Prometheus metrics.

Prometheus: Up & Running by O'Reilly Media is a recommended book for the sysadmins, who want to learn more about creating dashboards in Grafana for Prometheus metrics.

###This Article Provides:
***
* Environment Specification
* Installing Grafana Yum Repository in CentOS 7
* Installing Grafana on CentOS 7
* Add Prometheus Data Source in Grafana
* Create Prometheus Dashboard in Grafana
* Create Alerts in Grafana


##Environment Specification:
**
**We are using the same CentOS 7 virtual machine on which we have installed Prometheus in our previous article.

* CPU - 3.4 Ghz (2 cores)
* Memory - 2 GB
* Storage - 20 GB
* Operating System - CentOS 7.7
* Hostname - prometheus-01.example.com
* IP Address - 192.168.116.213 /24

##Installing Grafana Yum Repository in CentOS 7:
**Connect with prometheus-01.example.com using ssh as root user.

Grafana is available to downloads in many formats for a variety of Linux distros. Since, we are working on a CentOS 7 based linux distro, therefore, we have to install Grafana using RPMs.

Moreover, we can also install Grafana using it's yum repository. Therefore, we are adding Grafana yum repository in CentOS 7 as follows.

```
[root@prometheus-01 ~]# vi /etc/yum.repos.d/grafana.repo
```
and add following directives therein.
```
[grafana]
name=grafana
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt

```
####**Build cache for yum repositories.
```
[root@prometheus-01 ~]# yum makecache fast
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: repo.isra.edu.pk
 * extras: centos.myfahim.com
 * updates: centos.myfahim.com
base                                                     | 3.6 kB     00:00
extras                                                   | 2.9 kB     00:00
grafana/signature                                        |  488 B     00:00
Retrieving key from https://packages.grafana.com/gpg.key
Importing GPG key 0x24098CB6:
 Userid     : "Grafana <info@grafana.com>"
 Fingerprint: 4e40 ddf6 d76e 284a 4a67 80e4 8c8c 34c5 2409 8cb6
 From       : https://packages.grafana.com/gpg.key
Is this ok [y/N]: y
grafana/signature                                        | 2.9 kB     00:10 !!!
updates                                                  | 2.9 kB     00:00
(1/2): grafana/primary_db                                  |  53 kB   00:02
(2/2): updates/7/x86_64/primary_db                         | 2.8 MB   00:12
Metadata Cache Created
```

###Installing Grafana on CentOS 7:
 > Now, we can install Grafana using yum command.
```
[root@prometheus-01 ~]# yum install -y grafana
...
Installed:
  grafana.x86_64 0:6.4.4-1

Dependency Installed:
  dejavu-fonts-common.noarch 0:2.33-6.el7
  dejavu-sans-fonts.noarch 0:2.33-6.el7
  fontconfig.x86_64 0:2.13.0-4.3.el7
  fontpackages-filesystem.noarch 0:1.44-8.el7
  libICE.x86_64 0:1.0.9-9.el7
  libSM.x86_64 0:1.2.2-2.el7
  libX11.x86_64 0:1.6.7-2.el7
  libX11-common.noarch 0:1.6.7-2.el7
  libXau.x86_64 0:1.0.8-2.1.el7
  libXcursor.x86_64 0:1.1.15-1.el7
  libXext.x86_64 0:1.3.3-3.el7
  libXfixes.x86_64 0:5.0.3-1.el7
  libXi.x86_64 0:1.7.9-1.el7
  libXinerama.x86_64 0:1.1.3-2.1.el7
  libXmu.x86_64 0:1.1.2-2.el7
  libXrandr.x86_64 0:1.5.1-2.el7
  libXrender.x86_64 0:0.9.10-1.el7
  libXt.x86_64 0:1.1.5-3.el7
  libXxf86misc.x86_64 0:1.0.3-7.1.el7
  libXxf86vm.x86_64 0:1.1.4-1.el7
  libfontenc.x86_64 0:1.1.3-3.el7
  libxcb.x86_64 0:1.13-1.el7
  urw-base35-bookman-fonts.noarch 0:20170801-10.el7
  urw-base35-c059-fonts.noarch 0:20170801-10.el7
  urw-base35-d050000l-fonts.noarch 0:20170801-10.el7
  urw-base35-fonts.noarch 0:20170801-10.el7
  urw-base35-fonts-common.noarch 0:20170801-10.el7
  urw-base35-gothic-fonts.noarch 0:20170801-10.el7
  urw-base35-nimbus-mono-ps-fonts.noarch 0:20170801-10.el7
  urw-base35-nimbus-roman-fonts.noarch 0:20170801-10.el7
  urw-base35-nimbus-sans-fonts.noarch 0:20170801-10.el7
  urw-base35-p052-fonts.noarch 0:20170801-10.el7
  urw-base35-standard-symbols-ps-fonts.noarch 0:20170801-10.el7
  urw-base35-z003-fonts.noarch 0:20170801-10.el7
  xorg-x11-font-utils.x86_64 1:7.5-21.el7
  xorg-x11-server-utils.x86_64 0:7.7-20.el7

Complete!
```
 ###**Enable and start Grafana service.
```
[root@prometheus-01 ~]# systemctl enable --now grafana-server.service
Created symlink from /etc/systemd/system/multi-user.target.wants/grafana-server.service to /usr/lib/systemd/system/grafana-server.service.

```
  ###Allow Grafana default service port i.e. 3000/tcp in CentOS 7 firewall.
```
[root@prometheus-01 ~]# firewall-cmd --permanent --add-port=3000/tcp
success
[root@prometheus-01 ~]# firewall-cmd --reload
success
```

###Browse URL http://prometheus-01.example.com:3000/ in a client's browser.
if you wish to see the dashboard of Grafana select the link of ![](https://4.bp.blogspot.com/-DQVVbrg95tU/XcQtfZRuHTI/AAAAAAAAGxY/9ol0JYx6QCApwsCTm--uHweWuJdqMrp-gCLcBGAsYHQ/s1600/01-grafana-web-ui-login.png)

**##NOTE: The default user/password for Grafana is admin/admin.

Because, we are login for the first time, therefore, Grafana prompt us to change the default password of admin user. click for [HOME_DASHBOARD_image](https://1.bp.blogspot.com/-kKGhrwNAvf4/XcQtgnk0HCI/AAAAAAAAGxs/EqdQlcgVXvoZrObZPw56M5qmiSiJdpPLQCLcBGAsYHQ/s1600/03-grafana-home-dashboard.png)

Add Prometheus Data Source in Grafana:
After successful login, we are now at the main screen of Grafana Web UI.
Click on Add Data Source.|

for image [click](https://1.bp.blogspot.com/-7zm5eeJxy4s/XcQthI4vvdI/AAAAAAAAGxw/4TKGE6001S0n9IbUNz7yCtsiJp6S9e7lgCLcBGAsYHQ/s1600/04-grafana-add-data-source.jpg)

+select and Click on Prometheus opction

     
         








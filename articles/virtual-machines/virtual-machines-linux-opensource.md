<properties
	pageTitle="Azure 上的 Linux 和開放原始碼運算"
	description="本主題包含一份 Azure 上的 Linux 和開放原始碼運算清單，其中包括基本 Linux 使用方式、有關在 Azure 上執行或上傳 Linux 映像的一些基本概念，以及其他有關特定技術與最佳化的內容。"
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="05/08/2015"
	ms.author="rasquill"/>



# Azure 上的 Linux 和開放原始碼運算

本文件嘗試在一個地方列出 Microsoft 及其合作夥伴針對在 Microsoft Azure 上執行 Linux 虛擬機器及其他開放原始碼運算環境和應用程式而撰寫的所有相關主題。由於 Azure 和開放原始碼運算的世界變動非常迅速，「儘管」在事實上我們應該盡全力持續加入較新的主題並移除過期的主題，但是幾乎可以肯定本文件已過時。如果我們遺漏了什麼，請在註解中讓我們知道，或將提取要求提交至我們的 [Github 儲存機制](https://github.com/Azure/azure-content/)。

## 一般注意事項
本頁右側的章節均可向下細分。(有些連結可能會出現在多個章節中，因為有些主題可能與一個以上的概念、散發版本或技術相關。) 此外，有幾個描述各種 Linux 選項、映像儲存機制、案例研究的主題，以及如何上傳您自己的自訂映像的主題：

- [Azure Marketplace](http://azure.microsoft.com/marketplace/virtual-machines/)
- [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index)
- [事件和示範：Microsoft Openness CEE](http://www.opennessatcee.com/)
- [做法：上傳您自己的 Distro 映像](virtual-machines-linux-create-upload-vhd.md) (以及使用 [Azure 背書散發版本](virtual-machines-linux-endorsed-distributions.md)的指示)
- [注意：在 Azure 中執行的一般 Linux 需求](virtual-machines-linux-create-upload-vhd-generic.md)
- [注意：Azure 上的 Linux 一般簡介](virtual-machines-linux-introduction.md)

<!--
- [Distros](#distros) &mdash; Topics to do with a specific distro.
- [The Basics](#basics) &mdash; A lot of the basic things to do that you either know or need to know.
- [Community Images and Repositories](#images) &mdash; Other places for very useful information, repositories, and binaries.
- [Languages and Platforms](#langsandplats)
- [Samples and Scripts](#samples)
- [Auth and Encryption](#security) &mdash; Important security-related topics, not necessarily specific to Azure.
- [Devops, Management, and Optimization](#devops) &mdash; A big category, changing rapidly.
- [Support, Troubleshooting, and "It Just Doesn't Work"](#supportdebug) &mdash; Really.
-->

## 發行版

目前提供大量的 Linux 散發套件 (通常會依封裝管理系統加以細分)：有些是以 dpgk 為基礎 (例如 Debian 和 Ubuntu)，有些則是以 rpm 為基礎 (例如 CentOS、SUSE 及 RedHat)。有些公司提供 Distro 映像表示 Microsoft 的正式夥伴並加以背書。其他 Distro 映像則是由社群提供。本節中的散發版本有其相關的正式文件，即使只是用於其他技術的範例。

### [Ubuntu](http://azure.microsoft.com/marketplace/partners/Canonical/)

Ubuntu 是以 dpkg 和 apt-get 封裝管理為基礎的 Linux 散發套件，其非常受歡迎且已由 Azure 背書。

1. [做法：上傳您自己的 Ubuntu 映像](virtual-machines-linux-create-upload-vhd-ubuntu.md)
2. [做法：Ubuntu LAMP 堆疊](virtual-machines-linux-install-lamp-stack.md)
2. [映像：LAPP 堆疊](http://azure.microsoft.com/marketplace/partners/bitnami/lappstack54310ubuntu1404/)
3. [做法：MySQL 叢集](virtual-machines-linux-mysql-cluster.md)
4. [做法：Node.js 和 Cassandra](virtual-machines-linux-nodejs-running-cassandra.md)
5. [做法：IPython Notebook](virtual-machines-python-ipython-notebook.md)
6. [精通：在 Linux 上使用 Docker 容器執行 ASP.NET 5](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)
7. [映像：Redis 伺服器](http://azure.microsoft.com/marketplace/partners/cognosys/redisserver269ubuntu1204lts/)
8. [映像：Minecraft 伺服器](http://azure.microsoft.com/marketplace/partners/bitnami/craftbukkitminecraft179r030ubuntu1210/)
9. [映像：Moodle](http://azure.microsoft.com/marketplace/partners/bitnami/moodle270ubuntu1404/)
11. [映像：Mono 即服務](http://azure.microsoft.com/marketplace/partners/aegis/monoasaserviceubuntu1204/)


### [Debian](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=Debian)

Debian 是 Linux 及以 dpkg 和 apt-get 封裝管理為基礎之開放原始碼世界的重要散發套件。MSOpenTech VM Depot 有數個映像可使用。

### CentOS

CentOS Linux 散發版本是一個穩定、可預測、容易管理且可重製的平台，其衍生自 Red Hat Enterprise Linux (RHEL) 的原始碼。

1. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=centos)
2. [映像庫](http://azure.microsoft.com/en-in/marketplace/partners/OpenLogic/)
3. [做法：針對 Azure 準備自訂 CentOS 型 VM](virtual-machines-linux-create-upload-vhd-centos.md)
4. [部落格：如何部署 OpenLogic 提供的 CentOS VM 映像](http://azure.microsoft.com/blog/2013/01/11/deploying-openlogic-centos-images-on-windows-azure-virtual-machines/)
6. [做法：安裝 Apache Qpid Proton-C for AMQP 和服務匯流排](http://msdn.microsoft.com/library/azure/dn235560.aspx)
7. [映像：OpenLogic CentOS 6.3 上的 Apache 2.2.15](http://azure.microsoft.com/marketplace/partners/cognosys/apache2215onopenlogiccentos63/)
8. [映像：Drupal 7.2，OpenLogic CentOS 6.3 上的 LAMP 伺服器](http://azure.microsoft.com/marketplace/partners/cognosys/drupal720lampserveronopenlogiccentos63/)

### SUSE Linux Enterprise Server 與 openSUSE

9. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=OpenSUSE)
11. [做法：安裝和執行 MySQL](virtual-machines-linux-mysql-use-opensuse.md)
12. [做法：準備自訂 SLES 或 openSUSE VM](virtual-machines-linux-create-upload-vhd-suse.md)  
13. [[SUSE 論壇] 做法：移至新的修補程式伺服器](https://forums.suse.com/showthread.php?5622-New-Update-Infrastructure)
14. [映像：SUSE Linux Enterprise Server for SAP 雲端應用裝置程式庫](http://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver11sp3forsapcloudappliance/)

### CoreOS

CoreOS 是一個小型、最佳化的散發版本，適用於高度控制自訂的純計算。

10. [映像庫](http://azure.microsoft.com/en-in/marketplace/partners/coreos/)  
11. [做法：在 Azure 上使用 CoreOS](virtual-machines-linux-coreos-how-to.md)
12. [做法：開始在 Azure 上的 CoreOS 使用 Fleet 和 Docker](virtual-machines-linux-coreos-fleet-get-started.md)
13. [部落格：TechEd Europe -- Windows Docker 用戶端和 Linux 容器](http://azure.microsoft.com/blog/2014/10/28/new-docker-coreos-topics-linux-on-azure/)
14. [部落格：Azure 日益壯大、快速且更加開放](http://azure.microsoft.com/blog/2014/10/20/azures-getting-bigger-faster-and-more-open/)
15. [GitHub：在 Azure 上部署 CoreOS 的快速入門](https://github.com/timfpark/coreos-azure)
16. [GitHub：使用 Spring Boot、MongoDB 和 CoreOS 部署 Java 應用程式](https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init)

#### [Oracle Linux](http://azure.microsoft.com/marketplace/?term=Oracle+Linux)
  2. [準備執行 Azure 的 Oracle Linux 虛擬機器](virtual-machines-linux-create-upload-vhd-oracle.md)

### FreeBSD

12. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index?sort=Date&search=FreeBSD)
13. [部落格：在 Azure 中執行 FreeBSD](http://azure.microsoft.com/blog/2014/05/22/running-freebsd-in-azure/)
14. [部落格：輕鬆部署 FreeBSD](http://msopentech.com/blog/2014/10/24/easy-deploy-freebsd-microsoft-azure-vm-depot/)
15. [部落格：部署自訂的 FreeBSD 映像](http://msopentech.com/blog/2014/05/14/deploy-customize-freebsd-virtual-machine-image-microsoft-azure/)
17. [做法：安裝 Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md)
18. [Marketplace︰適用於 Linux 檔案伺服器的 Kaspersky AV](http://azure.microsoft.com/marketplace/partners/kaspersky-lab/kav-for-lfs-kav-for-lfs/)

## 基本概念

1. [基本概念：Azure 命令列介面 (Azure CLI)](../xplat-cli.md)
4. [基本概念：憑證的使用和管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)
5. [基本概念：選取 Linux 使用者名稱](virtual-machines-linux-usernames.md)
6. [基本概念：使用 Azure 入口網站登入 Linux VM](virtual-machines-linux-how-to-log-on.md)
7. [基本概念：SSH](virtual-machines-linux-use-ssh-key.md)
8. [基本概念：如何重設 Linux 的密碼或 SSH 屬性](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [基本概念：使用 Root](virtual-machines-linux-use-root-privileges.md)
10. [基本概念：將資料磁碟連接至 Linux VM](virtual-machines-linux-how-to-attach-disk.md)
11. [基本概念：從 Linux VM 卸離資料磁碟](virtual-machines-linux-how-to-detach-disk.md)
12. [基本概念部落格：使用 Linux 與 Azure 最佳化儲存體、磁碟和效能](http://blogs.msdn.com/b/igorpag/archive/2014/10/23/azure-storage-secrets-and-linux-i-o-optimizations.aspx)
13. [基本概念：RAID](virtual-machines-linux-configure-raid.md)
14. [基本概念：擷取 Linux VM 來製作範本](virtual-machines-linux-capture-image.md)
15. [基本概念：安裝 Azure Linux 代理程式](virtual-machines-linux-agent-user-guide.md)
16. [基本概念：Azure VM 延伸模組與功能](http://msdn.microsoft.com/library/azure/dn606311.aspx)
17. [基本概念：將自訂資料插入 VM 中以搭配 Cloud-init 使用](virtual-machines-how-to-inject-custom-data.md)
18. [基本概念部落格：以 12 個步驟在 Azure 上建置高可用性的 Linux](http://blogs.technet.com/b/keithmayer/archive/2014/10/03/quick-start-guide-building-highly-available-linux-servers-in-the-cloud-on-microsoft-azure.aspx)
19. [基本概念部落格：透過Azure CLI、node.js、jhawk 在 Azure 上自動佈建 Linux](http://blogs.technet.com/b/keithmayer/archive/2014/11/24/step-by-step-automated-provisioning-for-linux-in-the-cloud-with-microsoft-azure-xplat-cli-json-and-node-js-part-1.aspx)
19. [使用 Azure CLI 建立多個 VM 的部署](virtual-machines-create-multi-vm-deployment-../xplat-cli.md)
20. [基本概念：Azure Docker VM 延伸模組](virtual-machines-docker-vm-extension.md)
23. [Azure 服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx) 參考
24. [Azure 上的 GlusterFS](http://dastouri.azurewebsites.net/gluster-on-azure-part-1/)

## 社群映像和儲存機制
3. [MSOpenTech VM Depot](https://vmdepot.msopentech.com/List/Index) &mdash; 適用於社群提供的虛擬機器映像。
4. [GitHub](https://github.com/Azure/) &mdash; 適用於 Azure CLI，以及其他許多工具和專案。
5. [Docker Hub 登錄](https://registry.hub.docker.com/) &mdash; Docker 容器映像的登錄。

## 語言和平台
### [Azure Java 開發人員中心](http://azure.microsoft.com/develop/java/)

1. [映像](https://vmdepot.msopentech.com/List/Index?sort=Featured&search=java)
2. [做法：搭配使用 Java 的服務匯流排與 AMQP 1.0](http://msdn.microsoft.com/library/azure/jj841073.aspx)
3. [做法：使用 Azure 入口網站在 Linux 上設定 Tomcat7](virtual-machines-linux-setup-tomcat7-linux.md)
4. [影片：適用於服務管理的 Azure Java SDK](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-157-The-Java-SDK-for-Azure-Management-with-Brady-Gaster)
5. [部落格：開始使用 Java 的 Azure 管理程式庫](http://azure.microsoft.com/blog/2014/09/15/getting-started-with-the-azure-java-management-libraries/)
5. [GitHub 儲存機制：適用於 Eclipse with Java 的 Azure 工具組](https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava)
6. [參考：適用於 Eclipse with Java 的 Azure 工具組](http://msdn.microsoft.com/library/azure/hh694271.aspx)
7. [GitHub 儲存機制︰適用於 IntelliJ IDEA 和 Android Studio 的 MS Open Tech 工具外掛程式](https://github.com/MSOpenTech/msopentech-tools-for-intellij)
7. [部落格：MSOpenTech 對 OpenJDK 的貢獻](http://msopentech.com/blog/2014/10/21/ms-open-techs-first-contribution-openjdk/)
8. [映像：WebSphere](http://azure.microsoft.com/marketplace/partners/msopentech/was-8-5-was-8-5-5-3/)
9. [映像：WebLogic](http://azure.microsoft.com/marketplace/?term=weblogic)
10. [映像：Windows 上的 JDK6](http://azure.microsoft.com/marketplace/partners/msopentech/jdk6onwindowsserver2012/)
11. [映像：Windows 上的 JDK7](http://azure.microsoft.com/marketplace/partners/msopentech/jdk7onwindowsserver2012/)
12. [映像：Windows 上的 JDK8](http://azure.microsoft.com/marketplace/partners/msopentech/jdk8onwindowsserver2012r2/)

### JVM 語言

1. [調整：在 Azure 雲端服務中執行播放架構應用程式](http://msopentech.com/blog/2014/09/25/tutorial-running-play-framework-applications-microsoft-azure-cloud-services-2/)

### SDK 類型、安裝、升級
4. [Azure 服務管理 SDK：Java](http://dl.windowsazure.com/javadoc/)
5. [Azure 服務管理 SDK：Go](https://github.com/MSOpenTech/azure-sdk-for-go)
5. [Azure 服務管理 SDK：Ruby](https://github.com/MSOpenTech/azure-sdk-for-ruby)
    - [做法：安裝 Ruby on Rails](virtual-machines-ruby-rails-web-app-linux.md)
6. [Azure 服務管理 SDK：Python](https://github.com/Azure/azure-sdk-for-python)
    - [做法：Django Hello World Web 應用程式 (Mac-Linux)](virtual-machines-python-django-web-app-linux.md)
7. [Azure 服務管理 SDK：Node.js](https://github.com/MSOpenTech/azure-sdk-for-node)
8. [Azure 服務管理 SDK：PHP](https://github.com/MSOpenTech/azure-sdk-for-php)
    - [做法：在 Azure VM 上安裝 LAMP 堆疊](virtual-machines-linux-install-lamp-stack.md)
    - [影片：在 Azure VM 上安裝 LAMP 堆疊](http://channel9.msdn.com/Shows/Azure-Friday/LAMP-stack-on-Azure-VMs-with-Guy-Bowerman)
9. [Azure 服務管理 SDK：.NET](https://github.com/Azure/azure-sdk-for-net)
10. [部落格：Mono、ASP.NET 5、Linux 和 Docker](http://blogs.msdn.com/b/webdev/archive/2015/01/14/running-asp-net-5-applications-in-linux-containers-with-docker.aspx)

## 範例和指令碼

瀏覽本節，即能快速了解。如果您有任何建議，請傳送 PR 給我們，或在註解中留下建議，如下。

1. [使用 Azure CLI 建立多個 VM 的部署](virtual-machines-create-multi-vm-deployment-../xplat-cli.md)
2. [Patrick Chanezon 的 Azure Linux Github 儲存機制](https://github.com/chanezon/azure-linux)
3. [影片：如何使用 **usbip** 將 Linux 上的內部部署 USB 資料移到 Azure](http://channel9.msdn.com/Blogs/Open/On-premises-USB-devices-on-Linux-on-Azure-via-usbip)
4. [影片：在瀏覽器中使用 fernapp 存取 Azure 上的 Linux GUI](http://channel9.msdn.com/Blogs/Open/Accessing-Linux-based-GUI-on-Azure-over-browser-with-fernapp)
5. [影片：在 Linux 上使用 Azure 檔案預覽的共用儲存體 -- 第 1 部分](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)
6. [影片：在 Azure 上採行使用服務匯流排和網站的 Linux 裝置](http://channel9.msdn.com/Blogs/Open/Embracing-Linux-devices-on-Azure-via-Service-Bus-and-Web-Sites)
7. [影片：將原生 Linux 記憶體快取的應用程式連接到 Windows Azure](http://channel9.msdn.com/Blogs/Open/Connecting-a-Linux-based-native-memcache-application-to-Windows-Azure)
8. [影片：在 Azure 上平衡高可用性 Linux 服務的負載：](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)


## 資料

本節包含數種不同儲存體方法和技術的相關資訊，包括 NoSQL、關聯式和大數據。

### NoSQL

1. [部落格：Azure 的 8 個開放原始碼 NoSql 資料庫](http://openness.microsoft.com/blog/2014/11/03/open-source-nosql-databases-microsoft-azure/)
2. Couchdb
    - [Slideshare (MSOpenTech)︰Azure 上的 CouchDb 經驗](http://www.slideshare.net/brianbenz/experiences-using-couchdb-inside-microsofts-azure-team)
    - [部落格：透過 node.js、CORS 和 Grunt 執行 CouchDB 即服務](http://msopentech.com/blog/2013/12/19/tutorial-building-multi-tier-windows-azure-web-application-use-cloudants-couchdb-service-node-js-cors-grunt-2/)
3. MongoDB
    - [做法：以使用 MongoLab 附加元件的 MongoDB 在 Azure 上建立 Node.js 應用程式](store-mongolab-web-sites-nodejs-store-data-mongodb.md)
4. Cassandra
    - [做法：在 Azure 上執行 Cassandra 搭配 Linux 並透過 Node.js 進行存取](virtual-machines-linux-nodejs-running-cassandra.md)
5. Redis
    - [部落格：Azure Redis 快取服務中的 Redis on Windows](http://msopentech.com/blog/2014/05/12/redis-on-windows/)
    - [部落格：宣佈 Redis 預覽版本的 ASP.NET 工作階段狀態提供者](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)
6. RavenHQ
    - [部落格：Azure Marketplace 現已提供 RavenHQ](http://azure.microsoft.com/blog/2014/08/12/ravenhq-now-available-in-the-azure-store/)

### 巨量資料
2. Hadoop/Cloudera  
	- [部落格：在 Azure Linux VM 上安裝 Hadoop](http://blogs.msdn.com/b/benjguin/archive/2013/04/05/how-to-install-hadoop-on-windows-azure-linux-virtual-machines.aspx)
	- [做法：透過 HDInsight 開始使用 Hadoop 與 Hive](hdinsight-get-started.md)  
3. [Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) -- Azure 上完全受管理的 Hadoop 服務。

### 關聯式資料庫
2. MySQL
    - [做法：安裝和執行 MySQL](virtual-machines-linux-mysql-use-opensuse.md)
    - [做法：在 Azure 上最佳化 MySQL 的效能](virtual-machines-linux-optimize-mysql-perf.md)
    - [做法：MySQL 叢集](virtual-machines-linux-mysql-cluster.md)
    - [做法：透過 Marketplace 建立 MySQL 資料庫](store-php-create-mysql-database.md)
    - [做法：Azure 網站上採用 Python 和 Visual Studio 的 Django 和 MySQL](web-sites-python-ptvs-django-mysql.md)
    - [做法：Azure 網站上採用 WebMatrix 的 PHP 和 MySQL](web-sites-php-mysql-use-webmatrix.md)
    - [Microsoft Azure 中的 MySQL 高可用性架構](http://download.microsoft.com/download/6/1/C/61C0E37C-F252-4B33-9557-42B90BA3E472/MySQL_HADR_solution_in_Azure.pdf)
7. MariaDB
    - [做法：建立 MariaDb 的多重主機叢集](virtual-machines-mariadb-cluster.md)
8. [使用 corosync 安裝 Postgres，使用 ILB 安裝 pg_bouncer](https://github.com/chgeuer/postgres-azure)


## 驗證和加密

驗證和加密是軟體開發的重要主題，而網路上有許多描述如何在驗證和加密時使用適當安全性技術的主題。我們會描述一些基本使用方式，以便快速執行 Linux 和開放原始碼工作負載，並著重於 Azure 上用來重設或移除遠端安全性功能的工具。這些是基本程序，而我們即將加入更複雜的案例。

4. [基本概念：憑證的使用和管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)
7. [基本概念：SSH](virtual-machines-linux-use-ssh-key.md)
8. [基本概念：如何重設 Linux 的密碼或 SSH 屬性](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)
9. [基本概念：使用 Root](virtual-machines-linux-use-root-privileges.md)

## 開發、管理和最佳化

這一節的開頭是包含一系列影片的部落格文章︰[影片：Azure 虛擬機器︰使用 Chef、Puppet 和 Docker 管理 Linux VM](http://azure.microsoft.com/blog/2014/12/15/azure-virtual-machines-using-chef-puppet-and-docker-for-managing-linux-vms/)。不過，開發、管理和最佳化的領域相當廣泛、瞬息萬變，因此您應考慮從下列清單著手。

1. Docker
	- [Azure 上 Linux 的 Docker VM 延伸模組](virtual-machines-docker-vm-extension.md)
	- [透過 Azure 命令列介面 (Azure CL) 使用 Docker VM 延伸模組](virtual-machines-docker-with-../xplat-cli.md)
	- [從 Azure Preview 入口網站使用 Docker VM 延伸模組](virtual-machines-docker-with-portal.md)
	- [在 Azure Marketplace 中快速開始使用 Docker](virtual-machines-docker-ubuntu-quickstart.md)
	- [如何在 Azure 上使用 docker-machine](virtual-machines-docker-machine.md)
	- [如何在 Azure 上搭配 swarm 使用 docker](virtual-machines-docker-swarm.md)
	- [在 Azure 上開始使用 Docker 和 Compose](virtual-machines-docker-compose-quickstart.md)

2. [CoreOS 快速入門](virtual-machines-linux-coreos-how-to.md)
3. Deis
	- [GitHub 儲存機制︰在 Azure 的 CoreOS 叢集上安裝 Deis](https://github.com/chanezon/azure-linux/tree/master/coreos/deis)
4. Kubernetes
	- [使用 CoreOS 和 Weave 自動部署 Kubernetes 叢集的完整指南](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/docs/getting-started-guides/coreos/azure/README.md#kubernetes-on-azure-with-coreos-and-weave)
	- [Kubernetes Visualizer](http://azure.microsoft.com/blog/2014/08/28/hackathon-with-kubernetes-on-azure)
5. Jenkins 和 Hudson
	- [部落格：適用於 Azure 的 Jenkins 從屬外掛程式](http://msopentech.com/blog/2014/09/23/announcing-jenkins-slave-plugin-azure/)
	- [GitHub 儲存機制︰適用於 Azure 的 Jenkins 儲存體外掛程式](https://github.com/jenkinsci/windows-azure-storage-plugin)
	- [協力廠商︰適用於 Azure 的 Hudson 從屬外掛程式](http://wiki.hudson-ci.org/display/HUDSON/Azure+Slave+Plugin)
	- [協力廠商︰適用於 Azure 的 Hudson 儲存體外掛程式](https://github.com/hudson3-plugins/windows-azure-storage-plugin)
10. Chef
	- [Chef 和虛擬機器](virtual-machines-windows-install-chef-client.md)
	- [影片：Chef 是什麼，以及如何運作？](https://msopentech.com/blog/2014/03/31/using-chef-to-manage-azure-resources/)

12. Azure 自動化
	- [影片：如何在 Linux VM 上使用 Azure 自動化](http://channel9.msdn.com/Shows/Azure-Friday/Azure-Automation-104-managing-Linux-and-creating-Modules-with-Joe-Levy)
13. Powershell DSC for Linux
    - [部落格：如何使用 Powershell DSC for Linux](http://blogs.technet.com/b/privatecloud/archive/2014/05/19/powershell-dsc-for-linux-step-by-step.aspx)
    - [GitHub：Docker 用戶端 DSC](https://github.com/anweiss/DockerClientDSC)
13. [Ubuntu Juju](https://juju.ubuntu.com/docs/config-azure.html)
14. [Azure 的 Packer 外掛程式](https://github.com/msopentech/packer-azure)

## 支援、疑難排解和「根本沒有作用」

1. Microsoft 支援文件
	- [支援：Microsoft Azure 上的 Linux 映像支援](http://support2.microsoft.com/kb/2941892)

<!--Anchors-->
[Distros]: #distros
[The Basics]: #basics
[Community Images and Repositories]: #images
[Languages and Platforms]: #langsandplats
[Samples and Scripts]: #samples
[Auth and Encryption]: #security
[Devops, Management, and Optimization]: #devops
[Support, Troubleshooting, and "It Just Doesn't Work"]: #supportdebug

<!--Link references--In actual articles, you only need a single period before the slash. -->
[How to use docker-machine on Azure]: virtual-machines-docker-machine.md
[How to use docker with swarm on Azure]: virtual-machines-docker-swarm.md
 

<!---HONumber=July15_HO2-->
<properties 
   pageTitle="檢視與修改主機名稱"
   description="說明"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/28/2015"
   ms.author="joaoma" />

# 檢視與修改主機名稱

若要允許主機名稱參考您的角色執行個體，您必須在各個角色的服務組態檔中設定主機名稱的值。您可以將需要的主機名稱新增到 **Role** 項目的 **vmName** 屬性。**vmName** 屬性的值會做為各個角色執行個體之主機名稱的基底。例如，如果 **vmName** 是 *webrole* 且有三個該角色的執行個體，執行個體的主機名稱將是 *webrole0*、*webrole1* 以及 *webrole2*。您不需要指定組態檔中虛擬機器的主機名稱，因為虛擬機器的主機名稱會根據虛擬機器名稱填入。如需設定 Microsoft Azure 服務的詳細資訊，請參閱 [Azure 服務組態結構描述 (.cscfg 檔)](https://msdn.microsoft.com/library/azure/ee758710.aspx)

## 檢視主機名稱

您可以使用多種工具檢視雲端服務中虛擬機器和角色執行個體的主機名稱：Azure 入口網站、服務組態檔、遠端桌面以及 [Azure 服務管理 REST API](https://msdn.microsoft.com/library/azure/ee460799.aspx)。

### Azure 入口網站

您可以使用 Azure 入口網站檢視虛擬機器儀表板頁面上虛擬機器的主機名稱。請記住，儀表板中顯示**名稱**和**主機名稱**的值。雖然它們一開始都相同，但是變更主機名稱不會變更虛擬機器或角色執行個體的名稱。

角色執行個體也能在 Azure 入口網站中找到，但是當您列出雲端服務中的執行個體時不會顯示主機名稱。您將會看到各個執行個體的名稱，但是該名稱不代表主機名稱。

### 服務組態檔

您可以從 Azure 入口網站中服務的 [設定] 頁面，下載已部署服務的服務組態檔。然後您可以尋找 **Role name** 項目的**vmName** 屬性，查看主機名稱。請記住，這個主機名稱是做為各個角色執行個體之主機名稱的基底。例如，如果 **vmName** 是 *webrole* 且有三個該角色的執行個體，執行個體的主機名稱將是 *webrole0*、*webrole1* 以及 *webrole2*。

### 遠端桌面

啟用您虛擬機器或角色執行個體的遠端桌面 (Windows)、Windows PowerShell 遠端執行功能 (Windows) 或 SSH (Linux 和 Windows) 連線之後，您可以利用多種方式檢視使用中遠端桌面連線的主機名稱：

- 在命令提示字元或 SSH 終端機中輸入主機名稱。

- 在命令提示字元中輸入 ipconfig /all (僅限 Windows)。

- 在系統設定中檢視電腦名稱 (僅限 Windows)。

### Azure 服務管理 REST API

從 REST 用戶端，請遵循下列指示：

1. 確定您有連線到 Azure 入口網站的用戶端憑證若要取得用戶端憑證，請遵循 [做法：下載與匯入發行設定與訂閱資訊](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx) 中的步驟。

1. 設定名稱為 x-ms-version，值為 2013-11-01 的標頭項目。

1. 傳送下列格式的要求：https://management.core.windows.net/\<subscrition-id>/services/hostedservices/<service-name>?embed-detail=true

1. 尋找各個 **RoleInstance** 項目的 **HostName** 項目。

>[AZURE.WARNING]您也可以透過檢查 **InternalDnsSuffix** 項目、從遠端桌面工作階段中的命令提示字元執行 ipconfig /all (Windows)，或從 SSH 終端機執行 cat /etc/resolv.conf (Linux)，檢視 REST 呼叫回應中您雲端服務的內部網域尾碼。

## 修改主機名稱

您可以透過上傳已修改的服務組態檔，或是從遠端桌面工作階段重新命名電腦，來修改任何虛擬機器或角色執行個體的主機名稱。

## 另請參閱

[名稱解析 (DNS)](https://msdn.microsoft.com/library/azure/jj156088.aspx)

[Azure 服務組態結構描述 (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710.aspx)

[Azure 虛擬網路組態結構描述](http://go.microsoft.com/fwlink/?LinkId=248093)

[使用網路組態檔設定虛擬網路](http://go.microsoft.com/fwlink/?LinkId=248094)

<!---HONumber=July15_HO2-->
<properties
   pageTitle="使用適用於 Mac、Linux 和 Windows 的 Azure CLI 部署範本 | Microsoft Azure"
   description="說明部署或更新任何範本的基本步驟。"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="infrastructure"
   ms.date="06/09/2015"
   ms.author="danlep"/>

# 使用適用於 Mac、Linux 和 Windows 的 Azure CLI 部署範本

## 安裝 curl、wget 或其他下載工具
本主題使用 **curl**。使用作業系統的封裝管理員，或從[這裡](http://curl.haxx.se/download.html)下載。

## 下載範本參數檔案 (或是視需要下載範本)

下列步驟可讓您部署一個 Azure 範本，即使它是一個複雜的範本。本主題使用範本來建立已安裝 customscript VM 延伸模組的基本 Ubuntu 伺服器做為範例。主題結尾處也會包含檔案，以供參考。

### 下載 azuredeploy-parameters.json 檔案

如果有一個適用於您想要部署之範本的 azuredeploy-parameters.json 檔案存在，請下載它。

    curl -O https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy-parameters.json

## 輸入您的資源群組部署資訊

使用您最愛的編輯器來開啟這個檔案。您將會看到您需要為數個索引鍵指定值，特別是 **adminUsername**、**adminPassword** (請記住複雜性規則！)，以及儲存體帳戶名稱和您想要的 DNS 名稱。

    {
      "newStorageAccountName": {
        "value": "uniquestorageaccountname"
      },
      "adminUsername": {
        "value": ""
      },
      "adminPassword": {
        "value": ""
      },
      "dnsNameForPublicIP": {
        "value": "uniquednsnameforpublicip"
      },
      "vmSourceImageName": {
        "value": "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_2_LTS-amd64-server-20150309-zh-tw-30GB"
      },
      "location": {
        "value": "West US"
      },
      "virtualNetworkName": {
        "value": "myVNET"
      },
      "vmSize": {
        "value": "Standard_A0"
      },
      "vmName": {
        "value": "myVM"
      },
      "publicIPAddressName": {
        "value": "myPublicIP"
      },
      "nicName": {
        "value": "myNic"
      }
    }

加入新值 (如果可以，Azure 將會為您建立新的儲存體和 DNS 資源) 或是使用您已經建立的資源。下列 azuredeploy-parameters.json 檔案會示範範例：




下列的 url 會從「空的」azuredeploy-parameters.json 抓取參數檔，如果它們使用的是互動式方法，這將可運作。如果它們使用的是下載和自訂參數檔案的方法，則需要改用 --template-file <template-file> 選項。此外，我還撰寫了指令碼，根據您想要執行的動作來擷取這些檔案之任何部分中的個別區段。您可能想說，為了剖析 json，它們可能想要 jq: curl http://stedolan.github.io/jq/download/linux64/jq -o /usr/bin/jq


### 部署範本和參數檔案


[AZURE.NOTE]您或許發現有些範本可能沒有對應的 azuredeploy-parameters.json 檔案。

要設定的參數，或者根據您使用的是哪些範本而定，它們可能已經是範本本身的一部分。在這些情況下，您可能

如果您的範本直接包含它的參數，或者您想要自行擷取參數資料。如需範本結構的詳細資訊，請參閱 [Azure 資源管理員範本語言](https://msdn.microsoft.com/library/azure/dn835138.aspx)。


https://github.com/azure/azurermtemplates/raw/master/linux-virtual-machine-with-customdata/azuredeploy.json (或只有 azuredeploy-parameters.json 檔案) 您可以擷取範本的參數區段 -- 在這種情況下，您需要將它儲存回範本本身內部，或者儲存為個別的 azuredeploy-parameters.json 檔案。您需要取得要放入參數的值。

## 修改 azuredeploy-templates.json 檔案

收集您需要的值

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

Vestibul ante ipsum primis in faucibus orci luctus et ultrices posuere cubilia Curae; Nullam ultricies, ipsum vitae volutpat hendrerit, purus diam pretium eros, vitae tincidunt nulla lorem sed turpis：[連結 3 到另一個 azure.microsoft.com documentation 主題](../storage-whatis-account.md)。

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png
 

<!---HONumber=July15_HO2-->
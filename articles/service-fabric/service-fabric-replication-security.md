<properties
   pageTitle="保護 Azure Service Fabric 中具狀態服務的複寫流量"
   description="啟用複寫時，具狀態服務會將其狀態從主要複本複寫至次要複本，這類流量必須受到保護，以防竊聽和竄改。"
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# 保護複寫流量的安全

啟用複寫時，具狀態服務會在複本間複寫狀態。此頁面說明如何設定這類流量的保護。

支援下列兩種類型的安全性設定：

- X509：使用 x509 憑證來保護通訊通道的安全。使用者預期使用 ACL 憑證私密金鑰，以讓執行者/服務主機處理序能夠使用憑證認證。
- Windows：使用 Windows 安全性 (需具備 Active Directory) 來保護通訊通道的安全。

下一節說明如何設定上述兩種類型的設定。組態 "CredentialType" 可判斷正在使用哪種類型。

## CredentialType=X509

### 組態名稱

|名稱|備註|
|----|-------|
|StoreLocation|要尋找憑證的存放區位置：CurrentUser 或 LocalMachine|
|StoreName|要尋找憑證的存放區名稱|
|FindType|識別 FindValue 參數中所提供的值類型：FindBySubjectName 或 FindByThumbPrint|
|FindValue|搜尋目標以尋找憑證|
|AllowedCommonNames|複寫器使用的憑證常見名稱/DNS 名稱清單 (以逗號分隔)。|
|IssuerThumbprints|簽發者憑證指紋的清單 (以逗號分隔)。指定時，如果內送憑證是由清單內其中一個項目發出，則會通過驗證。一律執行鏈結驗證。|
|RemoteCertThumbprints|複寫器使用的憑證指紋清單 (以逗號分隔)。|
|ProtectionLevel|指出如何保護資料：Sign 或 EncryptAndSign 或 None|

## CredentialType=Windows

### 組態名稱

|名稱|備註|
|----|-------|
|ServicePrincipalName|註冊此服務的服務主體名稱。如果服務/行動主機處理序是以電腦帳戶執行 (例如：NetworkService、LocalSystem 等)，則可為空白。|
|WindowsIdentities|所有服務/行動主機處理序的 Windows 身分識別清單 (以逗號分隔)。
|ProtectionLevel|指出如何保護資料：Sign 或 EncryptAndSign 或 None|

## 範例

### 範例 1：CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### 範例 2：CredentialType=Windows
此程式碼片段顯示所有服務/行動主機處理序都以 NetworkService 或 LocalSystem 執行時的範例。ServicePrincipalName 是空的。

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### 範例 3： CredentialType=Windows
此程式碼片段顯示當服務/行動主機處理序以群組管理的服務帳戶 (具有效 ServicePrincipalName) 執行時的範例。

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=July15_HO2-->
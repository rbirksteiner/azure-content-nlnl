<properties
   pageTitle="Service Fabric 可靠動作項目 KVSActorStateProvider 組態概觀"
   description="深入了解設定 'KVSActorStateProvider' 類型的 Service Fabric 可設定狀態的動作項目"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/20/2015"
   ms.author="sumukhs"/>

# 可靠動作項目組態 - KVSActorStateProvider
您可以在有問題之指定動作項目的 "Config" 資料夾下，變更 Visual Studio 封裝根中所產生的 "settings.xml"，來修改 KVSActorStateProvider 的預設組態。

Service-Fabric 執行階段會查尋 "settings.xml" 檔案中預先定義的區段名稱，並在建立基礎執行階段元件時耗用組態值。

> [AZURE.NOTE]**不要**在 Visual Studio 方案所產生的 "settings.xml" 檔案中刪除/修改下列組態的區段名稱。

## 複寫器安全性組態
複寫器安全性組態用來保護在複寫期間使用的通訊通道。這表示服務將無法看到彼此的複寫流量，並且也會確保高度可用資料的安全。依預設，空白的安全性組態區段不會啟用複寫安全性。
### 區段名稱
& l t;ActorName & g t;ServiceReplicatorSecurityConfig
### 組態名稱
請參閱＜[複寫安全性](../service-fabric/service-fabric-replication-security.md)＞

## 複寫器組態
複寫器組態用於設定負責讓動作項目狀態提供者高度可靠的複寫器。預設組態由 Visual Studio 範本產生，且應已足夠使用。本節說明可用於微調複寫器的其他組態。
### 區段名稱
&lt;ActorName&gt;ServiceReplicatorConfig
### 組態名稱

|名稱|單位|預設值|備註|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|秒|0.05|次要複寫器收到作業後，將通知傳回給主要複寫器前所等待的時間間隔。任何要在此間隔內傳送給作業處理的其他通知，會集中以一個回應傳送。|
|ReplicatorEndpoint|N/A|N/A - RequiredParameter|主要/次要複寫器將用於與複本集中其他複寫器通訊的 IP 位址與連接埠。這應該參考服務資訊清單中的 TCP 資源端點。請參閱＜[服務資訊清單資源](service-fabric-service-manifest-resources.md)＞，深入瞭解如何在服務資訊清單中定義端點資源。 |
|RetryInterval|秒|5|複寫器若未收到作業通知，在重新傳輸訊息前的時間間隔。|
|MaxReplicationMessageSize|位元組|50MB|單一訊息可傳輸的複寫資料大小上限。|
|MaxPrimaryReplicationQueueSize|作業數目|1024|主要佇列中作業數目上限。主要複寫器收到所有次要複寫器的通知後，系統便會釋放作業。此值必須大於 64 且為 2 的乘冪。|
|MaxSecondaryReplicationQueueSize|作業數目|2048|次要佇列中作業數目上限。透過持續性讓狀態成為高可用性後，系統便會釋放作業。此值必須大於 64 且為 2 的乘冪。|

## 存放區組態
存放區組態用於設定本機存放區以用來保存正在複寫的狀態。預設組態由 Visual Studio 範本產生，且應已足夠使用。本節將討論其他可用來微調本機存放區的組態。
### 區段名稱
&lt;ActorName&gt;ServiceLocalStoreConfig
### 組態名稱

|名稱|單位|預設值|備註|
|----|----|-------------|-------|
|MaxAsyncCommitDelay|毫秒|200|設定長期本機存放區認可的批次間隔上限。|
|MaxVerPages|頁面數目|8192|本機存放區資料庫中版本頁面數上限。其會判定未完成交易數上限。|

## 範例組態檔

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```
## 備註

BatchAcknowledgementInterval 會控制複寫延遲性。值為 '0' 時延遲可能性最低，但代價是降低輸送量 (隨著必須傳送與處理的通知訊息增加，每個訊息包含的通知會變少)。BatchAcknowledgementInterval 的值越大，整體複寫輸送量越高，代價是作業延遲變高。這可直接解讀為交易認可延遲。
 

<!---HONumber=July15_HO2-->
<properties
   pageTitle="Service Fabric Reliable Actor 的 'ReliableDictionaryActortateProvider' 組態概觀"
   description="深入了解設定 'ReliableDictionaryActortateProvider' 類型的 Service Fabric 可設定狀態的動作項目"
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

# 設定可靠動作項目 - ReliableDictionaryActortateProvider
您可以變更 Visual Studio 封裝根目錄在 [Config] 資料夾下為指定動作項目產生的「settings.xml」檔案，來修改 ReliableDictionaryActortateProvider 的預設組態。

Service-Fabric 執行階段會查尋 "settings.xml" 檔案中預先定義的區段名稱，並在建立基礎執行階段元件時耗用組態值。

> [AZURE.NOTE]**不要**在 Visual Studio 方案所產生的 "settings.xml" 檔案中刪除/修改下列組態的區段名稱。

## 複寫器安全性組態
複寫器安全性組態用來保護在複寫期間使用的通訊通道。這表示服務將無法看到彼此的複寫流量，並且也會確保高度可用資料的安全。依預設，空白的安全性組態區段不會啟用複寫安全性。
### 區段名稱
& l t;ActorName & g t;ServiceReplicatorSecurityConfig
### 組態名稱
請參閱＜[複寫安全性](../service-fabric/service-fabric-replication-security.md)＞

## 複寫器組態
複寫器組態用來設定複寫器，並負責將狀態複寫和保存至本機，讓動作項目狀態提供者變得高度可靠。預設組態由 Visual Studio 範本產生，且應已足夠使用。本節說明可用於微調複寫器的其他組態。
### 區段名稱
&lt;ActorName&gt;ServiceReplicatorConfig
### 組態名稱

|名稱|單位|預設值|備註|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|秒|0.05|次要複寫器收到作業後，將通知傳回給主要複寫器前所等待的時間間隔。任何要在此間隔內傳送給作業處理的其他通知，會集中以一個回應傳送。||
|ReplicatorEndpoint|N/A|N/A - RequiredParameter|主要/次要複寫器將用於與複本集中其他複寫器通訊的 IP 位址與連接埠。這應該參考服務資訊清單中的 TCP 資源端點。請參閱＜[服務資訊清單資源](service-fabric-service-manifest-resources.md)＞，深入瞭解如何在服務資訊清單中定義端點資源。 |
|RetryInterval|秒|5|複寫器若未收到作業通知，在重新傳輸訊息前的時間間隔。|
|MaxReplicationMessageSize|位元組|50MB|單一訊息可傳輸的複寫資料大小上限。|
|MaxPrimaryReplicationQueueSize|作業數目|1024|主要佇列中作業數目上限。主要複寫器收到所有次要複寫器的通知後，系統便會釋放作業。此值必須大於 64 且為 2 的乘冪。|
|MaxSecondaryReplicationQueueSize|作業數目|2048|次要佇列中作業數目上限。透過持續性讓狀態成為高可用性後，系統便會釋放作業。此值必須大於 64 且為 2 的乘冪。|
|MaxStreamSizeInMB|MB|1024|已保留的記錄檔空間量。以位元組為單位的大小，必須大於 MaxRecordSize 以位元組為單位的 16 倍。|
|MaxRecordSizeInKB|KB|1024|複寫器可以寫入記錄檔中的最大記錄大小。此值必須是 4 的倍數且大於 16。|
|OptimizeForLocalSSD|布林值|false|為 True 時，將會設定記錄檔，以讓狀態資訊直接寫入複本的專用記錄檔。當記錄檔在固態磁碟媒體上，或當 VM 的磁碟 IO 速率受到高度節流控制時，將會提供最佳效能。為 false 時，狀態資訊會先暫置在共用的記錄檔中，然後轉出到專用的記錄檔。|
|OptimizeLogForLowerDiskUsage|布林值|false|為 true 時，記錄檔設定為使用 NTFS 疏鬆檔案來建立複本的專用記錄檔。這會降低檔案實際使用的磁碟空間量。為 false 時，將以固定配置建立檔案，並提供最佳寫入效能。|
|SharedLogId|guid|""|指定用於識別此複本共用記錄檔的唯一 GUID。服務通常不應使用此設定，不過如果指定了 SharedLogId，則也必須指定 SharedLogPath。|
|SharedLogPath|完整路徑名稱|""|指定建立此複本共用記錄檔的完整路徑。服務通常不應使用此設定，不過如果指定了 SharedLogPath，則也必須指定 SharedLogId。|


## 範例組態檔

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="MaxStreamSizeInMB" Value="512" />
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
BatchAcknowledgementInterval 會控制複寫延遲性。值為 '0' 時延遲可能性最低，但代價是降低輸送量 (隨著必須傳送與處理的通知訊息增加，每個訊息包含的通知會變少)。BatchAcknowledgementInterval 的值越大，整體複寫輸送量越高，代價是作業延遲變高。這會直接轉換成交易認可的延遲。

MaxStreamSizeInMB 的值決定複寫器可用來將狀態資訊儲存在複本專用記錄檔中的磁碟空間量。由於儲存的資訊狀態是用來允許另一個複本符合主要複本的狀態，因此一般來說最好能擁有較大的記錄檔，因為這樣會減少另一個複本符合主要狀態所花費的時間。不過較大的記錄檔可能會使用更多磁碟空間，因此減少可以在特定節點裝載的複本數目。

OptimizeForLowerDiskUsage 設定允許「過度佈建」記錄檔空間，讓作用中的複本可以在其記錄檔中儲存較多狀態資訊，而非作用中的複本則會使用較少的磁碟空間。雖然這允許在一個節點上裝載更多複本，但不使用時將造成磁碟空間不足，而透過將 OptimizeForLowerDiskUsage 設定為 False，狀態資訊會更快速地寫入記錄檔。

OptimizeForLocalSSD 設定用來停用狀態資訊先寫入共用記錄檔，才轉出到專用的記錄檔。通常應於專用記錄檔的本機磁碟儲存體為固態媒體時設定，因為降低磁頭移動量並不會有問題。其設定也能在 VM 執行大量本機磁碟 IO，和本機磁碟 IO 速率大幅受到 VM 主機節流時使用。

MaxRecordSizeInKB 定義複寫器可以寫入記錄檔的記錄大小上限。在大部分情況下，預設的 1024 KB 記錄大小是最佳作法，不過如果服務造成更大的資料項目成為狀態資訊的一部分，則可能需要增加此值。讓 MaxRecordSizeInKB 小於 1024 的好處不大，因為較小的記錄只會使用需要較小記錄的空間。預期只會在極少數的情況下需要變更。

SharedLogId 和 SharedLogPath 設定永遠會一起使用，並允許服務使用與節點預設共用記錄檔不同的共用記錄檔。如需最佳效率，請儘可能讓所有服務指定相同的共用記錄檔。共用記錄檔應該放在共用記錄檔專用的磁碟上，以減少磁頭移動爭用情形。預期只會在極少數的情況下需要變更。
 

<!---HONumber=July15_HO2-->
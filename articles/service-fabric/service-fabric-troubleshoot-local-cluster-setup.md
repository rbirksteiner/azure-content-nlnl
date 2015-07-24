<properties
   pageTitle="疑難排解本機叢集設定"
   description="本文涵蓋了一組疑難排解本機開發叢集的建議"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2015"
   ms.author="seanmck"/>

# 疑難排解本機開發叢集設定

如果您在與本機開發叢集互動時遇到問題，請檢閱下列建議，以尋求可能的解決方法。

## 叢集設定失敗

### 無法清除 Service Fabric 記錄檔

#### 問題

執行 DevClusterSetup 指令碼時，看到類似以下錯誤：

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### 解決方法

關閉目前的 Powershell 視窗，並以系統管理員身分啟動新的 Powershell 視窗。您現在應該能夠成功執行指令碼。

## 叢集連接失敗

### 類型初始化例外狀況

#### 問題

在 PowerShell 或 Service Fabric 總管中連接到叢集時，看到 System.Fabric.Common.AppTrace 的 TypeInitializationException。

#### 解決方法

在安裝期間未正確設定路徑變數。請登出 Windows，再重新登入。這樣會完全重新整理您的路徑。

### 叢集連接失敗，且出現「物件已關閉」

#### 問題

呼叫 Connect-ServiceFabricCluster 失敗，且出現類似以下錯誤：

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### 解決方法

關閉目前的 Powershell 視窗，並以系統管理員身分啟動新的 Powershell 視窗。您現在應該能夠成功連接。

### FabricConnectionDeniedException

#### 問題

從 Visual Studio 偵錯時，看見 FabricConnectionDeniedException。

#### 解決方法

當您嘗試以手動方式啟動服務主機處理序，而非讓 Service Fabric 執行階段啟動時，通常會發生這個錯誤。

請確定您的解決方法中沒有任何設定為啟始專案的服務專案。只有 Service Fabric 應用程式專案才可設為啟始專案。


## 後續步驟

- [透過系統健康情況報告了解及疑難排解您的叢集](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [使用 Service Fabric 總管視覺化叢集](service-fabric-visualizing-your-cluster.md)
 

<!---HONumber=July15_HO2-->
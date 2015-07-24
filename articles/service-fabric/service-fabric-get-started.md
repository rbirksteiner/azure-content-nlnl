<properties
   pageTitle="設定 Service Fabric 開發環境"
   description="安裝 Service Fabric 執行階段、SDK 和工具，並建立本機開發叢集。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/04/2015"
   ms.author="seanmck"/>

# 設定 Service Fabric 開發環境
 本文章涵蓋所有開始建置 [Service Fabric][1] 應用程式的所需資訊，包環安裝執行階段、SDK 和工具，以及設定本機叢集。

## 必要條件
### 支援的作業系統版本
下列為支援的作業系統版本：

- Windows 8/8.1
- Windows Server 2012 R2
- Windows 10 Technical Preview

### Visual Studio 2015

適用於 Service Fabric Preview 1 的工具取決於 Visual Studio 2015 RC，您可以在[這裡][2]找到。

> [AZURE.NOTE]若您並未執行其中一個支援的作業系統版本，或不想在您的電腦上安裝 Visual Studio 2015 RC，則您可以[設定 Azure 虛擬機器][3] 搭配 Windows Server 2012 R2 和從 VM 資源庫使用映像預先安裝的 Visual Studio 2015。

## 安裝執行階段、SDK 和工具

Service Fabric 元件的安裝是由 Web Platform Installer 所完成。遵循這些指示來進行安裝：

1. 按一下[這裡][4] 以下載使用 Web Platform Installer 的 SDK。

2. 按一下 [安裝] 開始安裝程序。

3. 檢閱並接受使用者授權合約。

系統將自動繼續安裝。

## 啟用 PowerShell 指令碼執行

Service Fabric 會使用 Windows PowerShell 指令碼，以便建立本機開發叢集，以及從 Visual Studio 部署應用程式。根據預設，Windows 會封鎖這些指令碼的執行。若要啟用其，您必須修改 PowerShell 執行原則。以系統管理員身分開啟 PowerShell 並輸入下列命令：

    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser


## 安裝並啟動本機叢集
本機叢集代表最後用於單一開發電腦上生產環境中的多個電腦拓樸。若要設定本機叢集，請遵循下列步驟：


1. 關閉所有其他的 PowerShell 視窗，並以系統管理員身分啟動一個新作業。

2. 瀏覽至包含 `cd "$env:ProgramW6432\Microsoft SDKs\Service Fabric\ClusterSetup"` 的叢集安裝目錄

3. 執行 `.\DevClusterSetup.ps1`

幾分鐘後，您應該會看到顯示節點資訊以及確認叢集已成功建立的輸出。在某些情況中，當 Service Fabric 主機服務和命名服務啟動時，您可能會看到警告。這是正常情況，並且後面會短暫地顯示某些叢集基本資訊。

> [AZURE.NOTE]您的本機叢集會使用與在 Azure 中執行時，完全相同的執行階段。這並非以任意方式進行虛擬或模擬。唯一的差別在於您的所有節點會在單一電腦上執行，而非像在 Azure 中時分散到多個電腦上執行。

## 驗證叢集設定

您可以使用 SDK 隨附的 Service Fabric 總管工具，檢查您的叢集是否已成功建立。

1. 執行 `. "$env:ProgramW6432\Microsoft SDKs\Service Fabric\Tools\ServiceFabricExplorer\ServiceFabricExplorer.exe"` 來啟動 Service Fabric 總管

2. 展開在左上角的 [Onebox/本機叢集] 節點。

3. 請確定 [應用程式] 和 [節點] 檢視皆顯示為綠色。

如果任何元素並非顯示為綠色，或者您看見錯誤，則請稍待片刻，然後按一下 [重新整理] 按鈕。如果您仍然有問題，請參閱＜[設定疑難排解步驟](service-fabric-troubleshoot-local-cluster-setup.md)＞。

## 後續步驟
現在您的開發環境已完成設定，您可以開始建置和執行應用程式。

- [深入了解程式設計模型：可靠動作項目和可靠服務](service-fabric-choose-framework.md)
- [開始使用可靠服務 API](service-fabric-reliable-services-quick-start.md)
- [開始使用可靠動作項目 API](service-fabric-reliable-actors-get-started.md)
- [請參閱 GitHub 上的 Service Fabric 範例](https://github.com/azure/servicefabric-samples)
- [使用 Service Fabric 總管視覺化叢集](service-fabric-visualizing-your-cluster.md)

[1]: http://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric 活動頁面"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[3]: http://blogs.msdn.com/b/visualstudioalm/archive/2014/06/04/visual-studio-14-ctp-now-available-in-the-virtual-machine-azure-gallery.aspx "Azure VM"
[4]: http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric "WebPI 連結"
 

<!---HONumber=July15_HO2-->
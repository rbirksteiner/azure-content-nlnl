<properties 
	pageTitle="什麼是 Azure .NET SDK" 
	description="了解 Azure .NET SDK 包含的項目。" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/11/2015" 
	ms.author="tdykstra"/>

# 什麼是 Azure SDK for .NET？

## 概觀

Azure SDK for .NET 是一組 Visual Studio 工具、命令列工具、執行階段二進位檔及用戶端程式庫，可協助您開發、測試和部署在 Azure 上執行的應用程式。本文將詳細說明安裝 SDK 後您會有什麼收穫。您可以從 [Azure 下載頁面](/downloads/)下載 SDK。

Azure SDK for .NET 還包含[用來使用 Azure 服務的用戶端程式庫](http://go.microsoft.com/fwlink/?LinkId=510472)這些程式庫可使用 NuGet (英文) 個別進行安裝。

##<a id="included"></a>Azure SDK for .NET 包含的內容

Azure SDK for .NET 將安裝下列產品：

- [Visual Studio Express for Web](#vwd)
- [Microsoft ASP.NET 和 Web Tools for Visual Studio](#wte)
- [Microsoft Azure Tools for Microsoft Visual Studio](#tools)
- [Microsoft Azure 製作工具](#auth)
- [Microsoft Azure 模擬器](#emulator)
- [Microsoft Azure 儲存體模擬器](#stgemulator)
- [Microsoft Azure 儲存體工具](#stgtools)
- [Microsoft Azure Libraries for .NET](#libraries)
- [HDInsight Tools for Visual Studio](#hdinsight) 和 [Microsoft Hive ODBC 驅動程式](#hdinsight)
- [Microsoft Azure Mobile App SDK V1.0](#mobile)
- [Microsoft Azure PowerShell](#ps)

###<a id="vwd"></a>Visual Studio Express for Web

如果您的電腦上沒有 Visual Studio，SDK 將會安裝 [Visual Studio Express for Web](http://www.visualstudio.com/products/visual-studio-express-vs.aspx)。
 
###<a id="wte"></a>Microsoft ASP.NET 和 Web Tools for Visual Studio

這可讓您使用 Azure 網站：

* [發行 Web 專案至 Azure 網站](web-sites-dotnet-get-started.md) (英文)。
* [發行主控台應用程式專案至 Azure WebJobs](websites-dotnet-deploy-webjobs.md) (英文)。
* [在建立新的 Web 專案或發行 Web 專案時，建立 Azure 網站和 SQL Database 資源](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) (英文)。
* [使用 Windows PowerShell 指令碼來發行至開發和測試環境](http://msdn.microsoft.com/library/dn642480.aspx)。
* [在伺服器總管中管理和疑難排解 Azure 網站](web-sites-dotnet-troubleshoot-visual-studio.md#sitemanagement) (英文)。
* [針對網站和 WebJobs 以偵錯模式從遠端執行](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug) (英文)。 

>[AZURE.NOTE]您不一定要安裝 Azure SDK for .NET 才能使用這些功能；Visual Studio 的更新中也包括這些功能。

###<a id="tools"></a>Microsoft Azure Tools for Microsoft Visual Studio

這可讓您使用 Azure 資源，主要是 Azure 雲端服務和虛擬機器：

* [建立、開啟及發行雲端服務專案](cloud-services-dotnet-get-started.md) (英文)。
* [使用 Azure Tools 發行雲端服務](http://msdn.microsoft.com/library/ff683672.aspx)。
* [在建立新的 Web 專案時，建立 Azure 虛擬機器](virtual-machines-dotnet-create-visual-studio-powershell.md) (英文)。
* [使用 Windows PowerShell 指令碼來發行至開發和測試環境](http://msdn.microsoft.com/library/dn642480.aspx)。
* [設定 Azure 專案](http://msdn.microsoft.com/library/ee405486.aspx)。
* 在 [伺服器總管]中，檢視和管理[雲端服務](http://msdn.microsoft.com/library/ff683675.aspx)、[虛擬機器](http://msdn.microsoft.com/library/jj131259.aspx)與[服務匯流排](http://msdn.microsoft.com/library/jj149828.aspx)。 
* [在 Visual Studio 中對雲端服務或虛擬機器進行偵錯](http://msdn.microsoft.com/library/ff683670.aspx)。
* [使用 Azure 資源群組部署專案自動化資源佈建](https://msdn.microsoft.com/library/dn872471.aspx)

###<a id="auth"></a>Microsoft Azure 製作工具

其包含下列工具：

* [CSPack 命令列工具](http://msdn.microsoft.com/library/gg432988.aspx)，用於建立部署封裝。
* [CSEncrypt 命令列工具](http://msdn.microsoft.com/library/hh404001.aspx)，用於加密密碼，若要透過遠端桌面連線存取雲端服務角色執行個體，便需要用到此密碼。
* 執行階段二進位檔，要有此二進位檔，雲端服務專案才能與專案執行階段環境進行通訊，也才能進行診斷。NuGet 封裝不提供這些二進位檔。

###<a id="emulator"></a>Microsoft Azure 模擬器

[Azure 模擬器](http://msdn.microsoft.com/library/dn339018.aspx)可模擬雲端服務環境，因此您可以在將雲端服務專案部署至 Azure 之前，先在您的電腦本機上進行測試。

###<a id="stgemulator"></a>Microsoft Azure 儲存體模擬器

[Azure 儲存體模擬器](http://msdn.microsoft.com/library/hh403989.aspx)使用 SQL Server 執行個體和本機檔案系統來模擬 Azure 儲存體 (佇列、資料表、Blob)，因此您可以在本機進行測試。

###<a id="stgtools"></a>Microsoft Azure 儲存體工具

這會安裝命令列工具 [AzCopy](http://aka.ms/AzCopy) (英文)，供您用來將資料傳入和傳出 Azure 儲存體帳戶。

###<a id="libraries"></a>Microsoft Azure Libraries for .NET

其包含下列工具：

* 用於 Azure 儲存體、服務匯流排及快取的 NuGet 封裝，NuGet 封裝皆儲存於您的電腦中，所以 Visual Studio 可在離線時建立新的雲端服務專案。
* Visual Studio 外掛程式，可讓[角色中快取](http://msdn.microsoft.com/library/dn386103.aspx)專案在 Visual Studio 中以本機方式執行。 

###<a id="hdinsight"></a>HDInsight Tools for Visual Studio 和 Microsoft Hive ODBC 驅動程式

伺服器總管中的 HDInsight 工具可讓您瀏覽 Hive 資料庫，和 HDInsight 叢集的連結儲存體帳戶、建立資料表，以及建立並送出 Hive 查詢。如需詳細資訊，請參閱〈[開始使用 HDInsight Hadoop Tools for Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)〉。

###<a id="mobile">Microsoft Azure Mobile App SDK

可使用 [Azure App Service Mobile Apps](app-service-mobile-value-prop-preview.md) 的工具。

###<a id="ps"></a>Microsoft Azure PowerShell

Azure PowerShell 可讓您[將 Azure 環境的建立及部署自動化](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything) (英文)。

##<a id="notincluded"></a>安裝 Azure SDK for .NET 時不包含的內容

當您安裝此 SDK 時，有一些您想用來開發 Azure 的項目並不包含在內。其中最重要的項目如下：

* [用戶端程式庫](http://go.microsoft.com/fwlink/?LinkId=510472) (英文)。 

	Azure SDK 包含用來取用 Azure 服務的用戶端程式庫，但並不是所有程式庫皆會在安裝 SDK 時加以安裝。如果 SDK 沒有安裝您應用程式所需的用戶端程式庫，您可以從 [NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) (英文) 取得。如果您的應用程式使用 SDK 所安裝的用戶端程式庫，那麼您最好以 NuGet.org 上的目前版本來更新此用戶端程式庫。

  	**用戶端程式庫的本機複本。** Azure SDK for .NET 會將適用於某些 Azure 用戶端程式庫 (例如儲存體、服務匯流排及快取等) 的 NuGet 封裝，複製到您的電腦。新的雲端服務專案會自動納入這些用戶端程式庫，因此本機的 NuGet 封裝可讓 Visual Studio 在未連線到網際網路的狀態下建立專案。一般來說，用戶端程式庫的更新頻率比發行 SDK 新版本的頻率還要高，因此 NuGet.org 上的用戶端程式庫通常會比透過 SDK 取得的版本還要新。

	**包含用戶端程式庫的專案範本。** 只有 [Azure 雲端服務](cloud-services-dotnet-get-started.md)和 [Azure 行動服務](mobile-services-dotnet-backend-windows-store-dotnet-leaderboard.md)專案範本會自動包含某些用戶端程式庫。如需其他程式庫或其他範本，請安裝您需要的[用戶端程式庫 NuGet 封裝](http://go.microsoft.com/fwlink/?LinkId=510472)。

* [Azure 行動服務專案範本](mobile-services-dotnet-backend-windows-store-dotnet-leaderboard.md) (英文)。

	只有 Visual Studio 2013 Update 2 和更新版本會提供行動服務範本。即使您安裝 Azure SDK for .NET，Visual Studio 2012 或更早版本以及 Visual Studio 2013 Update 1 或更早版本也不提供行動服務範本。

##<a id="faq"></a>常見問題集

- [Visual Studio 中已經有許多 Azure 功能。我是否還需要安裝 Azure SDK for .NET？](#azinvs)
- [我想要用戶端程式庫。我是否必須要安裝 Azure SDK for .NET 才能取得？](#clientlib)
- [哪裡可以找到舊版的 Azure SDK for .NET？](#olderversions)
- [Azure SDK for .NET 各版本的生命週期原則內容是什麼？](#lifecycle)
- [哪個客體 OS 版本與 Azure SDK for .NET 相容？](#guestos)
- [如何解除安裝 Azure SDK for .NET？](#uninstall)

###<a id="azinvs"></a>Visual Studio 中已經有許多 Azure 功能。我是否還需要安裝 Azure SDK for .NET？

如果您想要使用最新工具來開發 Azure，那麼您最好安裝此 SDK。如果您具備下列條件，也可以不安裝此 SDK：

* 已安裝最新版的 [Visual Studio 更新](http://www.visualstudio.com/downloads/download-visual-studio-vs#DownloadFamilies_5)。
* 僅開發 Azure 網站或行動服務，不開發雲端服務或虛擬機器。
* 您的應用程式沒有使用儲存體，或是有使用儲存體，但不需要儲存體模擬器或 AzCopy 工具。

###<a id="clientlib"></a>我想要用戶端程式庫。我是否必須要安裝 Azure SDK for .NET 才能取得？

此 SDK 只會安裝用戶端程式庫，因此您可以在未連線至網際網路的狀態下建立雲端服務專案。[NuGet.org](http://go.microsoft.com/fwlink/?LinkId=510472) (英文) 上的 NuGet 封裝有提供目前版本的用戶端程式庫。如需詳細資訊，請參閱本文前面的[安裝 Azure SDK for .NET 時不包含的內容](#notincluded)。

###<a id="olderversions"></a>哪裡可以找到舊版的 Azure SDK for .NET？

如需較舊的版本，請參閱 [Azure SDK for .NET](/downloads/archive-net-downloads/) 下載頁面。

###<a id="lifecycle"></a>Azure SDK for .NET 各版本的生命週期原則內容是什麼？

請參閱 [Microsoft Azure 雲端服務支援週期原則](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq)。

###<a id="guestos"></a>哪個客體 OS 版本與 Azure SDK for .NET 相容？

請參閱 [Azure 客體 OS 版本與 SDK 相容性比較表](http://msdn.microsoft.com/library/ee924680.aspx)。

###<a id="uninstall"></a>如何解除安裝 Azure SDK for .NET？

本文的＜[Azure SDK for .NET 包含的內容](#included)＞中列出的每個項目，皆為 Windows 控制台 [**程式和功能**] 的已安裝程式清單中所列的個別程式。您無法一次同時解除安裝這些項目，必須個別解除安裝每個程式。

若您已安裝 Azure SDK for.NET，且安裝的是最新版本，通常不需要解除安裝舊有版本。在大部分情況下，SDK 安裝會更新現有的程式，而不會加入新的程式及保留舊有的程式。

不過，如果您想要移除不再需要的舊版殘留部分，請只解除安裝指定舊版號碼的程式，並只在已有新版程式的情況下將舊版解除安裝。例如將「Microsoft Azure Tools for Microsoft Visual Studio 2013」從 2.5 版更新為 2.6 版之後，您可能會同時看到 2.5 和 2.6 版，這時您就可以解除安裝 2.5 版。若您只看到「Microsoft Azure 製作工具」 2.5 版，在這種情況下就不能解除安裝 2.5 版。

請注意，[**程式和功能**] 中程式標題所顯示的版本號碼可能會誤導您。例如 SDK 2.6 版包含「Microsoft Azure Mobile App SDK V1.0」，若您安裝適用於 Visual Studio 2013 的 SDK，以及安裝適用於 Visual Studio 2015 的「Microsoft Azure Mobile App SDK V2.0」，在此案例中，該版本並非 SDK 的版本，而是表示套用該程式的 Visual Studio 版本。

本文不會列出每個舊版 Azure SDK 包含的每個程式；由於較新版的 SDK 中可能未包含某些舊版中的程式，您也可以解除安裝舊版 SDK 中的這些程式。例如 2.5 版安裝了「Azure Resource Manager Tools for Visual Studio」，但本文中的清單未列出此程式，因為此程式不再屬於列於 [**程式和功能**] 中的個別程式。本文只會列出 Azure SDK for .NET 2.6 版包含的程式。

> **注意：**SDK 中包含的某些程式在其他環境下也可能是經由個別安裝，即使您不需要完整的 SDK，可能仍需用到這些功能。由舊版 SDK 安裝但在較新版的 SDK 中省略的程式也是如此。解除安裝程式時請小心避免移除電腦上仍需要的程式。



##<a id="versions"></a>版本

若要查看目前版本或下載舊版，請參閱「[Azure SDK for.NET 版本歷程記錄](/downloads/archive-net-downloads/)」頁面。

##<a id="resources"></a>資源

若要下載 Azure SDK for .NET 的目前版本或用戶端程式庫，請參閱「[Azure 下載」頁面](/downloads/)。

如需 Azure SDK for .NET 原始程式碼 (包含用戶端程式庫)，請參閱 [GitHub.com/Azure](https://github.com/azure/) (英文)。

如需 Azure 用戶端程式庫的參考文件，請參閱 [Azure .NET 參考](/documentation/api/) (英文)。

<!---HONumber=62-->
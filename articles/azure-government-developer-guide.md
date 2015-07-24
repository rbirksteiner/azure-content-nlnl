<properties 
	pageTitle="Azure Government 開發人員指南" 
	description="這為 Azure Government 的開發應用程式提供功能和指引的比較" 
	services="" 
	documentationCenter="" 
	authors="Joharve2" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="azure-government" 
	ms.date="01/21/2014" 
	ms.author="jharve"/>


#  Microsoft Azure Government 開發人員指南 

<p> Microsoft Azure Government 是 Microsoft Azure 的實體及網路隔離執行個體。此開發人員指南將針對應用程式開發人員和系統管理員需要與這些 Azure 個別區域互動及處理的差異，提供相關提供詳細資料。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## 本主題內容


+ [概觀](#Overview)
+ [開發人員指導方針](#Guidance)
+ [Microsoft Azure Government 中目前可用的功能](#Features)
+ [端點對應](#Endpoint)
+ [後續步驟](#next)


## <a name="Overview"></a>概觀

Microsoft Azure Government 是 Microsoft Azure 的獨立執行個體，用來處理美國聯邦機構、州和地方政府及其解決方案提供者的的安全性和法規遵循需求。Azure Government 提供與非美國政府部署隔離的實體及網路環境，並提供遴選過的美國工作人員。

Microsoft 提供一些工具來建立雲端應用程式並部署至 Microsoft 的全域 Microsoft Azure 服務 (全域服務) 及 Microsoft Azure Government 服務。

建立應用程式並部署至 Azure Government 服務時，由於這與全域服務相反，因此開發人員必須知道這兩種服務的主要差異。特別是針對安裝及設定其程式設計環境、設定端點、撰寫應用程式，以及將其部署為 Azure Government 的服務等方面。

本文件中的資訊將概述這些差異，並以 [Azure Government](http://www.azure.com/gov "Azure Government") 站台及 MSDN 上 [Microsoft Azure 技術文件庫](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN")中提供的資訊做補充。正式資訊也可以在其他許多位置中找到，例如 [Microsoft Azure 信任中心](http://azure.microsoft.com/support/trust-center/ "Microsoft Azure 信任中心")、[Azure 文件中心](http://azure.microsoft.com/documentation/)及 [Azure 部落格](http://azure.microsoft.com/blog/ "Azure 部落格")。

此內容的適用對象是要對 Microsoft Azure Government 進行部署的合作夥伴和開發人員。



## <a name="Guidance"></a>開發人員指導方針
由於大部分目前可用的技術內容皆假設應用程式是針對「全域服務」開發，而不是針對 Microsoft Azure Government，因此確保開發人員知道為裝載於 Azure Government 而開發的應用程式有何主要差異，便相當重要。

- 首先，有服務和功能差異，這表示「全域服務」特定區域中的某些功能可能無法在 Azure Government 中提供。

- 其次，Azure Government 中所提供的功能與「全域服務」有組態差異。因此，您應該檢閱您的範例程式碼、組態及步驟，以確保您是在「Azure Government 雲端服務」環境內建置和執行。


## <a name="Features"></a> Microsoft Azure Government 中目前可用的功能
Azure Government 目前在在「美國政府愛荷華州」(US GOV IOWA) 及「美國政府維吉尼亞州 (US GOV VIRGINIA)」區域有下列可用的服務：

- 虛擬機器
- 雲端服務
- 儲存體
- Active Directory
- 排程器
- 虛擬網路
- SQL Database

有其他服務可用，並且會持續新增更多服務。如需最新的服務清單，請參閱[區域頁面](http://azure.microsoft.com/regions/#services)，當中將醒目提示每個可用的區域及其服務。

目前，「美國政府愛荷華州」和「美國政府維吉尼亞州」是支援 Azure Government 的資料中心。如需目前的資料中心及可用的服務，請參閱上述區域頁面。

## <a name="Endpoint"></a>端點對應

將公用 Microsoft Azure 與 SQL Database 端點對應至 Azure Government 特定端點時，可使用下表做為指南。

<table>
<tr style='font-weight:bold'><td>
服務類型</td><td>	Azure 公用</td><td>	Azure Government
</td></tr><tr><td>
Azure Government 首頁</td><td>	windowsazure.com	</td><td>microsoftazure.us
</td></tr><tr><td>
管理入口網站</td><td>	manage.windowsazure.com</td><td>	manage.windowsazure.us
</td></tr><tr><td>
一般</td><td>	*.windows.net	</td><td>*.usgovcloudapi.net
</td></tr><tr><td>
核心	</td><td>*.core.windows.net	</td><td>*.core.usgovcloudapi.net
</td></tr><tr><td>
計算	</td><td>*.cloudapp.net	</td><td>*.usgovcloudapp.net
</td></tr><tr><td>
Blob 儲存體</td><td>	*.blob.core.windows.net</td><td>	*.blob.core.usgovcloudapi.net
</td></tr><tr><td>
佇列儲存體	</td><td>*.queue.core.windows.net</td><td>	*.queue.core.usgovcloudapi.net
</td></tr><tr><td>
資料表儲存體</td><td>	*.table.core.windows.net	</td><td>*.table.core.usgovcloudapi.net
</td></tr><tr><td>
服務管理</td><td>	management.core.windows.net</td><td>	management.core.usgovcloudapi.net

</td></tr>
<tr><td>SQL Database</td><td>	*.database.windows.net	</td><td>*.database.usgovcloudapi.net</td></tr>
</table>

## <a name="next"></a>接續步驟
如果您有興趣進一步了解 Azure Government 以及您組織如何取得存取資格，請到 <A href="http://azure.com/gov">http://www.azure.com/gov</a>

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=July15_HO1-->
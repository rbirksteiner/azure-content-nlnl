<properties
	pageTitle="在 Azure App Service 中建立 PHP Web 應用程式"
	description="了解如何在 Azure App Service 中建立 PHP Web 應用程式"
	documentationCenter="php"
	services="app-service\web"
	editor="mollybos"
	manager="wpickett"
	authors="tfitzmac"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="hero-article"
	ms.date="04/29/2015"
	ms.author="tomfitz"/>

# 在 Azure App Service 中建立 PHP Web 應用程式

## 概觀
本文章將顯示如何使用 [Azure Preview 入口網站](https://portal.azure.com)、[Azure CLI][Azure CLI] 或 [Azure PowerShell Cmdlet][powershell-cmdlets]，在 [Azure App Service] 中建立 PHP Web 應用程式。

一般而言，在 Azure App Service 中建立 PHP Web 應用程式與建立「任何」 Web 應用程式並無不同。依預設，所有 Web 應用程式都會啟用 PHP。如需設定 PHP (或提供您自己的自訂 PHP 執行階段) 的相關資訊，請參閱[在 Azure App Service Web Apps 中設定 PHP]。

下述每一個選項說明如何在共用託管環境中免費建立 Web 應用程式，但 CPU 使用量和頻寬使用量有所限制。如需詳細資訊，請參閱 [App Service 定價]。如需有關如何在 App Service 中升級和調整 Web 應用程式的資訊，請參閱[在 Azure App Service 中調整 Web 應用程式規模]。

>[AZURE.NOTE]如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

## 使用 Azure Preview 入口網站建立 PHP Web 應用程式

在 Azure Preview 入口網站中建立 PHP Web 應用程式時有三個選項：

- **快速建立** - 請參閱[作法：使用 Azure Preview 入口網站建立 Web 應用程式](../web-sites-create-deploy.md#createawebsiteportal)
- **使用資料庫建立** - 請參閱[在 Azure App Service 中建立 PHP-MySQL Web 應用程式並使用 Git 部署]
- **從 Marketplace** - 請參閱[在 Azure App Service 中建立 WordPress Web 應用程式]

## 使用 Azure CLI 建立 PHP Web 應用程式

若要使用 Azure CLI 建立 PHP Web 應用程式，請執行下列動作：

1. 依照下列指示來安裝 Azure CLI：[如何安裝 Azure CLI](../xplat-cli.md#install)。

1. 依照下列指示來下載和匯入發行設定檔：[如何下載和匯入發佈設定](../xplat-cli.md#configure)。

1. 從命令提示字元中執行下列命令：

		azure site create MyWebAppName

新建立的 Web 應用程式的 URL 將是 `http://MyWebAppName.azurewebsites.net`。

請注意，您可以搭配下列任何選項來執行 `azure site create` 命令：

* `--location [location name]`。此選項可讓您指定要建立 Web 應用程式的資料中心位置 (例如，[美國西部])。如果省略此選項，此命令會提示您選擇位置。
* `--hostname [custom host name]`。此選項可讓您指定 Web 應用程式的自訂主機名稱。
* `--git`。此選項可讓您在本機應用程式目錄和 Web 應用程式的資料中心建立 git 儲存機制，以利用 git 來發行至 Web 應用程式。請注意，如果本機資料夾已經是 git 儲存機制，此命令會將新的遠端新增至現有的儲存機制，並指向 Web 應用程式資料中心裡的儲存機制。

## 使用 Azure PowerShell Cmdlet 來建立 PHP Web 應用程式

若要使用 Azure PowerShell Cmdlet 來建立 PHP Web 應用程式，請執行下列動作：

1. 依照下列指示來安裝 Azure PowerShell Cmdlet：[開始使用 Azure PowerShell](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted)。

1. 依照下列指示來下載和匯入發佈設定檔：[作法：匯入發佈設定](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings)。

1. 開啟 PowerShell 命令提示字元並執行下列命令：

		New-AzureWebsite MyWebAppName

新建立的 Web 應用程式的 URL 將是 `http://MyWebAppName.azurewebsites.net`。

請注意，您可以搭配下列任何選項來執行 `New-AzureWebsite` 命令：

* `-Location [location name]`。此選項可讓您指定要建立 Web 應用程式的資料中心位置 (例如，[美國西部])。如果省略此選項，此命令會提示您選擇位置。
* `-Hostname [custom host name]`。此選項可讓您指定 Web 應用程式的自訂主機名稱。
* `-Git`。此選項可讓您在本機應用程式目錄和 Web 應用程式的資料中心建立 git 儲存機制，以利用 git 來發行至 Web 應用程式。請注意，如果本機資料夾已經是 git 儲存機制，此命令會將新的遠端新增至現有的儲存機制，並指向 Web 應用程式資料中心裡的儲存機制。

## 後續步驟

現在，您已在 Azure App Service 中建立 PHP Web 應用程式，您可以開始管理、設定、監視、部署至和調整 Web 應用程式。如需詳細資訊，請參閱下列連結：

* [在 Azure App Service 中設定 Web 應用程式](web-sites-configure.md)
* [在 Azure App Service Web Apps 中設定 PHP]
* [使用 Azure 入口網站管理 Web 應用程式](web-sites-manage.md)
* [監視 Azure App Service 中的 Web Apps](web-sites-monitor.md)
* [在 Azure App Service 中調整 Web 應用程式規模]
* [在 Azure App Service 中使用 GIT 連續部署](web-sites-publish-source-control.md)

如需完整的教學課程系列，請參閱＜[PHP 開發人員中心 - 教學課程](/develop/php/tutorials/)＞頁面 (英文)。

## 變更的項目
* 如需從網站變更為 App Service 的指南，請參閱：[Azure App Service 及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 如需從入口網站變更為預覽入口網站的指南，請參閱：[瀏覽預覽入口網站的參考](http://go.microsoft.com/fwlink/?LinkId=529715)

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Portal]: http://go.microsoft.com/fwlink/?LinkId=529715
[Azure CLI]: ../xplat-cli.md
[powershell-cmdlets]: ../powershell-install-configure.md
[在 Azure App Service Web Apps 中設定 PHP]: web-sites-php-configure.md
[在 Azure App Service 中建立 PHP-MySQL Web 應用程式並使用 Git 部署]: web-sites-php-mysql-deploy-use-git.md
[在 Azure App Service 中建立 WordPress Web 應用程式]: web-sites-php-web-site-gallery.md
[App Service 定價]: /pricing/details/app-service/
[在 Azure App Service 中調整 Web 應用程式規模]: web-sites-scale.md
 

<!---HONumber=62-->
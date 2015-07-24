<properties 
	pageTitle="搭配使用單一登入與 Azure Active Directory (PHP)" 
	description="了解如何建立使用單一登入搭配 Azure Active Directory 的 PHP Web 應用程式。" 
	services="active-directory" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="tomfitz"/>

# 利用 PHP 和 Azure Active Directory 進行單一登入

##<a name="introduction"></a>簡介

本教學課程將對 PHP 開發人員顯示如何運用 Azure Active Directory，對 Office 365 客戶的使用者啟用單一登入。您將了解如何：

* 在客戶的租用戶中佈建 Web 應用程式
* 使用 WS-同盟保護應用程式

###必要條件
此逐步解說需要下列開發環境必要條件：

* [PHP Sample Code for Azure Active Directory]
* [Eclipse PDT 3.0.x All In Ones]
* PHP 5.3.1 (透過 Web 平台安裝程式)
* Internet Information Services (IIS) 7.5 (已啟用 SSL)
* Windows PowerShell
* [Office 365 PowerShell Cmdlet]

## 步驟 1：建立 PHP 應用程式
此步驟描述如何建立將代表受保護資源的簡單 PHP 應用程式。將透過公司 STS 管理的結盟驗證，授與資源此的存取權，稍後在教學課程會有所描述。

1. 開啟新的 Eclipse 執行個體。
2. 從 [檔案] 功能表中，按一下 [新增]，然後按一下 [New PHP Project]。 
3. 在 [New PHP Project] 專案上，將專案命名為 *phpSample*，然後按一下 [完成]。
4. 從左邊的 [PHP 總管] 功能表中，在 *phpProject* 上按一下滑鼠右鍵、按一下 [新增]，然後按一下 [PHP File]。
5. 在 [New PHP File] 對話方塊上，將檔案命名為 **index.php**，然後按一下 [完成]。
6. 將產生的標記取代為下列，然後儲存 **index.php**：

		<!DOCTYPE>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index</title>
		</head>
		<body>
			##Index Page
		</body>
		</html> 

7. 在執行提示中輸入 **inetmgr**，然後按 Enter 鍵，來開啟 [Internet Information Services (IIS) 管理員]。

8. 在 IIS 管理員中，展開左功能表中 [網站] 資料夾、在 [預設網站] 上按一下滑鼠右鍵，然後按一下 [新增應用程式...]。

9. 在 [新增應用程式] 對話方塊上，將 [別名] 值設為 *phpSample*，並將 [實體路徑] 設為您建立 PHP 專案的檔案路徑。

10. 在 Eclipse 中，從 [執行] 功能表中按一下 [執行]。

11. 在 [Run PHP Web Application] 功能表上，按一下 [確定]。

12. **index.php** 頁面將在 Eclipse 的新索引標籤中開啟。頁面應該只顯示下列文字：*Index Page*。

## 步驟 2：在公司的目錄租用戶中佈建應用程式
此步驟描述 Azure Active Directory 客戶的系統管理員如何在其租用戶中佈建 PHP 應用程式，並設定單一登入。在完成此步驟之後，公司的員工可以使用其 Office 365 帳戶進行 Web 應用程式驗證。

佈建程序開始先為應用程式建立新的服務主體。服務主體是由 Azure Active Directory 用來向目錄註冊並驗證應用程式。

1. 下載並安裝 Office 365 PowerShell Commandlets (如果尚未這樣做)。
2. 從 [**開始**] 功能表中，執行 [**適用於 Windows PowerShell 的 Azure Active Directory 模組**] 主控台。此主控台提供命令行環境，用於設定 Office 365 租用戶的相關屬性，例如建立和修改服務主體。
3. 若要匯入必要的 **MSOnlineExtended** 模組，請輸入下列命令並按 Enter 鍵：

		Import-Module MSOnlineExtended -Force
4. 若要連接至 Office 365 目錄，您將需要提供公司的系統管理員認證。輸入下列命令並按 Enter 鍵，然後在提示中輸入您的認證：

		Connect-MsolService
5. 現在您將為應用程式建立新的服務主體。輸入下列命令並按 Enter 鍵：

		New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
此步驟將輸出如下的資訊：

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample PHP Website
		ServicePrincipalNames : {phpSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
> [AZURE.NOTE]您應該儲存此輸出，尤其是產生的對稱金鑰。此金鑰只會在建立服務主體期間顯示給您，而且日後您將無法擷取它。需要其他值，才能使用圖形 API 來讀取和寫入目錄中的資訊。

6. 最終步驟為您的應用程式設定回覆 URL。回覆 URL 為嘗試驗證之後傳送回應的目的地。輸入下列命令並按 Enter 鍵：

		$replyUrl = New-MsolServicePrincipalAddresses –Address "https://localhost/phpSample" 

		Set-MsolServicePrincipal –AppPrincipalId "7829c758-2bef-43df-a685-717089474505" –Addresses $replyUrl 
	
現在已在目錄中佈建 Web 應用程式，而且公司員工可以使用它，進行 Web 單一登入。

## 步驟 3：使用 WS-同盟進行員工登入來保護應用程式
此步驟顯示如何使用 Windows Identity Foundation (WIF)，以及您所下載的 simpleSAML.php 程式庫 (含必要條件中的範例程式碼)，新增結盟登入的支援。您也將新增登入頁面，並在應用程式與目錄租用戶之間設定信任。

1. 在 Eclipse 中，於 **phpSample** 專案上按一下滑鼠右鍵，按一下 [新增]，然後按一下 [檔案]。 

2. 在 [新增檔案] 對話方塊上，將檔案命名為 **federation.ini**，然後按一下 [完成]。

3. 在新的 **federation.ini** 檔案中，輸入下列資訊，同時將您建立服務主體時於步驟 2 中儲存的資訊提供給值：

		federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
		federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
		federation.trustedissuers.friendlyname=Fabrikam
		federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
		federation.realm=spn:7829c758-2bef-43df-a685-717089474505
		federation.reply=https://localhost/phpSample/index.php 


	> [AZURE.NOTE]**audienceuris** 和 **realm** 值的前面必須加上 "spn:"。

4. 在 Eclipse 中，於 **phpSample** 專案上按一下滑鼠右鍵，按一下 [新增]，然後按一下 [PHP File]。

5. 在 [New PHP File] 對話方塊上，將檔案命名為 **secureResource.php**，然後按一下 [完成]。

6. 在新的 **secureResource.php** 檔案中，輸入下列程式碼，將 **c:\phpLibraries** 路徑取代為您下載範例程式碼的根位置。根位置應該包括 **simpleSAML.php** 檔案和 **federation** 資料夾：

		<?php
		ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
		require_once ('federation/FederatedLoginManager.php');
		session_start();
		$token = $_POST['wresult'];
		$loginManager = new FederatedLoginManager();
		if (!$loginManager->isAuthenticated()) {
			if (isset ($token)) {
				try {
					$loginManager->authenticate($token);
				} catch (Exception $e) {
					print_r($e->getMessage());
				}  
			} else {
				$returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
				header('Pragma: no-cache');
				header('Cache-Control: no-cache, must-revalidate');
				header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
				exit();
			}
		}
		?> 

7. 開啟 **index.php** 頁面並更新其內容以確保頁面安全，然後儲存：

		<?php
		require_once (dirname(__FILE__) . '/secureResource.php');
		?>
		<!DOCTYPE html>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index Page</title>
		</head>
		<body>
			<h2>Index Page</h2>
			<h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
			<h4>Claim list:</h4>
			<ul>
				<?php
				foreach ($loginManager->getClaims() as $claim) {
					print_r('<li>' . $claim->toString() . '</li>');
				}
				?>  
			</ul>
		</body>
		</html> 

8. 從 [執行] 功能表中，按一下 [執行]。您應該被自動重新導向至 Office 365 身分識別提供者頁面，在此頁面中您可以使用目錄租用戶認證進行登入。例如，*john.doe@fabrikam.onmicrosoft.com*

## 摘要
本教學課程已顯示如何建立和設定單一租用戶 PHP 應用程式，來使用 Azure Active Directory 的單一登入功能。

顯示如何對 PHP 網站使用 Azure Active Directory 和單一登入的範例，可在 <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP> 取得。


[Step 1: Create a PHP Application]: #createapp
[Step 2: Provision the Application in a Company's Directory Tenant]: #provisionapp
[Step 3: Protect the Application Using WS-Federation for Employee Sign In]: #protectapp
[Summary]: #summary
[Introduction]: #introduction
[Developing Multi-Tenant Cloud Applications with Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/download/details.aspx?id=17331
[Office 365 PowerShell Cmdlet]: http://msdn.microsoft.com/library/azure/jj151815.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/download/details.aspx?id=4211
[Eclipse PDT 3.0.x All In Ones]: http://www.eclipse.org/pdt/downloads/
[PHP Sample Code for Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP
 

<!---HONumber=62-->
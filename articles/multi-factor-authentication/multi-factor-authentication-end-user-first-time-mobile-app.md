<properties 
	pageTitle="搭配 Azure MFA 使用行動應用程式做為連絡方式" 
	description="此頁面會顯示使用者如何使用行動應用程式做為 Azure MFA 的主要連絡方式。" 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="billmath"/>

# 搭配 Azure Multi-Factor Authentication 使用行動應用程式做為連絡方式

如果您要使用行動應用程式做為主要連絡方式，您可以使用這份文件。它會引導您設定 Multi-Factor Authentication，以利用行動應用程式做為主要連絡方式。

適用於 [Windows Phone](http://www.windowsphone.com/zh-tw/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50)、[Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) 和 [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458) 的 Azure Authenticator 應用程式。

## 使用行動應用程式做為連絡方式
<ol>
<li>從下拉式清單中選取行動應用程式</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)</center>

<li>選取 [通知] 或 [單次密碼]，然後按一下 [設定]。</li>
<li>在已安裝 Azure Authenticator 應用程式的電話上，啟動該應用程式並按一下 [掃描條碼]。</li>


<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)</center>


<li>掃描 [設定行動應用程式] 畫面上隨附的條碼圖。按一下 [完成] 以關閉條碼畫面。如果您無法取得要掃描的條碼，請手動輸入資訊。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode.png)</center>

<li>在電話上，它就會開始啟動，完成此作業後，請按一下 [與我連絡]。這會傳送通知或驗證碼到您的電話。按一下 [驗證]。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)</center>

<li>按一下 [關閉]。此時，您的驗證應會成功。</li>
<li>現在建議您輸入您的行動電話號碼，以防萬一您無法存取行動應用程式。<li>從下拉式清單中指定您的國家/地區，然後在國家/地區旁邊的方塊中輸入您的行動電話號碼。按 [下一步]。</li> <li>此時，您已設定好連絡方式，而現在可以設定非瀏覽器應用程式 (例如 Outlook 2010 或更舊版本) 的應用程式密碼。如果您未使用這些應用程式，請按一下 [**完成**]。否則，請繼續下一個步驟。</li>

<center>![Setup](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)</center>

<li>如果您正在使用這類應用程式，則複製提供的應用程式密碼並將此密碼貼到非瀏覽器應用程式中。如需有關個別應用程式 (如 Outlook 和 Lync) 的步驟，請參閱＜如何將您電子郵件中的密碼變更為應用程式密碼＞及＜如何將您應用程式中的密碼變更為應用程式密碼＞。</li>
<li>按一下 [完成]。</li>

<!---HONumber=July15_HO2-->
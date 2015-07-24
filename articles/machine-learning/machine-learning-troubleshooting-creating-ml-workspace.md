<properties 
	pageTitle="疑難排解：建立及連接至機器學習工作區 | Microsoft Azure" 
	description="建立及連接 Azure Machine Learning 工作區之常見問題的解決方法" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/07/2015" 
	ms.author="garye"/>


#疑難排解指南：建立及連接至機器學習服務工作區

本指南針對一些在設定 Azure Machine Learning 工作區時常發生的問題，提供解決方案。

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

##工作區擁有者

當您建立新的機器學習服務工作區時，您在 [工作區擁有者] 欄位中輸入的識別碼必須是有效的 Microsoft 帳戶 (先前稱為 Windows Live ID)，例如 john-contoso@live.com 或 john-contoso@hotmail.com。不可以使用非 Microsoft 帳戶，例如您的公司電子郵件帳戶。若要建立免費 Microsoft 帳戶，請前往 [www.live.com](http://www.live.com)。

請注意，您用來登入 Azure 入口網站來建立工作區的帳戶，不會自動具備可開啟該工作區的權限，除非您將該帳戶指定為擁有者。若要在 Machine Learning Studio 中開啟工作區，您必須使用定義為工作區擁有者的 Microsoft 帳戶登入，或者您需要收到來自擁有者的邀請，才能加入工作區。不過，您可以從 Azure 入口網站*管理*工作區，其中包括變更擁有者和設定存取的能力。

如需管理工作區的詳細資訊，請參閱[管理 Azure Machine Learning 工作區]。

[管理 Azure Machine Learning 工作區]: machine-learning-manage-workspace.md

##允許區域

機器學習服務目前可用於美國中南部地區。如果您的訂用帳戶不包括美國中南部，您可能會看見訊息「您沒有允許區域中的訂用帳戶。允許的區域：美國中南部。」

若要解決此問題，請如以下螢幕擷取畫面所示，從 Azure 管理入口網站選取 [**連絡 Microsoft 支援**]，然後在 [**支援類型**] 中選擇 [**計費**]，來將此區域加入您的訂用帳戶中。

![連絡 Microsoft 支援][screen1]

##儲存體帳戶
 
機器學習服務需要儲存體帳戶來儲存資料。您可以使用美國中南部地區中現有的儲存體帳戶，或是在建立新的機器學習服務工作區時建立新的儲存體帳戶 (如果您有建立新儲存體帳戶的配額的話)。若要查看您是否可以在管理入口網站中建立新的儲存體帳戶，請移至 [**設定**]，然後按一下 [**使用量**]。

![建立工作區][screen2]

新的機器學習服務工作區建立完成後，您可以使用指定作為工作區擁有者的 Microsoft 帳戶登入 Machine Learning Studio。如果您遇到錯誤訊息「找不到工作區」(類似於下列螢幕擷取畫面)，請使用下列步驟來刪除您的瀏覽器 Cookie。

![找不到工作區][screen3]

**刪除瀏覽器 Cookie**

	If you use Internet Explorer, click the **Tools** button in the upper-right corner and select **Internet options**.  

	![Internet options][screen4]

	Under the **General** tab, click **Delete…**

	![General tab][screen5]

	In the **Delete Browsing History** dialog box, make sure **Cookies and website data** is selected, and click **Delete**.

	![Delete cookies][screen6]

刪除 Cookie 之後，請重新啟動瀏覽器，然後前往 [Microsoft Azure Machine Learning](https://studio.azureml.net) 頁面。出現輸入使用者名稱和密碼的提示時，請輸入指定作為工作區擁有者的同一個 Microsoft 帳戶。

我們的目標是讓機器學習服務經驗盡可能完美。請將任何建議或問題貼在 [Azure Machine Learning 論壇](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning)中，以協助我們為您提供更好的服務。

[screen1]: media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]: media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]: media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]: media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]: media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]: media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png
 

<!---HONumber=July15_HO1-->
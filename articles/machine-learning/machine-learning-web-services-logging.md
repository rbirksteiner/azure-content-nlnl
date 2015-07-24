<properties 
	pageTitle="Machine Learning Web 服務的記錄 | Microsoft Azure" 
	description="了解如何為 Machine Learning Web 服務啟用記錄。記錄提供可協助疑難排解 API 的其他資訊。" 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data" 
	ms.date="06/30/2015"
	ms.author="raymondl;garye"/>

#為 Machine Learning Web 服務啟用記錄  

本文件內容提供 Azure ML Web 服務記錄功能的資訊。在 Web 服務中啟用記錄，可協助在疑難排解 API 問題時，提供比錯誤號碼和錯誤訊息更多的額外資訊。

-	如何在 Web 服務中啟用記錄：   
	-	若要啟用記錄，您必須先從 Azure 入口網站存取 [Web 服務儀表板]，然後按一下 [是]，再按一下 [儲存] 以啟用記錄。  
-	啟用記錄的效果是什麼：  
	-	記錄啟用時，所有的診斷/錯誤都會從預設端點記錄到與使用者工作區連結的 Azure 儲存體帳戶。您可以他們工作區的 Azure 入口網站 [儀表板] 檢視 (在 [快速概覽] 的底部) 中看到此儲存體帳戶 。  
-	記錄功能在哪裡可用：  
	-	目前只有為 Web 服務的預設端點啟用記錄。我們很快地就會為其他端點提供這項功能，令使用者可以在 Azure 入口網站中建立。  
-	查看記錄檔的方式：  
	-	記錄檔可使用任何可用於「探索」Azure 儲存體空間的多種工具來檢視。最簡單的方法，請直接瀏覽到 Azure 入口網站中的 [儲存體帳戶]，然後按一下 [容器] 標籤。您將會看到名為 [ML-診斷] 的容器。這個容器存放所有與此儲存體帳戶相關聯的工作區，所有 Web 端點的診斷資訊。  
-	記錄 blob 詳細資料是什麼：  
	-	在容器中的每個 blob，只會存放下列其中一項的診斷資訊：
		-	批次執行方法的執行  
		-	要求-回應方法的執行  
		-	要求-回應容器的初始化  
	-	每個 blob 的名稱都必須有下列格式的前置詞：{工作區識別碼}-{Web 服務識別碼}-{端點識別碼}/{記錄類型}  
-	記錄類型採用下列其中一個值：批次分數/要求分數/初始  

 

<!---HONumber=July15_HO2-->
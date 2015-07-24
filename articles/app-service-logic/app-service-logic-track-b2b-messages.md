<properties 
   pageTitle="追蹤 B2B 訊息" 
   description="本主題說明 B2B 處理的追蹤" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="harishkragarwal" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/27/2015"
   ms.author="hariag"/>


# 追蹤 B2B 訊息

## B2B 追蹤資訊
B2B 通訊牽涉到交易夥伴之間的訊息處理。關聯性定義為兩個交易夥伴之間的協議。建立通訊後，必須以適當方式監視通訊是否如預期般執行。在將 B2B API 應用程式融入為 Azure 應用程式服務的一部分時，我們已啟用追蹤資料，並將其提供在 Azure 入口網站上。

## AS2
在您建立 AS2 API 應用程式的執行個體後，您可以瀏覽至該執行個體，然後移至 [追蹤] 部件。在此處您將可檢視所有的 AS2 追蹤資訊，並可透過顯示的篩選分頁加以篩選。

![][1]

## EDIFACT
在您建立 EDIFACT API 應用程式的執行個體後，您可以瀏覽至該執行個體，然後移至 [追蹤] 部件。在此處您將可檢視所有的 EDIFACT 追蹤資訊，並可透過顯示的篩選器加以篩選。此外，在檢視畫面中，您也能夠檢視交換層級資料、群組層級資料和交易集層級資料。

如果批次已在相關聯的交易夥伴管理 API 應用程式中建立為 EDIFACT 協議的一部分，則 [批次] 部件會將這些批次全部列出。您可以進入批次中檢視構成作用中訊息 (如果有的話) 的訊息，以及過去已完成之批次的資訊。

![][2]

## X12
在您建立 X12 API 應用程式的執行個體後，您可以瀏覽至該執行個體，然後移至 [追蹤] 部件。在此處您將可檢視所有的 X12 追蹤資訊，並可透過顯示的篩選器加以篩選。此外，在檢視畫面中，您也能夠檢視交換層級資料、群組層級資料和交易集層級資料。

如果批次已在相關聯的交易夥伴管理 API 應用程式中建立為 X12 協議的一部分，則 [批次] 部件會將這些批次全部列出。您可以進入批次中檢視構成作用中訊息 (如果有的話) 的訊息，以及過去已完成之批次的資訊。

X12 和 EDIFACT 具有類似的追蹤檢視。

<!--Image references-->
[1]: ./media/app-service-logic-track-b2b-messages/AS2Tracking.jpg
[2]: ./media/app-service-logic-track-b2b-messages/EDIFACTTracking.jpg

<!---HONumber=62-->
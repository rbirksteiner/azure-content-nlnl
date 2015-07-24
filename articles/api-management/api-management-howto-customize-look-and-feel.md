<properties 
	pageTitle="如何在 Azure API 管理中自訂開發人員入口網站的外觀與風格" 
	description="如何在 Azure API 管理中自訂開發人員入口網站的外觀與風格。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# 如何在 Azure API 管理中自訂開發人員入口網站的外觀與風格

開發人員入口網站外觀與風格的色彩、字型、大小、間距及其他方面，都是由樣式規則定義。有幾組這些規則適用於頁面的每一個結構元素 - 頁首、功能表、內容本文、頁面標題等。在此作法中，您將了解如何修改樣式規則。

若要編輯樣式規則，請在發行者入口網站的 [**開發人員入口網站**] 功能表上按一下 [**外觀**]。然後按 [開始自訂] 來啟用樣式編輯器。

您的瀏覽器將在開發人員入口網站內切換為隱藏頁面，其中包含了內容範例，以及可在網站任意處使用之所有樣式規則的範例。若要開啟樣式編輯器，請將游標移至頁面最左側的細長灰色垂直線上。編輯器工具列應該顯示如下：

![Customization toolbar][api-management-customization-toolbar]

編輯樣式規則有兩種主要模式 - [**編輯所有規則**] 會顯示在任意處使用之所有樣式規則的清單，而 [**挑選元素**] 可讓您從所在頁面選取元素，並且僅顯示該特定元素的樣式。

在本節中，我們只想變更特定元素的樣式 (例如頁首)。從樣式編輯器工具列按一下 [Pick element] 選項，然後按一下 [Select an element to customize]。現在，當您將滑鼠移至元素上方，元素會變成反白顯示的狀態，以象徵在您按一下元素時呈現的元素樣式。

將滑鼠移至頁首中代表頁面標題的文字並按一下。一組已具名且分類的樣式規則將出現在樣式編輯器中。

每個規則均代表所選取元素的樣式屬性。例如，針對以上選取的頁首文字，文字的大小在 @font-size-h1，而替代字型的名稱則在 @headings-font-family。

> 如果您熟悉[啟動程序](http://getbootstrap.com/)，事實上，開發人員入口網站所使用啟動程序佈景主題內的這些規則[變數較少](http://getbootstrap.com/css/)。

現在，讓我們變更標題文字的色彩！ 選取 [**@headings-color**] 欄位中的項目，然後輸入 #000000。這是黑色的十六進位碼。執行這項作業，您會看見文字方塊末端出現方形色彩指示器。如果您按一下此指示器，將出現色彩選擇器，供您選擇色彩。

![Color picker][api-management-customization-toolbar-color-picker]

對所選取元素的樣式完成變更時，請按一下 [預覽變更]，在畫面上查看結果。目前只有管理員可看見這些結果。若要讓每個人都可看見這些變更，請在樣式編輯器中按一下 [發佈] 按鈕並確認變更。

![Publish form][api-management-customization-toolbar-publish-form]

> 若要變更套用至頁面上任何其他元素的樣式規則，請遵循您對頁首所進行的相同程序 - 從樣式編輯器按一下 [Pick an element]，選取您感興趣的元素，並開始修改畫面上顯示之樣式規則的值。


[Next steps]: #next-steps

[Management Portal]: https://manage.windowsazure.com/

[api-management-customization-toolbar]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png

<!---HONumber=62-->
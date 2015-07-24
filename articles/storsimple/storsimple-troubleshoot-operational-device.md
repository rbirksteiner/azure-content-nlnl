<properties 
   pageTitle="可運作的 StorSimple 裝置疑難排解"
   description="說明如何診斷和修正在可運作的 StorSimple 裝置上所發生的錯誤。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/09/2015"
   ms.author="v-sharos" />

# 可運作的 StorSimple 裝置疑難排解

## 概觀

本文提供實用的疑難排解指導方針，可用來解決在部署 StorSimple 裝置且可運作之後您可能遇到的問題。文中將說明常見問題、可能原因和建議的步驟，可協助您解決在執行 StorSimple 時可能遇到的問題。此資訊適用於 StorSimple 內部部署實體裝置和 StorSimple 虛擬裝置。

## 適用於可運作裝置的安裝精靈程序

您可以使用安裝精靈 (Invoke-HcsSetupWizard) 來檢查裝置設定，並視需要採取更正動作。

當您在先前已設定且可運作的裝置上執行安裝精靈時，程序流程是不一樣的。您只能變更下列項目：

- IP 位址、子網路遮罩及閘道
- 主要 DNS 伺服器
- 主要 NTP 伺服器
- 選用的 Web Proxy 設定

安裝精靈不會執行與密碼收集和裝置註冊相關的操作。

## 在安裝精靈後續執行期間發生的錯誤

下表說明當您在可運作的裝置上執行安裝精靈時可能遇到的錯誤、產生錯誤的可能原因，以及建議用來解決這些錯誤的動作。

| 編號 | 錯誤訊息或情況 | 可能的原因 | 建議的動作 |
| --- | -------------------------- | --------------- | ------------------ |
| 1 | 錯誤 350032：此裝置已經停用。 | 如果您在已停用的裝置上執行安裝精靈，就會看到這個錯誤。 | [連絡 Microsoft 支援服務](https://msdn.microsoft.com/library/azure/dn757750.aspx)以進行後續步驟。已停用的裝置無法處於執行狀態。您可能需要進行原廠重設，裝置才能再次啟用。 |
| 2 | Invoke-HcsSetupWizard：ERROR_INVALID_FUNCTION (發生例外狀況於 HRESULT：0x80070001) | DNS 伺服器更新失敗。DNS 設定是全域設定，並會在所有已啟用的網路介面上加以套用。 | 啟用介面並重新套用 DNS 設定。因為這些設定是全域的，所以這樣做可能會中斷其他已啟用介面的網路。 |
| 3 | 裝置會在 StorSimple Manager 服務入口網站中顯示為線上，但是當您嘗試完成最小安裝並儲存設定時，操作失敗。 | 在初始安裝期間，並未設定 Web Proxy，即使已有實際的 Proxy 伺服器存在也一樣。 | 使用 [Test-HcsmConnection](https://msdn.microsoft.com/library/azure/eedae62d-0957-4005-b346-9248724f90e0#sec05) Cmdlet 來尋找錯誤。如果您無法更正此問題，請[連絡 Microsoft 支援服務](https://msdn.microsoft.com/library/azure/dn757750.aspx)。 |
| 4 | Invoke-HcsSetupWizard：值未落在預期的範圍內。 | 這個錯誤是因為不正確的子網路遮罩而引發。可能的原因如下：<ul><li>子網路遮罩遺失或空白。</li><li>Ipv6 首碼格式不正確。</li><li>介面具備雲端功能，但閘道遺失或不正確。</li></ul>請注意，如果 DATA 0 是透過安裝精靈設定的，即會自動具備雲端功能。 | 若要判斷問題所在，請使用子網路 0.0.0.0 或 256.256.256.256，然後查看輸出。視需要針對子網路遮罩、閘道及 Ipv6 首碼輸入正確的值。 |
 
## 後續步驟
如果您無法解決問題，請[連絡 Microsoft 支援服務](https://msdn.microsoft.com/library/azure/dn757750.aspx)，尋求協助。

<!---HONumber=July15_HO2-->
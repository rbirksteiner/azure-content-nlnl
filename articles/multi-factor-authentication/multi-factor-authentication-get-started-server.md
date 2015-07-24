<properties 
	pageTitle="開始使用 Azure Multi-Factor Authentication Server" 
	description="這是說明如何開始使用 Azure MFA Server 的 Azure Multi-Factor Authentication 頁面。" 
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
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# 開始使用 Azure Multi-Factor Authentication Server




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

既然我們已經決定是否要使用內部部署 Multi-Factor Authentication，那麼我們就開始著手進行吧。本頁面探討新伺服器安裝，以及透過內部部署 Active Directory 予以設定。如果您已安裝 Phonefactor Server，並且正在尋找達成目標的方法，請參閱＜升級為 Azure Mult-Factor Authentication Server＞；如果您正在尋找只安裝 Web 服務的相關資訊，請參閱＜部署 Azure Multi-Factor Authentication Server Mobile App Web 服務＞。



## 下載 Azure Multi-Factor Authentication Server

下載 Azure Multi-Factor Authentication Server 的方法有兩種。第一個方法是登入 Azure 入口網站，第二個方法是直接從 [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net) 下載。


### 從 Azure 入口網站下載 Azure Multi-Factor Authentication Server
--------------------------------------------------------------------------------

1. 以系統管理員身分登入 Azure 入口網站。
2. 在左側選取 [Active Directory]。
3. 在 [Active Directory] 頁面頂端，按一下 [**Multi-Factor Auth Provider**]
4. 在底部按一下 [**管理**]
5. 按一下 [**下載**]
6. 在 [**產生啟用認證**] 上方，按一下 [**下載**]
7. 儲存下載內容。

### 直接下載 Azure Multi-Factor Authentication Server
--------------------------------------------------------------------------------

1. 登入 [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net)。
2. 按一下 [**下載**]
<center>![Cloud](./media/multi-factor-authentication-get-started-server/download2.png)</center>
3. 在 [**產生啟用認證**] 上方，按一下 [**下載**] - 請保留該頁面，因為稍後還會回來這裡。
4. 儲存下載內容。



## 安裝及設定 Azure Multi-Factor Authentication Server
既然您已下載伺服器，現在可以進行安裝和設定。請確認要安裝的伺服器符合下列需求：



Azure Multi-Factor Authentication Server 需求|說明|
:------------- | :------------- | 
硬體|<li>200 MB 的硬碟空間</li><li>x32 或 x64 處理器</li><li>1 GB 或更高的 RAM</li>
軟體|<li>Windows Server 2003 或更新版本 (如果主機是伺服器作業系統)</li><li>Windows Vista 或更新版本 (如果主機是用戶端作業系統)</li><li>Microsoft.NET 2.0 Framework</li><li>IIS 6.0 或更新版本 (如果要安裝使用者入口網站或 Web 服務 SDK)</li>

### Azure Multi-Factor Authentication Server 防火牆需求
--------------------------------------------------------------------------------
每部 MFA Server 都必須要能夠透過連接埠 443 與以下位址進行輸出通訊：

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

如果輸出防火牆會限制連接埠 443，您必須開啟以下 IP 位址範圍：

IP 子網路|網路遮罩|IP 範圍
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

如果您不使用 Azure Multi-Factor Authentication 事件確認功能，而且使用者不會在公司網路中利用裝置上的 Multi-Factor Auth 行動應用程式進行驗證，可以將 IP 範圍縮小為如下所示：


IP 子網路|網路遮罩|IP 範圍
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### 安裝及設定 Azure Multi-Factor Authentication Server
--------------------------------------------------------------------------------


1. 按兩下可執行檔。安裝作業隨即會開始。
2. 在 [選取安裝資料夾] 畫面中，請確認資料夾正確，然後按 [下一步]。
3. 當安裝完成時，請按一下 [完成]。這會啟動組態精靈。
4. 在組態精靈歡迎畫面上，勾選 [**略過使用驗證組態精靈**]，然後按 [**下一步**]。這會關閉精靈並啟動伺服器。
<center>![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)</center>

5. 回到下載伺服器的頁面，按一下 [**產生啟用認證**] 按鈕。將此資訊複製到 Azure MFA Server 提供的方塊中，然後按一下 [**啟用**]。


上述步驟說明使用組態精靈進行快速安裝。您可以從伺服器的 [工具] 功能表選取驗證精靈，以便重新執行。



##從 Active Directory 匯入使用者

既然您已安裝及設定伺服器，現在快速地將使用者匯入 Azure MFA Server。

### 從 Active Directory 匯入使用者
--------------------------------------------------------------------------------


1. 在 Azure MFA Server 的左側選取 [**使用者**]。
2. 在底部選取 [**從 Active Directory 匯入**]。
3. 現在您可以搜尋個別使用者，或在 AD 中搜尋含使用者的 OU。在此情況下，我們會指定使用者的 OU。
4. 在右側反白顯示所有使用者，然後按一下 [**匯入**]。您應該會看到指出成功完成作業的快顯視窗。關閉匯入視窗。

<center>![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)</center>

<!---HONumber=July15_HO2-->
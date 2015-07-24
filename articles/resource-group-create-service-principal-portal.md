<properties
   pageTitle="使用 Azure 入口網站建立新的 Azure 服務主體"
   description="描述如何建立新的 Azure 服務主體，而 Azure 服務主體可以與 Azure 資源管理員中的角色存取控制搭配使用來管理資源存取權。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz"/>

# 使用 Azure 入口網站建立新的 Azure 服務主體

## 概觀
服務主體是需要存取其他資源的自動化程序、應用程式或服務。使用 Azure 資源管理員，您可以授與服務主體的存取權並驗證服務主體，以讓它對存在於訂用帳戶或做為租用戶的資源執行允許的管理動作。

本主題顯示如何使用 Azure 入口網站建立新的服務主體。目前，您必須使用 Microsoft Azure 入口網站來建立新的服務主體。在更新的版本中，會將這項功能加入至 Azure 預覽入口網站。

## 概念
1. Azure Active Directory (AAD) - 雲端的身分識別與存取管理服務組建。如需詳細資料，請參閱：[什麼是 Azure Active Directory](./active-directory-whatis/)。
2. 服務主體 - 目錄中應用程式的執行個體。
3. AD 應用程式 - AAD 中向 AAD 識別應用程式的目錄記錄。如需詳細資料，請參閱 [Azure AD 中的驗證基本概念](https://msdn.microsoft.com/library/azure/874839d9-6de6-43aa-9a5c-613b0c93247e#BKMK_Auth)。


## 建立 Active Directory 應用程式
1. 透過[傳統入口網站](https://manage.windowsazure.com/)登入 Azure 帳戶。

2. 從左窗格中，選取 [**Active Directory**]。

   ![選取 Active Directory][1]

3. 選取您想要用來建立新應用程式的目錄。

   ![選擇目錄][2]

3. 若要檢視目錄中的應用程式，請按一下 [**應用程式**]。

   ![檢視應用程式][11]

4. 如果您之前尚未在該目錄中建立應用程式，則應該會看到與下面類似的映像。按一下 [**新增應用程式**]。

   ![新增應用程式][6]

   或者，按一下下方窗格中的 [**新增**]。

   ![新增][12]

5. 選取您想要建立的應用程式類型。在本教學課程中，我們不會使用組件庫中的應用程式。

   ![新的應用程式][10]

6. 填寫應用程式名稱，然後選取您想要使用的應用程式類型。因為我們想要搭配使用這個應用程式的服務主體與 Azure 資源管理員來進行驗證，所以將選擇建立 **WEB 應用程式和 (或) WEB API**，然後按 [下一步] 按鈕。

   ![名稱應用程式][9]

7. 填寫您應用程式的屬性。針對 [**登入 URL**]，提供描述您應用程式之網站的 URI。不會驗證網站是否存在。針對 [**應用程式識別碼 URI**]，提供識別您應用程式的 URI。不會驗證端點的唯一性或其是否存在。按一下 [**完成**] 建立 AAD 應用程式。

   ![應用程式屬性][4]

## 建立服務主體密碼
入口網站現在應該已選取您的應用程式。

1. 按一下 [**設定**] 索引標籤設定您應用程式的密碼。

   ![設定應用程式][3]

2. 向下捲動至 [**金鑰**] 區段，並選取您想要的密碼有效時間。

   ![金鑰][7]

3. 選取 [**儲存**] 建立金鑰。

   ![儲存][13]

   即會顯示儲存的金鑰，而且您可以進行複製。

   ![儲存的金鑰][8]

4. 您現在可以使用金鑰來驗證為服務主體。除了 [**金鑰**] 之外，您還需要 [**用戶端識別碼**] 才能登入。移至 [**用戶端識別碼**] 並複製它。
  
   ![用戶端識別碼][5]


您的應用程式現在已就緒，並且已在租用戶上建立服務主體。以服務主體身分登入時，請務必使用：

* **用戶端識別碼** - 做為使用者名稱。
* **金鑰** - 做為密碼。

## 後續步驟
開始使用

- [Azure 資源管理員概觀](./resource-group-overview.md)  
- [搭配使用 Azure PowerShell 與 Azure 資源管理員](./powershell-azure-resource-manager.md)
- [搭配使用適用於 Mac、Linux 和 Windows 的 Azure CLI 與 Azure 資源管理](virtual-machines/xplat-cli-azure-resource-manager.md)  
- [使用 Azure 入口網站管理 Azure 資源](azure-portal/resource-group-portal.md)  
  
建立和部署應用程式
  
- [編寫 Azure 資源管理員範本](./resource-group-authoring-templates.md)  
- [使用 Azure 資源管理員範本部署應用程式](azure-portal/resource-group-template-deploy.md)  
- [Azure 中的資源群組部署疑難排解](virtual-machines/resource-group-deploy-debug.md)  
- [Azure 資源管理員範本函數](./resource-group-template-functions.md)  
- [進階範本作業](./resource-group-advanced-template.md)  
- [使用 .NET 程式庫和範本部署 Azure 資源](virtual-machines/arm-template-deployment.md)
  
組織資源
  
- [使用標記來組織您的 Azure 資源](./resource-group-using-tags.md)  
  
管理和稽核存取權
  
- [管理和稽核資源存取權](azure-portal/resource-group-rbac.md)  
- [使用 Azure 資源管理員驗證服務主體](./resource-group-authenticate-service-principal.md)  

<!-- Images. -->
[1]: ./media/resource-group-create-service-principal-portal/active-directory.png
[2]: ./media/resource-group-create-service-principal-portal/active-directory-details.png
[3]: ./media/resource-group-create-service-principal-portal/application-configure.png
[4]: ./media/resource-group-create-service-principal-portal/app-properties.png
[5]: ./media/resource-group-create-service-principal-portal/client-id.png
[6]: ./media/resource-group-create-service-principal-portal/create-application.png
[7]: ./media/resource-group-create-service-principal-portal/create-key.png
[8]: ./media/resource-group-create-service-principal-portal/save-key.png
[9]: ./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png
[10]: ./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png
[11]: ./media/resource-group-create-service-principal-portal/view-applications.png
[12]: ./media/resource-group-create-service-principal-portal/add-icon.png
[13]: ./media/resource-group-create-service-principal-portal/save-icon.png

<!---HONumber=July15_HO1-->
<properties 
	pageTitle="了解如何安全存取 DocumentDB 中的資料 | Azure" 
	description="深入了解 DocumentDB 中的存取控制概念，其中包括主要金鑰、唯讀金鑰、使用者和權限。" 
	services="documentdb" 
	authors="stephbaron" 
	manager="johnmac" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="stbaro"/>

# 安全存取 DocumentDB 資料 #

本文提供儲存於 [Microsoft Azure DocumentDB](../../services/documentdb/) 中資料的安全存取概觀。 

閱讀本概觀後，您將能夠回答下列問題：  

-	什麼是 DocumentDB 主要金鑰？
-	什麼是 DocumentDB 唯讀金鑰？
-	什麼是 DocumentDB 資源權杖？
-	如何使用 DocumentDB 使用者和權限來安全存取 DocumentDB 資料？

##<a id="Sub1"></a>DocumentDB 存取控制概念##

為控制 DocumentDB 資源的存取，DocumentDB 提供一流的概念。在本主題中，DocumentDB 資源分為兩種類別：

- 管理資源
	- 帳戶
	- 資料庫
	- 使用者
	- 權限
- 應用程式資源
	- 集合
	- 文件
	- 附件
	- 預存程序
	- 觸發程序
	- 使用者定義函數

在這兩種類別的內容中，DocumentDB 支援三種類型的存取控制角色：帳戶管理員、唯讀管理員和資料庫使用者。每個存取控制角色的權限如下：
 
- 帳戶管理員：給定 DocumentDB 帳戶內所有資源 (系統管理和應用程式) 的完整存取權。
- 唯讀系統管理員：給定 DocumentDB 帳戶內所有資源 (系統管理和應用程式) 的唯讀存取權。 
- 資料庫使用者：與一組特定 DocumentDB 資料庫資源 (例如集合、文件、指令碼) 相關聯的 DocumentDB 使用者資源。多個使用者資源可以與一個給定資料庫相關聯，而每個使用者資源可以有與其相關聯的多個權限。

請謹記上述的類別和資源，DocumentDB 存取控制模型可定義三種類型的存取建構：

- 主要金鑰：建立 DocumentDB 帳戶時，系統會建立兩個主要金鑰 (主要和次要)。這些金鑰可提供 DocumentDB 帳戶內所有資源的完整系統管理存取權。

![DocumentDB master keys illustration](./media/documentdb-secure-access-to-data/masterkeys.png)

- 唯讀金鑰：建立 DocumentDB 帳戶時，系統會建立兩個唯讀金鑰 (主要和次要)。這些金鑰可提供 DocumentDB 帳戶內所有資源的唯讀存取權。

![DocumentDB read-only keys illustration](./media/documentdb-secure-access-to-data/readonlykeys.png)

- 資源權杖：資源權杖會與 DocumentDB 權限資源相關聯，並擷取資料庫使用者與使用者所具備之特定 DocumentDB 應用程式資源 (例如集合、文件) 權限之間的關係。

![DocumentDB resource tokens illustration](./media/documentdb-secure-access-to-data/resourcekeys.png)

##<a id="Sub2"></a>使用 DocumentDB 主要和唯讀金鑰 ##
前面提過，DocumentDB 主要金鑰可提供 DocumentDB 帳戶內所有資源的完整系統管理存取權，而唯讀金鑰可提供帳戶內所有資源的讀取權限。下列程式碼片段說明如何使用 DocumentDB 帳戶端點和主要金鑰來具現化 DocumentClient，並建立新的資料庫。 

    //Read the DocumentDB endpointUrl and authorization keys from config.
    //These values are available from the Azure Management Portal on the DocumentDB Account Blade under "Keys".
    //NB > Keep these values in a safe and secure location. Together they provide Administrative access to your DocDB account.
    
	private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
    private static readonly SecureString authorizationKey = ToSecureString(ConfigurationManager.AppSettings["AuthorizationKey"]);
        
    client = new DocumentClient(new Uri(endpointUrl), authorizationKey);
    
	//Create Database
    Database database = await client.CreateDatabaseAsync(
        new Database
        {
            Id = databaseName
        });


##<a id="Sub3"></a>DocumentDB 資源權杖的概觀 ##
若要對無法託付主要金鑰的用戶端提供 DocumentDB 帳戶內資源的存取權，您可以使用資源權杖 (方法是建立 DocumentDB 使用者和權限)。您的 DocumentDB 主要金鑰包括主要和次要金鑰，兩者皆可授與帳戶及帳戶內所有資源的系統管理存取權。提供任一主要金鑰都會讓您的帳戶受到惡意或粗心使用的可能性。 

同樣地，DocumentDB 唯讀金鑰可提供 DocumentDB 帳戶內所有資源的讀取權限 (當然權限資源除外)，但無法用來提供特定 DocumentDB 資源的更細微存取權 。

DocumentDB 資源權杖提供一個安全的替代方式，無需主要或唯讀金鑰便可讓用戶端根據他們被授與的權限，來讀取、寫入及刪除 DocumentDB 帳戶中的資源。

以下是典型的設計模式，其中資源權杖可能會被要求、產生和傳遞給用戶端：

1. 設定中間層服務，以協助行動應用程式分享使用者相片。
2. 中間層服務擁有 DocumentDB 帳戶的主要金鑰。
3. 使用者的行動裝置上會安裝相片應用程式。 
4. 登入時，相片應用程式會建立中間層服務的使用者身分識別。這項身分識別建立的機制完全取決於應用程式。
5. 建立身分識別後，中間層服務會根據身分識別要求權限。
6. 中間層服務會將資源權杖送回電話應用程式。
7. 電話應用程式可以繼續使用資源權杖，利用資源權杖所定義的權限並在資源權杖所允許的間隔內，直接存取 DocumentDB 資源。 
8. 資源權杖過期時，後續要求將會收到 401 未經授權的例外狀況。此時，電話應用程式會重新建立身分識別，並要求新的資源權杖。

![DocumentDB resource tokens workflow](./media/documentdb-secure-access-to-data/resourcekeyworkflow.png)

##<a id="Sub4"></a>使用 DocumentDB 使用者和權限 ##
DocumentDB 使用者資源會與 DocumentDB 資料庫相關聯。每個資料庫可能包含零個或多個 DocumentDB 使用者。下列程式碼片段示範如何建立 DocumentDB 使用者資源。

	//Create a user.
    User docUser = new User
    {
        Id = "mobileuser"
    };

    docUser = await client.CreateUserAsync(database.SelfLink, docUser);

> [AZURE.NOTE] 每個 DocumentDB 使用者都具有 PermissionsLink 屬性，可用來擷取與使用者相關聯的權限清單。

DocumentDB 權限資源會與 DocumentDB 使用者相關聯。每位使用者可能包含零個或多個 DocumentDB 權限。當使用者嘗試存取特定的應用程式資源時，權限資源會提供使用者所需的安全性權杖存取權。
權限資源可能會提供兩種可用的存取層級：

- 所有：使用者具有資源的完整權限
- 讀取：使用者只能讀取資源的內容，但無法執行資源的寫入、更新或刪除作業。


> [AZURE.NOTE] 為執行 DocumentDB 預存程序，使用者必須具備即將執行預存程序之集合的「所有」權限。


下列程式碼片段示範如何建立權限資源，讀取權限資源的資源權杖 (token)，並將權限與先前所建立的使用者產生關聯。

	//Create a permission.

    Permission docPermission = new Permission
    {
        PermissionMode = PermissionMode.Read,
        ResourceLink = documentCollection.SelfLink,
        Id = "readperm"
    };
            
	docPermission = await client.CreatePermissionAsync(docUser.SelfLink, docPermission);
	Console.WriteLine(docPermission.Id + " has token of: " + docPermission.Token);

若要輕鬆地取得所有與特定使用者相關聯的權限資源，DocumentDB 會為每個使用者物件提供權限摘要。下列程式碼片段示範如何擷取與先前所建立的使用者相關聯的權限、建構權限清單，並代表使用者具現化新的 DocumentClient。

	//Read a permission feed.
    FeedResponse<Permission> permFeed = await client.ReadPermissionFeedAsync(docUser.SelfLink);
	
	List<Permission> permList = new List<Permission>();
    
	foreach (Permission perm in permFeed)
    {
        permList.Add(perm);
    }
            
    DocumentClient userClient = new DocumentClient(new Uri(endpointUrl),permList);

> [AZURE.TIP] 資源權杖有 1 小時的預設有效時間範圍。不過，您可以明確指定權杖存留期，最多 5 個小時。

##<a name="NextSteps"></a>後續步驟

- 若要深入了解 DocumentDB，請按一下[這裡](http://azure.com/docdb)。
- 若要了解如何管理主要和唯讀金鑰，請按一下 [這裡](documentdb-manage-account.md)。
- 若要了解如何建構 DocumentDB 授權權杖，請按一下 [這裡](https://msdn.microsoft.com/library/azure/dn783368.aspx)

<!--HONumber=49--> 
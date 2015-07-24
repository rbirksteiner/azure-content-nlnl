<properties 
	pageTitle="使用 Azure SDK for Java 在 Azure App Service 中建立 Web 應用程式" 
	description="了解如何使用 Azure SDK for Java 在 Azure App Service 中以程式設計方式建立 Web 應用程式。" 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter="Java" 
	authors="donntrenton" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="v-donntr"/>


# 使用 Azure SDK for Java 在 Azure App Service 中建立 Web 應用程式

<!-- Azure Active Directory workflow is not yet available on the Azure Portal -->

## 概觀

本逐步解說說明如何建立 Azure SDK for Java 應用程式，以便在 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 中建立 Web 應用程式，並將應用程式部署到其上。 其中包括兩個部分：

- 第 1 部分示範如何建立 Java 應用程式，以便建立 Web 應用程式。
- 第 2 部分示範如何建立簡單 JSP "Hello World" 應用程式，然後使用 FTP 用戶端將程式碼部署至 App Service。


## 必要條件

### 軟體安裝

本文中的 AzureWebDemo 應用程式程式碼是使用 Azure Java SDK 0.7.0 撰寫，您可使用 [Web Platform Installer (WebPI)](http://go.microsoft.com/fwlink/?LinkID=252838) 進行安裝。此外，務必使用最新版的 [Azure Toolkit for Eclipse](https://msdn.microsoft.com/library/azure/hh690946.aspx)。安裝 SDK 之後，在 **Maven 儲存機制**中執行**更新索引**以在 Eclipse 專案中更新相依性，然後在 [**相依性**] 視窗中重新新增各封裝的最新版本。按一下 **[說明] > [安裝詳細資料]**，可以驗證 Eclipse 中安裝的軟體版本；您至少要有下列版本：

- Package for Microsoft Azure Libraries for Java 0.7.0.20150309
- Eclipse IDE for Java EE Developers 4.4.2.20150219


### 在 Azure 中建立和設定雲端資源

開始此程序前，Azure 上必須有使用中 Azure 訂用帳戶並設定預設 Active Directory (AD)。


### 在 Azure 中建立 Active Directory (AD)

如果您的 Azure 訂用帳戶上還沒有 Active Directory (AD)，請使用您的 Microsoft 帳戶登入 [Azure 傳統入口網站](https://manage.windowsazure.com)。如果您有多個訂用帳戶，請按一下 [**訂用帳戶**] 並針對您要用於此專案的訂用帳戶選取預設目錄。接著按一下 [**套用**] 切換至該訂用帳戶檢視。

1. 從左側功能表中選取 [**Active Directory**]。按一下 **[新增] > [目錄] > [自訂建立]**。

2. 在 [**新增目錄**] 中，選取 [**建立新目錄**]。

3. 在 [**名稱**] 中，輸入目錄名稱。

4. 在 [**網域**] 中，輸入網域名稱。這是您的目錄預設包含的基本網域名稱；其格式為 `<domain_name>.onmicrosoft.com`您可以根據此目錄名稱或您擁有的其他網域名稱予以命名。之後，您可以新增貴組織已使用的其他網域名稱。

5. 在 [**國家或地區**] 中，選取您的地區設定。

如需 AD 詳細資訊，請參閱[什麼是 Azure Active Directory？](http://technet.microsoft.com/library/jj573650.aspx)。


### 建立 Azure 的管理憑證

Azure SDK for Java 使用管理憑證來向 Azure 訂用帳戶進行驗證。這些是用來驗證下列用戶端應用程式的 X.509 v3 憑證：利用服務管理 API 代表訂用帳戶擁有者管理訂用資源的用戶端應用程式。

此程序中的程式碼使用自我簽署的憑證來向 Azure 進行驗證。在此程序中，您需要事先建立憑證並將其上傳至 [Azure 傳統入口網站](https://manage.windowsazure.com)。請執行下列步驟：

- 產生代表您的用戶端憑證的 PFX 檔案，並將其儲存於本機。
- 從 PFX 檔案產生管理憑證 (CER 檔案)。
- 將 CER 檔案上傳至您的 Azure 訂用帳戶。
- 將 PFX 檔案轉換為 JKS，因為 Java 使用該格式來驗證憑證的使用。
- 撰寫應用程式的驗證碼，以便參照本機 JKS 檔案。

當您完成這個程序時，CER 憑證會位於 Azure 訂用帳戶，而 JKS 憑證會位於本機磁碟機。如需管理憑證的詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](http://msdn.microsoft.com/library/azure/gg551722.aspx)。


#### 建立憑證

若要建立自己的自我簽署憑證，請開啟作業系統上的命令主控台並執行下列命令。

> **注意：**您用來執行此命令的電腦必須已安裝 JDK。此外，keytool 的路徑取決於您安裝 JDK 的位置。如需詳細資訊，請參閱 Java 線上文件中的[金鑰和憑證管理工具 (keytool)](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html)。

若要建立 .pfx 檔案：

    <java-install-dir>/bin/keytool -genkey -alias <keystore-id>
     -keystore <cert-store-dir>/<cert-file-name>.pfx -storepass <password>
     -validity 3650 -keyalg RSA -keysize 2048 -storetype pkcs12
     -dname "CN=Self Signed Certificate 20141118170652"

若要建立 .cer 檔案：

    <java-install-dir>/bin/keytool -export -alias <keystore-id>
     -storetype pkcs12 -keystore <cert-store-dir>/<cert-file-name>.pfx
     -storepass <password> -rfc -file <cert-store-dir>/<cert-file-name>.cer

其中：

- `<java-install-dir>` 是您安裝 Java 的目錄路徑。
- `<keystore-id>` 是keystore 項目識別碼 (例如 `AzureRemoteAccess`)。
- `<cert-store-dir>` 是您儲存憑證的目錄路徑 (例如 `C:/Certificates`)。
- `<cert-file-name>` 是憑證檔案的名稱 (例如 `AzureWebDemoCert`)。
- `<password>` 是您選擇用來保護憑證的密碼；長度必須至少 6 個字元。您可以不輸入密碼，但不建議這麼做。
- `<dname>` 是要與別名相關聯的 X.500 辨別名稱，並作為自我簽署憑證中的簽發者和主旨欄位。

如需詳細資訊，請參閱[建立和上傳 Azure 的管理憑證](http://msdn.microsoft.com/library/azure/gg551722.aspx)。


#### 上傳憑證

若要將自我簽署憑證上傳至 Azure，請移至傳統入口網站中的 [**設定**] 頁面，然後按一下 [**管理憑證**] 索引標籤。按一下頁面底部的 [**上傳**] 並導覽至您建立之 CER 檔案的位置。


#### 將 PFX 檔案轉換為 JKS

在 Windows 命令提示字元 (以系統管理員身分執行) 中，將目錄變更至包含憑證的目錄並執行下列命令，其中 `<java-install-dir>` 是電腦上安裝 Java 的目錄：

    <java-install-dir>/bin/keytool.exe -importkeystore
     -srckeystore <cert-store-dir>/<cert-file-name>.pfx
     -destkeystore <cert-store-dir>/<cert-file-name>.jks
     -srcstoretype pkcs12 -deststoretype JKS

1. 出現提示後，請輸入目的地 keystore 密碼；這會是 JKS 檔案的密碼。

2. 出現提示後，請輸入來源 keystore 密碼；這是您為 PFX 檔案指定的密碼。

兩組密碼不必相同。您可以不輸入密碼，但不建議這麼做。


## 建置 Web 應用程式建立應用程式

### 建立 Eclipse 工作區和 Maven 專案

在這一節中，您可為 Web 應用程式建立應用程式 (名稱為 AzureWebDemo) 建立工作區和 Maven 專案。

1. 建立新的 Maven 專案。按一下 **[檔案] > [新增] > [Maven 專案]**。在 [**新增 Maven 專案**] 中，選取 [**建立簡單專案**] 和 [**使用預設工作區位置**]。

2. 在 [**新增 Maven 專案**] 的第二頁上，指定下列各項：

    - 群組識別碼：`com.<username>.azure.webdemo`
    - 成品識別碼：AzureWebDemo
    - 版本：0.0.1-SNAPSHOT
    - 封裝：jar
    - 名稱：AzureWebDemo

    按一下 [完成]。

3. 在 [專案總管] 中開啟新專案的 pom.xml 檔案。選取 [**相依性**] 索引標籤。由於這是新專案，所以尚未列出任何封裝。

4. 開啟 [Maven 儲存機制] 檢視。按一下 **[視窗] > [顯示檢視] > [其他] > [Maven] > [Maven 儲存機制]**，然後按一下 [**確定**]。[**Maven 儲存機制**] 檢視將顯示在 IDE 底部。

5. 開啟 [**全域儲存機制**]，以滑鼠右鍵按一下 **central** 儲存機制，然後選取 [**重建索引**]。

    ![][1]
    
    視您的連線速度而定，此步驟可能需要數分鐘的時間。重建索引時，您應可在 **central** Maven 儲存機制中看見 Microsoft Azure 封裝。

6. 在 [**相依性**] 中，按一下 [**新增**]。在 [**輸入群組識別碼...**] 中輸入 `azure-management`。選取用於基礎管理和 App Service Web Apps 管理的封裝：

        com.microsoft.azure  azure-management
        com.microsoft.azure  azure-management-websites

    > **注意：**如果在新版本發行後更新相依性，則需在這份清單中重新新增每個相依性。按一下 [**新增**] 並選取每個相依性後，每個相依性會在 [**相依性**] 清單中顯示新的版本號碼。

按一下 [確定]。Azure 封裝會接著顯示在 [**相依性**] 清單中。


### 呼叫 Azure SDK 以便撰寫 Java 程式碼來建立 Web 應用程式

接著，撰寫可在 Azure SDK for Java 中呼叫 API 的程式碼，以便建立 App Service Web 應用程式。

1. 建立 Java 類別以包含主要進入點程式碼。在 [專案總管] 中，以滑鼠右鍵按一下專案節點並選取 **[新增] > [類別]**。

2. 在 [**新增 Java 類別**] 中，將類別命名為 `WebCreator` 並勾選 [**public static void main**] 核取方塊。選取項目應如下所示：

    ![][2]

3. 按一下 [完成]。WebCreator.java 檔案會顯示在 [專案總管] 中。


### 呼叫 Azure API 以建立 App Service Web 應用程式


#### 新增必要匯入

在 WebCreator.java 中，新增下列匯入；這些匯入可供存取類別管理資料館中的類別，以便使用 Azure API：

    // General imports
    import java.net.URI;
    import java.util.ArrayList;
    
    // Imports for Exceptions
    import java.io.IOException;
    import java.net.URISyntaxException;
    import javax.xml.parsers.ParserConfigurationException;
    import com.microsoft.windowsazure.exception.ServiceException;
    import org.xml.sax.SAXException;
    
    // Imports for Azure App Service management configuration
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.management.configuration.ManagementConfiguration;
    
    // Service management imports for App Service Web Apps creation
    import com.microsoft.windowsazure.management.websites.*;
    import com.microsoft.windowsazure.management.websites.models.*;
    
    // Imports for authentication
    import com.microsoft.windowsazure.core.utils.KeyStoreType;


#### 定義主要進入點類別

因為 AzureWebDemo 應用程式的目的是要建立 App Service Web 應用程式，所以請將此應用程式的主要類別命名為 `WebAppCreator`此類別提供主要進入點程式碼，以便呼叫 Azure 服務管理 API 來建立 Web 應用程式。

新增 Web 應用程式和網路空間的下列參數定義。您需要提供自己的 Azure 訂用帳戶識別碼和憑證資訊。

    public class WebAppCreator {
    
        // Parameter definitions used for authentication.
        private static String uri = "https://management.core.windows.net/";
        private static String subscriptionId = "<subscription-id>";
        private static String keyStoreLocation = "<certificate-store-path>";
        private static String keyStorePassword = "<certificate-password>";
    
        // Define web app parameter values.
        private static String webAppName = "WebDemoWebApp";
        private static String domainName = ".azurewebsites.net";
        private static String webSpaceName = WebSpaceNames.WESTUSWEBSPACE;
        private static String appServicePlanName = "WebDemoAppServicePlan";

其中：

- `<subscription-id>` 是您要建立資源的 Azure 訂用帳戶識別碼。
- `<certificate-store-path>` 是本機憑證存放區目錄中 JKS 檔案的路徑和檔案名稱。例如，`C:/Certificates/CertificateName.jks` 適用於 Linux，而 `C:\Certificates\CertificateName.jks` 適用於 Windows。
- `<certificate-password>` 是在建立 JKS 憑證時指定的密碼。
- `webAppName` 可以您選擇的任何名稱；此程序使用 `WebDemoWebApp` 這個名稱。完整網域名稱為附加 `domainName` 的 `webAppName`，所以此例中的完整網域為 `webdemowebapp.azurewebsites.net`
- `domainName` 應如上所述加以指定。
- `webSpaceName` 應是在 [WebSpaceNames](http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/models/WebSpaceNames.html) 類別中指定的其中一個值。
- `appServicePlanName` 應如上所述加以指定。

> **注意：**每次執行此應用程式時，您必須先變更 `webAppName` 和 `appServicePlanName` 的值 (或在 Azure 入口網站上刪除 Web 應用程式)，才能再次執行此應用程式。否則，執行作業會因為 Azure 上已存在相同資源而失敗。


#### 定義 Web 建立方法

接著，定義用以建立 Web 應用程式的方法。此方法 (`createWebApp`) 會指定 Web 應用程式和網路空間的參數。也會建立及設定 App Service Web Apps 管理用戶端，而該管理用戶端是由 [WebSiteManagementClient](http://dl.windowsazure.com/javadoc/com/microsoft/windowsazure/management/websites/WebSiteManagementClient.html) 物件定義。此管理用戶端是建立 Web Apps 的關鍵。它可提供符合 REST 限制的 Web 服務，以便應用程式藉由呼叫服務管理 API 來管理 Web 應用程式 (執行作業，例如建立、更新和刪除)。

    private static void createWebApp() throws Exception {

        // Specify configuration settings for the App Service management client.
        Configuration config = ManagementConfiguration.configure(
            new URI(uri),
            subscriptionId,
            keyStoreLocation,  // Path to the JKS file
            keyStorePassword,  // Password for the JKS file
            KeyStoreType.jks   // Flag that you are using a JKS keystore
        );

        // Create the App Service Web Apps management client to call Azure APIs
        // and pass it the App Service management configuration object.
        WebSiteManagementClient webAppManagementClient = WebSiteManagementService.create(config);

        // Create an App Service plan for the web app with the specified parameters.
        WebHostingPlanCreateParameters appServicePlanParams = new WebHostingPlanCreateParameters();
        appServicePlanParams.setName(appServicePlanName);
        appServicePlanParams.setSKU(SkuOptions.Free);
        webAppManagementClient.getWebHostingPlansOperations().create(webSpaceName, appServicePlanParams);

        // Set webspace parameters.
        WebSiteCreateParameters.WebSpaceDetails webSpaceDetails = new WebSiteCreateParameters.WebSpaceDetails();
        webSpaceDetails.setGeoRegion(GeoRegionNames.WESTUS);
        webSpaceDetails.setPlan(WebSpacePlanNames.VIRTUALDEDICATEDPLAN);
        webSpaceDetails.setName(webSpaceName);

        // Set web app parameters.
        // Note that the server farm name takes the Azure App Service plan name.
        WebSiteCreateParameters webAppCreateParameters = new WebSiteCreateParameters();
        webAppCreateParameters.setName(webAppName);
        webAppCreateParameters.setServerFarm(appServicePlanName);
        webAppCreateParameters.setWebSpace(webSpaceDetails);

        // Set usage metrics attributes.
        WebSiteGetUsageMetricsResponse.UsageMetric usageMetric = new WebSiteGetUsageMetricsResponse.UsageMetric();
        usageMetric.setSiteMode(WebSiteMode.Basic);
        usageMetric.setComputeMode(WebSiteComputeMode.Shared);

        // Define the web app object.
        ArrayList<String> fullWebAppName = new ArrayList<String>();
        fullWebAppName.add(webAppName + domainName);
        WebSite webApp = new WebSite();
        webApp.setHostNames(fullWebAppName);

        // Create the web app.
        WebSiteCreateResponse webAppCreateResponse = webAppManagementClient.getWebSitesOperations().create(webSpaceName, webAppCreateParameters);

        // Output the HTTP status code of the response; 200 indicates the request succeeded; 4xx indicates failure.
        System.out.println("----------");
        System.out.println("Web app created - HTTP response " + webAppCreateResponse.getStatusCode() + "\n");

        // Output the name of the web app that this application created.
        String shinyNewWebAppName = webAppCreateResponse.getWebSite().getName();
        System.out.println("----------\n");
        System.out.println("Name of web app created: " + shinyNewWebAppName + "\n");
        System.out.println("----------\n");
    }

此程式碼將會輸出回應的 HTTP 狀態 (表示成功或失敗)，如果成功，將會輸出所建立 Web 應用程式的名稱。


#### 定義 main() 方法

提供 main() 方法程式碼，以便呼叫 createWebApp() 來建立 Web 應用程式。

最後，從 `main` 呼叫 `createWebApp`：

        public static void main(String[] args)
            throws IOException, URISyntaxException, ServiceException,
            ParserConfigurationException, SAXException, Exception {

            // Create web app
            createWebApp();

        }  // end of main()

    }  // end of WebAppCreator class


#### 執行應用程式並驗證 Web 應用程式建立

若要確認您的應用程式可執行，請按一下 **[執行] > [執行]**。當應用程式完成執行時，您應在 Eclipse 主控台中看見下列輸出：

    ----------
    Web app created - HTTP response 200
    
    ----------
    
    Name of web app created: WebDemoWebApp
    
    ----------

登入 Azure 傳統入口網站並按一下 [**Web Apps**]。幾分鐘內，新的 Web 應用程式應顯示在 Web Apps 清單中。


## 將應用程式部署至 Web 應用程式

執行 AzureWebDemo 並建立新的 Web 應用程式之後，登入傳統入口網站，按一下 **[Web Apps]**，然後選取 [**Web Apps**] 清單中的 [**WebDemoWebApp**]。在 Web 應用程式的儀表板頁面中，按一下 [**瀏覽**] (或按一下 URL：`webdemowebapp.azurewebsites.net`) 以導覽至該 Web 應用程式。您將會看見空白預留位置頁面，因為尚未將任何內容發佈至此 Web 應用程式。

接下來，您會建立 "Hello World" 應用程式並將其部署至 Web 應用程式。


### 建立 JSP Hello World 應用程式

#### 建立應用程式

為了示範如何將應用程式部署到 Web，下列程序顯示如何建立簡單的 "Hello World" Java 應用程式並將它更新至您的應用程式所建立的 App Service Web App。

1. 按一下 **[檔案] > [新增] > [動態 Web 專案]**。將它命名為 `JSPHello`您不需要變更此對話方塊中的任何其他設定。按一下 [完成]。

    ![][3]

2. 在 [專案總管] 中，展開 **JSPHello** 專案，以滑鼠右鍵按一下 **WebContent**，然後按一下 **[新增] > [JSP 檔案]**。在 [新增 JSP 檔案] 對話方塊中，將新檔案命名為 `index.jsp`。按 [下一步]。

3. 在 [Select JSP Template] 對話方塊中，選取 [New JSP File (html)]，然後按一下 [完成]。

4. 在 index.jsp 的 `<head>` 和 `<body>` 標記區段中新增下列程式碼：

        <head>
          ...
          java.util.Date date = new java.util.Date();
        </head>
    
        <body>
          Hello, the time is <%= date %> 
        </body>


#### 在 localhost 中執行 Hello World 應用程式

執行此應用程式前，您需設定一些屬性。

1. 在 **JSPHello** 專案上按一下滑鼠右鍵，然後選取 [**屬性**]。

2. 在 [**屬性**] 對話方塊中：選取 [**Java 組建路徑**]、選取 [**順序和匯出**] 索引標籤、核取 [**JRE 系統程式庫**]，然後按一下 [**向上**] 將它移至清單最上方。

    ![][4]

3. 此外，在 [**屬性**] 對話方塊中：選取 [**目標執行階段**] 並按一下 [**新增**]。

4. 在 [**新增伺服器執行階段環境**] 對話方塊中，選取伺服器 (例如 **Apache Tomcat v7.0**) 並按 [**下一步**]。在 [**Tomcat 伺服器**] 對話方塊中，將 [**名稱**] 設定為 `Apache Tomcat v7.0`，然後將 [**Tomcat 安裝目錄**] 設定為您安裝 Tomcat 伺服器的目錄。

    ![][5]

    按一下 [完成]。

5. 然後，返回 [**屬性**] 對話方塊的 [**目標執行階段**] 頁面。選取 [**Apache Tomcat v7.0**]，然後按一下 [**確定**]。

    ![][6]

6. 在 Eclipse 的 [**執行**] 功能表中，按一下 [**執行**]。在 [**執行身分**] 對話方塊中，選取 [**在伺服器上執行**]。在 [**在伺服器上執行**] 對話方塊中，選取 [**Tomcat v7.0 Server**]。

    ![][7]

    按一下 [完成]。

7. 當應用程式執行時，您應看見 [**JSPHello**] 頁面顯示於 Eclipse 的 localhost 視窗 (`http://localhost:8080/JSPHello/`)，顯示下列訊息：

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### 將應用程式匯出為 WAR

將 Web 專案檔案匯出為網頁封存 (WAR) 檔案，以便將它部署至 Web 應用程式。下列 Web 專案檔案位於 WebContent 資料夾中：

    META-INF
    WEB-INF
    index.jsp

1. 以滑鼠右鍵按一下 WebContent 資料夾並選取 [**匯出**]。

2. 在 [**匯出選取**] 對話方塊中，按一下 **[Web] > [WAR]** 檔案，然後按 [**下一步**]。

3. 在 [**WAR 匯出**] 對話方塊中，選取目前專案中的 src 目錄，然後在結尾包含 WAR 檔案名稱。例如：

    `<project-path>/JSPHello/src/JSPHello.war`

如需部署 WAR 檔案的詳細資訊，請參閱[將 Java 應用程式新增至 Azure App Service Web Apps](web-sites-java-add-app.md)。


### 使用 FTP 部署 Hello World 應用程式

選取協力廠商 FTP 用戶端，以發佈應用程式。此程序說明兩個選項：Azure 內建的 Kudu 主控台；以及 FileZilla (具有便利圖形 UI 的熱門工具)。

> **注意：** Azure Plugin for Eclipse with Java 2.4 支援部署至儲存體帳戶和雲端服務，但目前不支援部署至 Web 應用程式。如[在 Eclipse 中建立 Azure 的 Hello World 應用程式](http://msdn.microsoft.com/library/azure/hh690944.aspx)所述，您可以使用 Azure 部署專案部署至儲存體帳戶和雲端服務，但不能部署至 Web 應用程式。使用 FTP 或 GitHub 等方法將檔案移轉至您的 Web 應用程式。

> **注意：**不建議從 Windows 命令提示字元 (Windows 隨附的命令列 FTP.EXE 公用程式) 使用 FTP。採用使用中 FTP (例如 FTP.EXE) 的 FTP 用戶端通常無法透過防火牆作業。使用中 FTP 可指定 FTP 伺服器將可能無法連線的內部 LAN 位址。

如需使用 FTP 部署至 App Service Web 應用程式的詳細資訊，請參閱下列主題：

- [使用 Azure 入口網站管理 Web 應用程式：部署](web-sites-manage.md/#deployment)
- [使用 FTP 公用程式部署](web-sites-deploy.md)


#### [設定部署認證] 來設定

確定您已執行 **AzureWebDemo** 應用程式來建立 Web 應用程式。您會將檔案移轉到此位置。

1. 登入傳統入口網站並按一下 [**Web Apps**]。確定 **WebDemoWebApp** 顯示在 Web 應用程式清單中，並確定它正在執行中。按一下 **WebDemoWebApp** 以開啟其 [**儀表板**] 頁面。

2. 在 [**儀表板**] 頁面的 [**快速瀏覽**] 下，按一下 [**設定您的部署認證**] (如果您已有部署認證，則為 [**重設您的部署認證**])。

    部署認證與 Microsoft 帳戶相關聯。您需要指定使用者名稱和密碼，以便利用 Git 和 FTP 進行部署。您可以使用這些認證來部署至與您的 Microsoft 帳戶相關聯的所有 Azure 訂用帳戶中的任何 Web 應用程式。在對話方塊中提供 Git 和 FTP 部署，並記錄使用者名稱和密碼以供未來使用。


#### 取得 FTP 連線資訊

若要使用 FTP 將應用程式檔案部署至新建立的 Web 應用程式，您需要取得連線資訊。取得連線資訊的方法有兩種。造訪 Web 應用程式的 [**儀表板**] 頁面是一種方法；另一種方法則是下載 Web 應用程式的發行設定檔。發行設定檔是可提供下列資訊的 XML 檔案：Azure App Service 中 Web 應用程式的 FTP 主機名稱和登入認證。 您可以使用此使用者名稱和密碼來部署至與 Azure 帳戶相關聯的所有訂用帳戶中的任何 Web 應用程式 (不限於這一個)。

若要從 [Azure 入口網站](https://portal.azure.com)中 Web 應用程式的刀鋒視窗取得 FTP 連線資訊：

1. 在 [**基本功能**] 之下，尋找並複製 [**FTP 主機名稱**]。這是類似於 `ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net` 的 URI。

2. 在 [**基本功能**] 之下，尋找並複製 [**FTP/部署使用者名稱**]。其格式為 *webappname\deployment-username*；例如 `WebDemoWebApp\deployer77`

若要從發行設定檔取得 FTP 連線資訊：

1. 在 Web 應用程式的刀鋒視窗中，按一下 [**取得發行設定檔**]。這會將 .publishsettings 檔案下載到本機磁碟機。

2. 在 XML 編輯器或文字編輯器中開啟 .publishsettings 檔案，然後尋找包含 `publishMethod="FTP"` 的 `<publishProfile>` 元素。該元素如下所示：

        <publishProfile
            profileName="WebDemoWebApp - FTP"
            publishMethod="FTP"
            publishUrl="ftp://waws-prod-bay-NNN.ftp.azurewebsites.windows.net/site/wwwroot"
            ftpPassiveMode="True"
            userName="WebDemoWebApp\$WebDemoWebApp"
            userPWD="<deployment-password>"
            ...
        </publishProfile>

3. 請注意，Web 應用程式的 `publishProfile` 設定會對應至 FileZilla Site Manager 設定，如下所示：

- `publishUrl` 與 [**FTP 主機名稱**] 相同 (您在 [**主機**] 中設定的值)。
- `publishMethod="FTP"` 表示您將 [**通訊協定**] 設定為 [**FTP - 檔案傳輸通訊協定**]，而將 [**加密**] 設定為 [**使用一般 FTP**]。
- `userName` 和 `userPWD` 是您重設部署認證時指定之實際使用者名稱和密碼值的索引鍵。`userName` 與 [**部署 / FTP 使用者**] 相同。這些對應至 FileZilla 中的 [**使用者**] 和 [**密碼**]。
- `ftpPassiveMode="True"` 表示 FTP 站台使用被動 FTP 傳輸；選取 [**傳輸設定**] 索引標籤上的 [**被動**]。


#### 設定 Web 應用程式以裝載 Java 應用程式

在發佈應用程式前，您需要變更一些組態設定，以便 Web 應用程式裝載 Java 應用程式。

1. 在傳統入口網站中，移至 Web 應用程式的 [**儀表板**] 頁面並按一下 [**設定**]。在 [**設定**] 頁面上，指定下列設定。

2. [**Java 版本**] 的預設值為 [**關閉**]；選取應用程式鎖定的 Java 版本；例如 1.7.0_51。這麼做之後，還要確定 [**Web 容器**] 設定已設定為 Tomcat Server 的版本。

3. 在 [**預設文件**] 中，新增 index.jsp 並向上移至清單最上方。(Web 應用程式的預設檔案為 hostingstart.html。)

4. 按一下 [儲存]。


#### 使用 Kudu 發佈您的應用程式

使用 Azure 內建的 Kudu 偵錯主控台是發佈應用程式的一種方法。Kudu 已知可穩定而一致地使用於 App Service Web Apps 和 Tomcat Server。瀏覽至下列格式的 URL，以存取 Web 應用程式的主控台：

`https://<webappname>.scm.azurewebsites.net/DebugConsole`

1. 在此程序中，Kudu 主控台位於下列 URL；請瀏覽至此位置：

    `https://webdemowebapp.scm.azurewebsites.net/DebugConsole`

2. 從頂端功能表，選取 **[偵錯主控台] > [CMD]**。

3. 在主控台命令列中，導覽至 `/site/wwwroot` (或按一下 `site`，然後按一下頁面頂端的目錄檢視中的 `wwwroot`)：

    `cd /site/wwwroot`

4. 在您指定 [**Java 版本**] 後，Tomcat 伺服器應建立 webapps 目錄。在主控台命令列中，導覽至 webapps 目錄：

    `mkdir webapps`

    `cd webapps`

5. 從 `<project-path>/JSPHello/src/` 拖曳 JSPHello.war 並放在 `/site/wwwroot/webapps` 之下的 Kudu 目錄檢視中。請勿將它拖曳至 [拖曳至此以便上傳和壓縮] 區域，因為 Tomcat 會將它解壓縮。

  ![][8]

JSPHello.war 一開始會自行出現在目錄區域中：

  ![][9]

Tomcat Server 會在短時間 (或許少於 5 分鐘) 內將 WAR 檔案解壓縮到已解壓縮的 JSPHello 目錄中。按一下 ROOT 目錄，查看 index.jsp 是否已解壓縮並複製到此處。若是如此，請導覽回到 webapps 目錄，查看是否已建立已解壓縮的 JSPHello 目錄。如果您未看見這些項目，請稍後並重複。

  ![][10]


#### 使用 FileZilla 發佈您的應用程式 (選用)

FileZilla 是另一項可用來發佈應用程式的工具 ，這是具有便利圖形 UI 的熱門協力廠商 FTP 用戶端。如果您還沒有 FileZilla，可以從 [http://filezilla-project.org/](http://filezilla-project.org/) 下載並安裝此工具。如需使用用戶端的詳細資訊，請參閱 [FileZilla 文件](https://wiki.filezilla-project.org/Documentation)和 [FTP 用戶端 - 第 4 部分：FileZilla](http://blogs.msdn.com/b/robert_mcmurray/archive/2008/12/17/ftp-clients-part-4-filezilla.aspx) 上的這篇部落格。

1. 在 FileZilla 中，按一下 **[檔案] > [網站管理員]**。
2. 在 [**網站管理員**] 對話方塊中，按一下 [**新增網站**]。[**選取項目**] 中將會出現新的空白 FTP 網站，提示您提供名稱。在此程序中，將它命名為 `AzureWebDemo-FTP`

    在 [**一般**] 索引標籤上，指定下列設定： - **主機：**輸入您從儀表板複製的 [**FTP 主機名稱**]。 - **連接埠：** (將此空白，因為這是被動傳輸，伺服器將會決定要使用的連接埠。) - **通訊協定：** FTP 檔案傳輸通訊協定 - **加密：**使用一般 FTP - **登入類型：** 一般 - **使用者：**輸入您從儀表板複製的部署 / FTP 使用者。這是完整 FTP 使用者名稱，其格式為 *webappname\username*。 - **密碼：**輸入您設定部署認證時指定的密碼。

    在 [**傳輸設定**] 索引標籤上，選取 [**被動**]。

3. 按一下 [連接]。如果成功，FileZilla 的主控台將顯示 `Status: Connected` 訊息並發出 `LIST` 命令以列出目錄內容。

4. 在 [**本機**] 網站面板中，選取 JSPHello.war 檔案所在的來源目錄；路徑將類似下列路徑：

    `<project-path>/JSPHello/src/`

5. 在 [**遠端**] 網站面板中，選取目的地資料夾。您會將 WAR 檔案部署至 Web 應用程式的根目錄下的 `webapps` 目錄。導覽至 `/site/wwwroot`，以滑鼠右鍵按一下 `wwwroot`，然後選取 [**建立目錄**]。將目錄命名為 `webapps` 並輸入該目錄。

6. 將 JSPHello.war 傳輸至 `/site/wwwroot/webapps`選取 [**本機**] 檔案清單中的 JSPHello.war，在其上按一下滑鼠右鍵並選取 [**上傳**]。您應看見該檔案出現在 `/site/wwwroot/webapps`中。

7. 將 JSPHello.war 複製到 webapps 目錄之後，Tomcat Server 會自動將 WAR 檔案中的檔案解壓縮。雖然 Tomcat Server 幾乎立即開始進行解壓縮，但檔案可能需要很長的一段時間 (可能數小時) 才會出現在 FTP 用戶端中。


#### 在 Web 應用程式上執行 Hello World 應用程式

1. 上傳 WAR 檔案並確認 Tomcat Server 已建立解壓縮的 `JSPHello` 目錄後，請瀏覽至 `http://webdemowebapp.azurewebsites.net/JSPHello` 以執行應用程式。

    > **注意：**如果從傳統入口網站按一下 [**瀏覽**]，可能會出現預設網頁，表示「此 Java Web 應用程式建立成功」。 您可能必須重新整理網頁，才能檢視應用程式輸出 (而非預設網頁)。

2. 當應用程式執行時，您應看見有下列輸出的網頁：

    `Hello World, the time is Tue Mar 24 23:21:10 GMT 2015`


#### 清除 Azure 資源

此程序會建立 App Service Web 應用程式。只要資源存在，您就需支付費用。除非打算繼續使用 Web 應用程式進行測試或開發，否則您應考慮予以停止或刪除。已停止的 Web 應用程式仍將帶來少許費用，但您可以隨時予以重新啟動。刪除 Web 應用程式會清除您已上傳至其上的所有資料。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

  [1]: ./media/java-create-azure-website-using-java-sdk/eclipse-maven-repositories-rebuild-index.png
  [2]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-java-class.png
  [3]: ./media/java-create-azure-website-using-java-sdk/eclipse-new-dynamic-web-project.png
  [4]: ./media/java-create-azure-website-using-java-sdk/eclipse-java-build-path.png
  [5]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-tomcat-server.png
  [6]: ./media/java-create-azure-website-using-java-sdk/eclipse-targeted-runtimes-properties-page.png
  [7]: ./media/java-create-azure-website-using-java-sdk/eclipse-run-on-server.png
  [8]: ./media/java-create-azure-website-using-java-sdk/kudu-console-drag-drop.png
  [9]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-1.png
  [10]: ./media/java-create-azure-website-using-java-sdk/kudu-console-jsphello-war-2.png
 

<!---HONumber=62-->
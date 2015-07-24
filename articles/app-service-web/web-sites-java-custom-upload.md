<properties 
	pageTitle="將自訂 Java Web 應用程式上傳至 Azure" 
	description="本教學課程說明如何將自訂 Java Web 應用程式上傳至 Azure App Service Web Apps。" 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="robmcm"/>

# 將自訂 Java Web 應用程式上傳至 Azure

本主題說明如何將自訂 Java Web 應用程式上傳至 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps。內容包括適用於任何 Java 網站或 Web 應用程式的資訊，以及針對特定應用程式的一些範例。

請注意，如同[在 Azure App Service 中建立 Java Web 應用程式](web-sites-java-get-started.md)中的說明一樣，Azure 提供了使用 Azure 預覽入口網站的組態 UI 和 Azure Marketplace 來建立 Java Web 應用程式的方法。本教學課程適用於您不打算使用入口網站組態 UI 或 Azure Marketplace 的案例。

## 組態方針

下列內容說明在 Azure 上自訂 Java Web 應用程式的預期設定。

- 系統會動態指派 Java 程序所使用的 HTTP 連接埠。此程序必須使用來自環境變數 `HTTP_PLATFORM_PORT` 的連接埠。
- 您應停用所有的接聽連接埠 (單一 HTTP 接聽程式除外)。在 Tomcat 中，這包括了關機、HTTPS 和 AJP 連接埠。
- 僅需針對 IPv4 流量設定容器。
- 組態中必須設定應用程式的 **startup** 命令。
- 要求目錄具備寫入權限的應用程式必須位於 Azure Web 應用程式的內容目錄，也就是 **D:\home**。環境變數 `HOME` 是指 D:\home。  

您可以在 web.config 檔案中視需要設定環境變數。

## web.config httpPlatform 組態

下列資訊說明 web.config 內的 **httpPlatform** 格式。
                                 
**arguments** (預設值="")。在 **processPath** 設定中所指定的可執行檔或指令檔的引數。

範例 (顯示內容包括 **processPath**)：

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"


**processPath** - 將會啟動接聽 HTTP 要求程序的可執行檔或指令檔路徑。


範例：


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (預設值=10)。 **processPath** 中指定程序可容許的每分鐘當機次數。如果超過此限制，**HttpPlatformHandler** 便會停止在此分鐘剩餘時間內啟動程序。
                                    
**requestTimeout** (預設值="00:02:00")。 **HttpPlatformHandler** 會等待接聽 `%HTTP_PLATFORM_PORT%` 程序回應的持續時間。

**startupRetryCount** (預設值=10)。 **HttpPlatformHandler** 會嘗試啟動 **processPath** 中指定程序的次數。如需詳細資訊，請參閱 **startupTimeLimit**。

**startupTimeLimit** (預設值=10 秒)。 **HttpPlatformHandler** 會等待可執行檔/指令檔啟動接聽連接埠程序的持續時間。如果超過此時間限制，**HttpPlatformHandler** 會中止程序，並嘗試將它重新啟動 **startupRetryCount** 次。
                                                                                      
**stdoutLogEnabled** (預設值="true")。 如果為 true，則 **processPath** 設定中指定程序的 **stdout** 和 **stderr** 會被重新導向至 **stdoutLogFile** 中的指定檔案 (請參閱 **stdoutLogFile** 區段)。
                                    
**stdoutLogFile** (預設值="d:\home\LogFiles\httpPlatformStdout.log")。 **processPath** 中指定程序之 **stdout** 和 **stderr** 的絕對檔案路徑將會被記錄下來。
                                    
> [AZURE.NOTE]`%HTTP_PLATFORM_PORT%` 是個特殊預留位置，它必須以 **arguments** 的一部分或 **httpPlatform** **environmentVariables** 清單的一部分進行指定。這將會被透過 **HttpPlatformHandler** 內部產生的連接埠取代，以便 **processPath** 所指定的程序可以接聽此連接埠。

## Deployment

您可透過與 Internet Information Services (IIS) 架構 Web 應用程式中使用的大部分相同方式，來輕鬆部署 Java 型 Web 應用程式。FTP、Git 和 Kudu 為支援的部署機制，如同 Web 應用程式的整合式 SCM 功能。WebDeploy 會以通訊協定的方式運作，不過，由於 Java 不是使用 Visual Studio 開發的，WebDeploy 並不適用於 Java Web 應用程式部署使用案例。

## 應用程式組態範例

在下列應用程式中，我們將提供 web.config 檔案和應用程式組態作為範例，說明如何啟用 App Service Web Apps 上的 Java 應用程式。

### Tomcat
App Service Web Apps 隨附了兩個 Tomcat 變化，但很有可能您仍然可以上傳客戶特定執行個體。以下是搭配不同 JVM 之 Tomcat 的安裝範例。

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
	        arguments="">
	      <environmentVariables>
	        <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	        <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
	        <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

在 Tomcat 端，您必須進行幾個組態變更。必須編輯 server.xml 以設定下列選項：

-	關機連接埠 = -1
-	HTTP 連接器連接埠 = {port.http}
-	HTTP 連接器位址 = "127.0.0.1"
-	註解化 HTTPS 和 AJP 連接器
-	您也可以在 catalina.properties 檔案中進行 IPv4 設定，在此檔案中，您可以新增 `java.net.preferIPv4Stack=true`
    
App Service Web Apps 不支援 Direct3d 呼叫。若要將其停用，請新增下列 Java 選項，您的應用程式即可進行下列呼叫：`-Dsun.java2d.d3d=false`

### Jetty

和 Tomcat 的情況一様，客戶可以上傳他們自己的 Jetty 執行個體。在執行 Jetty 完整安裝的情況下，組態看來像這樣：

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Jetty 組態必須在 start.ini 中進行變更，進而設定 `java.net.preferIPv4Stack=true`。

### Hudson

我們的測試使用了 Hudson 3.1.2 war 和預設 Tomcat 7.0.50 執行個體，但沒有使用 UI 進行選項設定。因為 Hudson 是個軟體建置工具，建議您將它安裝在專屬執行個體上，您可以在專屬執行個體中設定 Web 應用程式的 **AlwaysOn** 旗標。

1. 在 Web 應用程式的根目錄中 (例如 **d:\home\site\wwwroot**) 建立 **webapps** 目錄 (如果尚未存在)，並將 Hudson.war 放在 **d:\home\site\wwwroot\webapps** 中。
2. 下載 apache maven 3.0.5 (與 Hudson 相容)，並將它放在 **d:\home\site\wwwroot** 中。
3. 在 **d:\home\site\wwwroot** 中建立 web.config，並將下列內容貼入 web.config：
	
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <handlers>
		      <add name="httppPlatformHandler" path="*" verb="*" 
		modules="httpPlatformHandler" resourceType="Unspecified" />
		    </handlers>
		    <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
		startupTimeLimit="20"
		startupRetryCount="10">
		<environmentVariables>
		  <environmentVariable name="HUDSON_HOME" 
		value="%HOME%\site\wwwroot\hudson_home" />
		  <environmentVariable name="JAVA_OPTS" 
		value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
		</environmentVariables>            
		    </httpPlatform>
		  </system.webServer>
		</configuration>

    此時，您可以重新啟動 Web 應用程式以接受變更。連線至 http://yourwebapp/hudson 以啟動 Hudson。

4. 在 Hudson 自行設定之後，您應該可以看到下列畫面：

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. 存取 Hudson 組態頁面：按一下 [Manage Hudson]，再按一下 [設定系統]。
6. 如下所示設定 JDK：

	![Hudson configuration](./media/web-sites-java-custom-upload/hudson2.png)

7. 如下所示設定 Maven：

	![Maven configuration](./media/web-sites-java-custom-upload/maven.png)

8. 儲存設定。Hudson 現在應已設定完成，並且可以開始使用。

如需 Hudson 的其他資訊，請參閱 [http://hudson-ci.org](http://hudson-ci.org)。

### Liferay

App Service Web Apps 支援 Liferay。因為 Liferay 需要大量記憶體，Web 應用程式必須在可提供足夠記憶體的中型或大型專用背景工作上執行。另外，Liferay 需要數分鐘的時間才能啟動。基於這個理由，建議您將 Web 應用程式設為 [Always On]。

使用隨附於 Tomcat 的 Liferay 6.1.2 Community Edition GA3 時，下列檔案在下載 Liferay 之後遭到編輯：

**Server.xml**

- 關機連接埠變為 -1。
- HTTP 連接器變為 `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- 註解化 AJP 連接器。

在 **liferay\tomcat-7.0.40\webapps\ROOT\WEB-INF\classes** 資料夾中，建立名為 **portal-ext.properties** 的檔案。此檔案必須包含一行程式碼，如下所示：

    liferay.home=%HOME%/site/wwwroot/liferay

在與 tomcat-7.0.40 資料夾相同的目錄層級中，根據下列內容建立名為 **web.config** 的檔案：

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	<add name="httpPlatformHandler" path="*" verb="*"
	     modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
	                  arguments="run" 
	                  startupTimeLimit="10" 
	                  requestTimeout="00:10:00" 
	                  stdoutLogEnabled="true">
	      <environmentVariables>
	  <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	  <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
	        <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

在 **httpPlatform** 區塊底下，**requestTimeout** 已設為 “00:10:00”。您可以降低此值，但如此一來，您很有可能會在啟動載入 Liferay 時看到一些逾時錯誤。如果此值遭到變更，則 tomcat server.xml 中的 **connectionTimeout** 也會同時進行修改。

值得一提的是，JRE_HOME 環境變數會在上述的 web.config 中進行指定，並指向 64 位元 JDK。預設值為 32 位元，但是因為 Liferay 可能需要高階記憶體，建議您使用 64 位元 JDK。

進行這些變更之後，請重新啟動執行 Liferay 的 Web 應用程式，然後開啟 http://yourwebapp。您可以在 Web 應用程式根目錄中找到 Liferay 入口網站。

如需 Liferay 的詳細資訊，請參閱 [http://www.liferay.com](http://www.liferay.com)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

<!---HONumber=62-->
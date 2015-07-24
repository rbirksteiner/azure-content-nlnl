<properties
   pageTitle="在雲端服務角色上安裝 .NET"
   description="本文說明如何在雲端服務 Web 和背景工作角色上手動安裝 .NET Framework。"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/23/2015"
   ms.author="saurabh"/>

# 在雲端服務角色上安裝 .NET 

本文說明如何在雲端服務 Web 和背景工作角色上安裝 .NET framework。您可以依照下列步驟在 Azure 客體 OS 系列 4 上安裝.NET framework 4.5.2。如需最新的客體 OS 版本相關資訊，請參閱 [Azure 客體 OS 版本與 SDK 相容性比較表](cloud-services-guestos-update-matrix.md)。

在 Web 和背景工作角色上安裝 .NET 的程序會涉及在雲端專案中加入 .NET 安裝程式套件，並在角色的啟動工作過程中啟動安裝程式。

## 將 .NET 安裝程式加入至專案
1. 下載 [.NET 4.5.2 Web 安裝程式](http://www.microsoft.com/zh-tw/download/details.aspx?id=42643)
2. 若是 Web 角色
  1. 在 [**方案總管**] 中，於雲端服務專案中的 [**角色**] 下，以滑鼠右鍵按一下您的角色，然後依序選取 [**新增 > 新增資料夾**]。建立名為 *bin* 的資料夾
  2. 在 **bin** 資料夾上按一下滑鼠右鍵，並依序選取 [**新增 > 現有項目**]。選取 .NET 安裝程式，並將它加入至 bin 資料夾。
3. 若是背景工作角色
  1. 以滑鼠右鍵按一下您的角色，然後依序選取 [**新增 > 現有項目**]。選取 .NET 安裝程式，並將它加入至角色。 

以此方式新增至角色內容資料夾的檔案會自動加入至雲端服務封裝，並部署至虛擬機器上的一致位置。為雲端服務中的所有 Web 和背景工作角色重複此程序，以便所有角色都有安裝程式副本。

![安裝程式檔案的角色內容][1]

## 定義角色的啟動工作
啟動工作可讓您在啟動角色之前執行作業。將 .NET Framework 做為啟動工作的一部分安裝，可確保在執行任何應用程式程式碼之前已安裝好 Framework。如需有關啟動工作的詳細資訊，請參閱：[在 Azure 中執行啟動工作](https://msdn.microsoft.com/library/azure/hh180155.aspx)。

1. 將下列內容加入所有角色中 *WebRole* 或 *WorkerRole* 節點底下的 *ServiceDefinition.csdef* 檔案：
	
	```xml
	 <LocalResources>
	    <LocalStorage name="InstallLogs" sizeInMB="5" cleanOnRoleRecycle="false" />
	 </LocalResources>
	 <Startup>
	    <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
	        <Environment>
	        <Variable name="PathToInstallLogs">
	        <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='InstallLogs']/@path" />
	        </Variable>
	        </Environment>
	    </Task>
	 </Startup>
	```

	上述組態會使用系統管理員權限來執行主控台命令 *install.cmd*，以便安裝 .NET Framework。此組態也會建立名為 *InstallLogs* 的 LocalStorage，以儲存安裝指令碼所建立的任何記錄檔資訊。如需詳細資訊，請參閱：[使用本機儲存體儲存啟動期間的檔案](https://msdn.microsoft.com/library/azure/hh974419.aspx)

2. 建立含有下列內容的 install.cmd 檔案：

	```
	REM install.cmd to install .NET Framework
	set timehour=%time:~0,2%
	set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
	set startuptasklog=%PathToInstallLogs%\startuptasklog-%timestamp%.txt
	set netfxinstallerlog = %PathToInstallLogs%\NetFXInstallerLog-%timestamp%
	echo Logfile generated at: %startuptasklog% >> %startuptasklog%
	echo Checking if .NET 4.5.2 is installed >> %startuptasklog%
	reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find "0x5cbf5"
	if %ERRORLEVEL%== 0 goto end
	echo Installing .NET 4.5.2. Log: %netfxinstallerlog% >> %startuptasklog%
	start /wait %~dp0NDP452-KB2901954-Web.exe /q /serialdownload /log %netfxinstallerlog%
	:end
	echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
	```

	安裝指令碼會透過查詢登錄來檢查 .NET 4.5.2 是否已在在電腦上安裝。如果未安裝.NET 4.5.2，則 .Net Web 安裝程式便會啟動。為協助疑難排解任何問題，該指令碼會將所有活動記錄到名為 *startuptasklog-(currentdatetime).txt* (儲存於 *InstallLogs* 本機儲存體) 的檔案。

	> [AZURE.NOTE]使用如 [記事本] 之類的簡單文字編輯器來建立這個檔案。如果使用 Visual Studio 來建立文字檔案，然後將它重新命名為 '.cmd'，則此檔案可能仍會包含 UTF-8 位元順序標記，並在執行第一行的指令碼時出現錯誤。如果您要使用 Visual Studio 來建立檔案，請在檔案的第一行保留加入 REM (備註)，以便在執行時將它忽略。
      
3. 以滑鼠右鍵按一下角色，並依序選取 [**新增 > 現有項目**]，即可將 install.cmd 檔案新增至所有角色。因此，所有角色現在應該都有 .NET 安裝程式檔案，以及 install.cmd 檔案。
	
	![所有檔案的角色內容][2]

## 設定診斷，將啟動工作記錄檔傳輸到 Blob 儲存體 (選擇性)
您可以選擇性地設定 Azure 診斷，將啟動指令碼或 .NET 安裝程式所產生的任何記錄檔傳輸到 Blob 儲存體。透過這種方法，您可以從 Blob 儲存體直接下載記錄檔，而無需遠端桌面到角色，即可檢視記錄檔。

若要設定診斷，請開啟 *diagnostics.wadcfgx*，並在 *<Directories>* 節點底下加入下列內容：

```xml 
<DataSources>
    <DirectoryConfiguration containerName="netfx-install">
    <LocalResource name="InstallLogs" relativePath="."/>
    </DirectoryConfiguration>
</DataSources>
```

這會設定 Azure 診斷將 *InstallLogs* 資源中的所有檔案，傳輸到 *netfx-install* Blob 容器中的診斷儲存體帳戶。

## 部署您的服務 
部署服務時，啟動工作將會執行並安裝 .NET Framework (如果尚未安裝)。安裝 Framework 時，您的角色將會處於忙碌狀態，而且甚至可能會重新啟動 (如果 Framework 安裝有此要求)。


## 其他資源

- [安裝 .NET Framework][]
- [作法：判斷安裝的 .NET Framework 版本][]
- [疑難排解 .NET Framework 安裝][]

[作法：判斷安裝的 .NET Framework 版本]: https://msdn.microsoft.com/library/hh925568.aspx
[安裝 .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[疑難排解 .NET Framework 安裝]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<!---HONumber=62-->
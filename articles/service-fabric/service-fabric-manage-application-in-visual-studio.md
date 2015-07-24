<properties
   pageTitle="在 Visual Studio 中管理 Service Fabric 應用程式"
   description="您可以透過 Visual Studio 管理 Microsoft Azure Service Fabric 應用程式與服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/14/2015"
   ms.author="jesseb"/>

# 在 Visual Studio 中管理 Service Fabric 應用程式

您可以透過 Visual Studio 管理 Microsoft Azure Service Fabric 應用程式與服務。[設定開發環境](../service-fabric-setup-your-development-environment)後，您可以使用 Visual Studio 在本機開發叢集中建立 Service Fabric 應用程式、新增服務，或封裝、註冊及部署應用程式。

若要管理 Service Fabric 應用程式，請到 [方案總管] 中，在應用程式專案上按一下滑鼠右鍵。

![在應用程式專案上按一下滑鼠右鍵以管理 Service Fabric 應用程式][manageservicefabric]

## 部署 Service Fabric 應用程式

部署 Service Fabric 應用程式會將以下幾個步驟結合成一個簡單的作業。

1. 建立應用程式封裝
2. 將應用程式封裝上傳至映像存放區
3. 註冊應用程式類型
4. 移除任何執行中的應用程式執行個體
5. 建立新的應用程式執行個體

在 Visual Studio 的 [**方案總管**] 中，以滑鼠右鍵按一下 Service Fabric 應用程式，然後按一下 [**部署**]，即完成部署。按下 **F5** 也會部署應用程式，並將偵錯工具附加到所有應用程式執行個體。

使用 [**移除部署**] 可移除部署。這會還原上述的部署步驟。

1. 移除任何執行中的應用程式執行個體
2. 取消註冊應用程式類型
3. 移除映像存放區中的應用程式封裝

## 新增服務至 Service Fabric 應用程式

您可以將新的 Fabric 服務新增至應用程式以擴充其功能。若要確保應用程式封裝中包含該服務，請透過 [**新 Fabric 服務**] 功能表項目新增服務。

![新增新 Fabric 服務至應用程式][newservice]

選取要新增至應用程式的 Service Fabric 專案類型，並指定服務的名稱。請參閱[選擇服務架構](service-fabric-choose-framework.md)，以協助決定要使用的服務類型。

![選取要新增至應用程式的 Fabric 服務類型][addserviceproject]

新服務將會新增到方案與現有的應用程式封裝。服務參照與預設的服務執行個體將會新增到應用程式資訊清單。該服務會在下次您部署應用程式時建立並啟動。

![新服務將會新增到應用程式資訊清單][newserviceapplicationmanifest]

## 封裝 Service Fabric 應用程式

必須先建立應用程式封裝，才能將應用程式及其服務部署至叢集。封裝會以特定的配置來組織應用程式資訊清單、服務資訊清單和其他必要的檔案。Visual Studio 會在 'pkg' 目錄的應用程式專案資料夾中設定與管理封裝。按一下 [**封裝**] 建立或更新應用程式封裝。如果您使用自訂的 Powershell 指令碼部署應用程式，您可能會想要這麼做。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

- [Service Fabric 應用程式模型](../service-fabric-application-model)
- [Service Fabric 應用程式部署](../service-fabric-deploy-remove-applications)
- [偵錯 Service Fabric 應用程式](../service-fabric-debugging-your-application)
- [使用 Service Fabric 總管視覺化叢集](../service-fabric-visualizing-your-cluster)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
 

<!---HONumber=July15_HO2-->
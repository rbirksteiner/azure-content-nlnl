<properties
   pageTitle="開始使用可靠動作項目 | Microsoft Azure"
   description="本教學課程將引導您使用 Service Fabric 可靠動作項目，以建立、偵錯及部署標準的 HelloWorld 服務的步驟。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/17/2015"
   ms.author="claudioc"/>

# 可靠動作項目：標準的 HelloWorld 逐步解說案例
本文說明 Service Fabric 可靠動作項目的基本概念，並將逐步引導您在 Visual Studio 中建立、偵錯及部署簡單的 HelloWorld 應用程式。

## 安裝與設定
開始之前，確定機器上已設定 Service Fabric 開發環境。如需如何設定開發環境的詳細資訊，請參閱[這裡](service-fabric-get-started.md)。

## 基本概念
若要開始使用可靠動作項目，您只需要了解 4 個基本概念：

* **動作項目服務**。可靠動作項目封裝在可在 Service Fabric 基礎結構內部署的服務中。服務可以裝載一或多個動作項目。我們將在稍後討論如何在各服務之間取捨一或多個動作項目。現在讓我們假設只需要實作一個動作項目。
* **動作項目介面**。動作項目介面用於定義動作項目的公用介面。在動作項目模型術語中，其定義動作項目能夠了解程序的訊息類型。其他的動作項目或用戶端應用程式會使用動作項目介面將訊息「傳送」(非同步) 給動作項目。可靠動作項目可實作多個介面，如我們所見，HelloWorld Actor 可實作 IHelloWorld 介面，但也會實作定義訊息/功能的 ILogging 介面。
* **動作項目註冊**。在動作項目服務中必須註冊動作項目類型，Service Fabric 才能感知新的類型，並將其用於建立新的動作項目。
* **ActorProxy 類別**。ActorProxy 類別用於與動作項目繫結，並叫用透過其介面公開的方法。ActorProxy 類別提供兩個重要的功能：
	* 名稱解析：能夠在叢集中找到動作項目 (在裝載動作項目的叢集節點中尋找)。
	* 處理失敗：會重試方法叫用並重新決定動作項目位置，例如在需要動作項目重新定位至叢集中另一個節點失敗後進行。

## 在 Visual Studio 中建立新專案
安裝 Visual Studio 專用的 Service Fabric 工具後，您可以建立新的專案類型。新專案類型位在 [新專案] 對話方塊的 [雲端] 類別下


![][1]


讓我們為 HelloWorld 專案使用 Service Fabric 動作項目服務。

建立專案之後，您應該會看到下列結構：

![][2]

## 可靠動作項目基本建置組塊

一般的可靠動作項目方案是由 3 個專案組成：

* 應用程式專案 (HelloWorldApplication)。此專案會將所有的服務封裝在一起部署。其包含了用於管理應用程式的 ApplicationManifest.xml 與 PowerShell 指令碼。

* 介面專案 (HelloWorld.Interfaces)。此專案包含動作項目的介面定義。在介面專案中，您可以定義將由方案中的動作項目使用者介面。

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld.Interfaces
{
    public interface IHelloWorld : IActor
    {
        Task<string> SayHello(string greeting);
    }
}

```

* 服務專案 (HelloWorld)。此專案用於定義即將裝載動作項目的 Service Fabric 服務。其包含某些重複使用的程式碼，這些程式碼在大多數的狀況下 (ServiceHost.cs) 與實作動作項目時不需要編輯。實作動作項目會涉及實作衍生自基底類型 (動作項目) 的類別，並實作在 .Interfaces 專案中定義的介面。

```csharp

using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;
using System.Threading.Tasks;
using HelloWorld.Interfaces;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Actors;

namespace HelloWorld
{
    public class HelloWorld : Actor, IHelloWorld
    {
        public Task<string> SayHello(string greeting)
        {
            return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
        }
    }
}

```

動作項目服務專案包含建立 Service Fabric 服務的程式碼，在服務定義中會註冊動作項目類型，使其能夠用於具現化新的動作項目。

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterActor(typeof(HelloWorld));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e);
            throw;
        }
    }
}  

```

如果您從在 Visual Studio 中建立新專案開始，且您只有一個動作項目定義，則該註冊預設會包含在 Visual Studio 產生的程式碼中。如果您在服務中定義其他的動作，您必須使用下列項目新增動作項目註冊：

```csharp

fabricRuntime.RegisterActor(typeof(MyNewActor));


```

## 偵錯

Visual Studio 專用的 Service Fabric 工具支援在本機機器上偵錯。您可以點擊 F5 開始偵錯工作階段。Visual Studio 會在本機 Service Fabric 叢集上 (視需要) 建置封裝與部署應用程式，並附加偵錯工具。會有與偵錯 ASP.NET 應用程式類似的經驗。在部署的過程中，您可在 [輸出視窗] 中查看進度

![][3]

## 應用程式部署
從 Visual Studio，您也可以選取 Service Fabric 應用程式專案，然後以滑鼠按一下右鍵，在本機叢集中封裝與部署應用程式，無須啟動偵錯工具。

![][4]

* **部署**：封裝應用程式並啟動部署程序
* **移除部署**：可用來從本機叢集移除應用程式
* **封裝**：封裝應用程式。此動作對於將應用程式準備好部署在不同的叢集上很有幫助，例如部署在 Azure 上。

## 後續步驟

[Service Fabric Actor 簡介](service-fabric-reliable-actors-introduction.md)



<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
 

<!---HONumber=July15_HO2-->
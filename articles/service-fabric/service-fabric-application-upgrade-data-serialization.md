<properties
   pageTitle="Service Fabric 應用程式升級：資料序列化"
   description="資料序列化的最佳作法，以確保成功的應用程式升級。"
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
   ms.date="04/22/2015"
   ms.author="jesseb"/>


# Service Fabric 應用程式升級：資料序列化

在[輪流應用程式升級](service-fabric-application-upgrade.md)中，升級會套用至節點的子集，一次一個升級網域。在此過程中，某些升級網域會比您的應用程式版本新，而某些升級網域會比您的應用程式的版本舊。此時，新版的應用程式必須能夠讀取舊版資料，舊版的應用程式必須能夠讀取新版資料。如果資料格式不是向前及向後相容，升級可能會失敗，或可能會遺失資料。本文將討論您的資料格式的構成項目和最佳作法，以確保您的資料向前及向後相容。


## 資料格式的構成項目？

Service Fabric 中保留及複寫的資料來自您的 C# 類別。對於使用[可靠的集合](service-fabric-reliable-services-reliable-collections.md)的應用程式，也就是可靠的字典和佇列中的物件。對於使用[可設定狀態的可靠的執行者](service-fabric-reliable-actors-introduction.md)的應用程式，也就是執行者的備份狀態。這些 C# 類別必須是可序列化，以便保存和複寫。因此，資料格式是由已序列化的欄位和屬性，以及其序列化方式來定義。例如，在 `IReliableDictionary<int, MyClass>` 中，資料是序列化 `int` 和序列化 `MyClass`。

### 資料格式變更

由於資料格式是由 C# 類別決定，所以類別的變更可能會導致資料格式變更。請小心確保輪流升級能夠處理資料格式變更。可能會造成資料格式變更的範例：

- 新增或移除欄位或屬性
- 重新命名欄位或屬性
- 變更欄位或屬性的類型
- 變更類別名稱或命名空間

### 預設序列化程式

序列化程式通常負責讀取資料，以及還原序列化為目前版本，即使資料是舊版或*新*版。預設的序列化程式是[資料合約序列化程式](https://msdn.microsoft.com/library/ms733127.aspx)，其具有定義完善的版本控制規則。可靠的集合允許序列化程式加以覆寫，但是可靠的執行者目前不允許。資料序列化程式在啟用輪流升級中扮演著重要的角色。資料合約序列化程式是建議的 Service Fabric 應用程式的序列化程式。


## 資料格式如何影響輪流升級

輪流升級期間，有兩種序列化程式可能會遇到舊版或*新*版資料的主要案例：

1. 節點升級並重新啟動之後，新的序列化程式會載入資料，該資料保存到舊版的磁碟。
2. 輪流升級期間，叢集可能包含舊和新版本程式碼的混合。由於複本會放在不同升級網域，序列化程式可能會遇到您的新版和舊版資料 (序列化程式本身可能是新版或舊版)。

> [AZURE.NOTE]此處的「新版」和「舊版」是指您正在執行的程式碼的版本。「新序列化程式」是指在您的新版應用程式中執行的序列化程式程式碼。「新資料」是指來自您的新版應用程式的序列化 C# 類別。

兩個版本的程式碼和資料格式必須同時向前及向後相容。如果它們不相容，輪流升級可能會失敗，或可能會遺失資料。輪流升級可能會失敗，因為程式碼或序列化程式在遇到其他版本時可能會擲回例外狀況或錯誤。比方說，如果已加入新屬性，但是舊的序列化程式在還原序列化期間捨棄它，則可能會遺失資料。


## 資料合約

資料合約是建議的解決方案，以確保您的資料相容。它有新增、移除及變更欄位的定義完善版本控制規則。它也支援處理未知欄位、連結到序列化和還原序列化程序，以及類別繼承。如需詳細資訊，請參閱[使用資料合約](https://msdn.microsoft.com/library/ms733127.aspx)。


## 後續步驟

[升級教學課程](service-fabric-application-upgrade-tutorial.md)

[升級參數](service-fabric-application-upgrade-parameters.md)

[進階主題](service-fabric-application-upgrade-advanced.md)
 

<!---HONumber=July15_HO2-->
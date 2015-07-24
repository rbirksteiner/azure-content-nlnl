
<properties 
    pageTitle="RemoteApp 的應用程式需求"
    description="深入了解您想要用於 RemoteApp 的應用程式需求" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/11/2015" 
    ms.author="elizapo" />



# 應用程式需求
RemoteApp 支援從 Windows Server 2012 R2 安裝的資料流 32 位元或 64 位元 Windows 架構應用程式。大部分現有 32 位元或 64 位元 Windows 架構應用程式都是「依現狀」在 RemoteApp (遠端桌面服務或舊稱的「終端機服務」) 環境中執行。不過，執行和良好執行是有差別的，有些應用程式運作正常且完善執行，有些則不是。下列資訊提供在遠端桌面服務環境中開發應用程式，及測試以確保相容性的指引。

提示：我們正在為您建立一些實用的應用程式範例。每月結束時，您會看到討論如何在 RemoteApp 內使用 Microsoft Access、QuickBooks 和 APP-V 的新主題。

## 需求
如果遵循下列三個需求，可協助應用程式在 RemoteApp 中順暢執行： 

1.	符合所有 [Windows 傳統型應用程式認證需求](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx)，並遵循[遠端桌面服務程式設計指導方針](https://msdn.microsoft.com/library/aa383490.aspx) (英文) 的應用程式，皆與 RemoteApp 完全相容。 
2.	應用程式應該永遠不要在可能遺失的映像或 RemoteApp 執行個體中本機儲存資料。建立 RemoteApp 集合之後，會複製執行個體，且不會呈現狀態並只包含應用程式。將資料儲存在外部來源，或使用者的設定檔內。 
3.	自訂映像不應該包含可能會遺失的資料。  

## 測試應用程式
使用下列步驟來測試應用程式：

1.	安裝 Windows Server 2012 R2 和您的應用程式
2.	啟用遠端桌面
3.	建立兩個使用者帳戶 (UserA 和 UserB)，將這兩個使用者帳戶新增至遠端桌面的安全性群組。 
4.	啟動應用程式時建立兩個同時 RDS 工作階段到電腦，藉此檢查多重工作階段相容性。
5.	驗證應用程式行為

## 應用程式開發指導方針
使用下列指導方針來開發 RemoteApp 的應用程式。 

### 多個使用者
 
- 為[單一使用者安裝一個應用程式](https://msdn.microsoft.com/library/aa380661.aspx) (英文) 可能在多使用者環境中造成問題。 
- 應用程式應在使用者特定位置中[儲存使用者專屬資訊](https://msdn.microsoft.com/library/aa383452.aspx) (英文)，藉此與套用到所有使用者的全域資訊區隔。 
- RemoteApp [為核心物件使用多個命名空間](https://msdn.microsoft.com/library/aa382954.aspx) (英文)；主要是藉由用戶端/伺服器應用程式中的服務來使用全域命名空間。 
- 假設指派給電腦的電腦名稱或 [IP 位址](https://msdn.microsoft.com/library/aa382942.aspx) (英文) 與單一使用者相關聯並不安全，因為多個使用者可以同時登入遠端桌面工作階段主機 (RD 工作階段主機) 伺服器。 

### 效能
- 先停用[圖形效果](https://msdn.microsoft.com/library/aa380822.aspx) (英文)，再將應用程式新增至 RemoteApp。
- 若要最大化所有使用者的 CPU 可用性，請停用 [ 背景工作 ](https://msdn.microsoft.com/library/aa380665.aspx) (英文) 或建立不需要大量資源的高效率背景工作。 
- 您應該針對多使用者、多處理器環境微調和平衡應用程式[執行緒使用量](https://msdn.microsoft.com/library/aa383520.aspx) (英文)。
- 若要最佳化效能，讓應用程式[偵測](https://msdn.microsoft.com/library/aa380798.aspx)它們是否正在用戶端工作階段中執行是個好方法。 


<!--HONumber=52--> 
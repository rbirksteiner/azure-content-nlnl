<properties
   pageTitle="從 System Center Operations Manager 連接至 Operational Insights"
   description="深入了解如何透過 Operations Manager 連接到 Operational Insights。"
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="lauracr"/>

# 從 System Center Operations Manager 連接至 Operational Insights


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

您可以將 Operational Insights 連線至現有的 System Center Operations Manager 環境。這可讓您使用現有的 Operations Manager 代理程式收集資料。如需有關搭配使用 Operations Manager 與 Operational Insights 的其他資訊，請參閱 [Operational Insights 的 Operations Manager 考量](operational-insights-operations-manager.md)。

如果您使用 Operations Manager 監視下列任何工作負載，您將需要為其設定 Operations Manager 執行身分帳戶。如需設定帳戶的詳細資訊，請參閱 [Operational Insights 的 Operations Manager 考量](operational-insights-operations-manager.md)

- SQL 評估
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE]使用 Operations Manager 2012 SP1 UR6 及 Operations Manager 2012 R2 UR2 支援 Operational Insights。System Center Operations Manager 2012 SP1 UR7 和 System Center Operations Manager 2012 R2 UR3 中已加入 Proxy 支援。

## 將 Operations Manager 連接至 Operational Insights 和加入代理程式

1. 在 Operations Manager 主控台中，按一下 [**管理**]。

2. 展開 [**Operational Insights**] 節點，然後按一下 [**Operational Insights 連接**]。

3. 按一下 [**註冊至 Operational Insights**] 連結，並遵循畫面上的指示執行。

4. 完成註冊精靈之後，按一下 [**新增電腦/群組**]。![Operations Manager 會新增電腦/群組](./media/operational-insights-connect-scom/om01.png)
5. 在 [**電腦搜尋**] 對話方塊中，您可以搜尋 Operations Manager 監視的電腦或群組。選取電腦或群組以連線至 Operational Insights，按一下 [**新增**]，然後按一下 [**確定**]。![Operations Manager 會新增電腦](./media/operational-insights-connect-scom/om02.png)
## 後續步驟

[設定 Proxy 和防火牆設定 (選擇性)](operational-insights-proxy-firewall.md)

<!---HONumber=July15_HO2-->
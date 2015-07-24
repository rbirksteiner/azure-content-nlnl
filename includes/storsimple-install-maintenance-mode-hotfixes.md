
#### 透過 Windows PowerShell for StorSimple 安裝維護模式 Hotfix

> [AZURE.IMPORTANT]在維護模式中，您需要先在某一個控制站上套用 Hotfix，然後在另一個控制站上套用 Hotfix。

1. 使裝置處於維護模式。如需如何進入維護模式的相關指示，請參閱[進入維護模式](#enter-maintenance-mode)。

2. 若要套用 Hotfix，請輸入：

     `Start-HcsHotfix`

3. 出現提示時，請提供包含 Hotfix 檔案的網路共用資料夾所在路徑。

4. 系統將提示您進行確認。輸入 **Y** 繼續安裝 Hotfix。

5. 在某一個控制站上套用 Hotfix 之後，請登入另一個控制站。使用您為前一個控制站所做的方式來套用 Hotfix。

6. 套用 Hotfix 之後，結束維護模式。如需相關指示，請參閱[結束維護模式](#exit-maintenance-mode)。

<!---HONumber=July15_HO2-->
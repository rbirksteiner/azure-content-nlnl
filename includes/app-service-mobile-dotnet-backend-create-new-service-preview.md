

依照下列步驟建立新的行動應用程式。

1. 登入 [Azure 入口網站]。在視窗左下角，按一下 [**+新增**]。捲動到您看到 [**行動應用程式**] 項目。

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/new-mobile-app.png)

    便會顯示 [**新增行動應用程式**] 刀鋒視窗。

2. 輸入您的行動應用程式的名稱。其長度必須至少是 8 個字元，以及小寫 a 到 z。

7. 選取區域。在本教學課程中，我們使用 [**中南美洲**]。

    > [AZURE.NOTE]作為本教學課程的一部分，您需要建立新的 SQL 資料庫執行個體和伺服器。You can reuse this new database and administer it as you would any other SQL Database instance.如果您在相同區域中已經有資料庫做為新的行動應用程式後端，可以改選 [**使用現有資料庫**]，然後選取該資料庫。不建議您使用位在不同區域的資料庫，因為這會需要額外的頻寬成本和產生更高的延遲。

3. 選取您的訂閱。

4. 使用與您的行動應用程式相同的名稱，建立新的資源群組。

5. 在 [**封裝設定**] 中，選取 [**USERDATABASE**]。您可以選擇現有的資料庫或建立一個新的資料庫。若要建立新的資料庫，輸入新「**資料庫**」的名稱、建立新的「**伺服器**」、輸入該伺服器的名稱，然後選擇一個「**登入名稱**」(這是新的 SQL Database 伺服器的系統管理員登入名稱)，輸入並確認密碼，然後按一下 [確定] 按鈕以完成程序。如果選取現有的資料庫，您必須提供 [**伺服器系統管理員密碼**]。

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create-db.png)

6. 使用與您的行動應用程式相同的名稱，建立新的虛擬主機方案。

    > [AZURE.NOTE]輸入您的虛擬主機方案名稱，請勿複製/貼上。此欄位有一個名稱驗證，如果您沒有輸入名稱，驗證便會失敗。它不一定是與網站完全相同的名稱 (但還是需要遵循相同的規則)。

8. 選取一個 [價格層]。在本教學課程中，我們使用 [**標準 1**]。

    新的行動應用程式設定頁面現在如下所示：

    ![](./media/app-service-mobile-dotnet-backend-create-new-service-preview/dotnet-backend-create.png)

9. 按一下刀鋒視窗底部的 [**建立**] 按鈕，您應該就會看到它在 [通知] 視窗中開始部署。

您現在已經建立一個可供行動應用程式使用的新行動應用程式後端。

> [AZURE.NOTE]建立您的行動應用程式之後，瀏覽至入口網站中您剛才所建立的 SQL Server (請務必選取伺服器，而非 Azure SQL 資料庫)。從該處按一下設定部分、展開防火牆部分，然後變更 [允許存取 Azure 服務]。如果您不這樣做，您的應用程式將無法運作。

<!-- URLs. -->
[Azure 入口網站]: https://portal.azure.com/

<!---HONumber=62-->
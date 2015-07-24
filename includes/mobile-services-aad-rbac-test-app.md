
下面的指示和螢幕擷取畫面適用於測試 Windows 市集用戶端，但您可以在 Azure 行動服務所支援的任何平台上進行測試。

1. 在 Visual Studio 中執行用戶端應用程式，並嘗試驗證名為 Dave 的已建立使用者帳戶。 

    ![](./media/mobile-services-aad-rbac-test-app/dave-login.png)

2. Dave 沒有 Sales 群組的成員資格。因此以角色為基礎的存取檢查將會拒絕存取資料表作業。關閉用戶端應用程式。

    ![](./media/mobile-services-aad-rbac-test-app/unauthorized.png)

3. 在 Visual Studio 中重新執行用戶端應用程式。這一次驗證名為 Bob 的已建立使用者帳戶。

    ![](./media/mobile-services-aad-rbac-test-app/bob-login.png)

4. Bob 確實具有 Sales 群組的成員資格。因此以角色為基礎的存取檢查將會允許存取資料表作業。

    ![](./media/mobile-services-aad-rbac-test-app/success.png)

<!---HONumber=July15_HO2-->
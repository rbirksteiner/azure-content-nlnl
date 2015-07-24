
### 步驟 1：在管理入口網站中授權裝置以變更服務資料加密金鑰

一般而言，裝置系統管理員將會要求服務管理員授權裝置，以變更服務資料加密金鑰。然後服務管理員就會授權裝置來變更金鑰。

此步驟是在管理入口網站中執行。服務管理員可以在有資格獲得授權的裝置顯示清單中選取裝置。然後裝置就會獲得授權，以啟動服務資料加密金鑰變更程序。

#### 哪些裝置可獲得授權以變更服務資料加密金鑰？

裝置必須符合下列準則，才能獲得授權以起始服務資料加密金鑰變更：

- 裝置必須在線上，才能獲得授權以變更服務資料加密金鑰。

- 如果尚未起始金鑰變更，您可以在 30 分鐘後再次授權同一個裝置。

- 如果先前獲得授權的裝置尚未起始金鑰變更，則您可以授權另一個裝置。新裝置已獲授權之後，舊裝置就無法起始變更。

- 正在變換服務資料加密金鑰時，無法授權裝置。

- 如果有些已向服務註冊的裝置已變換加密，而某些裝置還沒有，則您可以授權裝置。在這種情況下，有資格的裝置就是已完成服務資料加密金鑰變更的裝置。

> [AZURE.NOTE]在管理入口網站中，可獲授權以啟動金鑰變更的裝置清單不會顯示 StorSimple 虛擬裝置。

請執行下列步驟，選取並授權裝置以起始服務資料加密金鑰變更。

#### 授權裝置以變更索引鍵

1. 在服務儀表板頁面上，按一下 [**變更服務資料加密金鑰**]。

    ![變更服務加密金鑰](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)

2. 在 [**變更服務資枓加密金鑰**] 對話方塊中，選取並授權裝置以起始服務資料加密金鑰變更。下拉式清單包含有資格獲得授權的所有裝置。

3. 按一下核取圖示 ![核取圖示](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png)。

### 步驟 2：使用 Windows PowerShell for StorSimple 起始服務資料加密金鑰變更

這個步驟是在 Windows PowerShell for StorSimple 介面中，對已獲授權的 StorSimple 裝置執行。

> [AZURE.NOTE]除非金鑰變換完成，否則無法在管理入口網站對 StorSimple Manager 服務執行任何作業。

如果您使用裝置序列主控台連接到 Windows PowerShell 介面，請執行下列步驟。

#### 起始服務資料加密金鑰變更

1. 選取選項 1 以使用完整存取權登入。

2. 在命令提示字元中，輸入：

     `Invoke-HcsmServiceDataEncryptionKeyChange`

3. Cmdlet 順利完成之後，您就會得到新的服務資料加密金鑰。複製並儲存此金鑰，以供此程序的步驟 3 使用。此金鑰將用來更新已向 StorSimple Manager 服務註冊的其餘所有裝置。

    > [AZURE.NOTE]此程序必須在授權 StorSimple 裝置後的四個小時內起始。

    然後，新金鑰就會傳送至服務並推播至所有已向服務註冊的裝置。之後，服務儀表板上將出現警示。此服務會停用已註冊的裝置上的所有作業，然後裝置管理員就必須在其他裝置上更新服務資料加密金鑰。不過，不會中斷 I/O (將資料傳送至雲端的主機)。

    如果只有一個裝置向您的服務註冊，則變換程序現在已完成，您可以略過下一個步驟。如果有多個裝置向您的服務註冊，請繼續步驟 3。

### 步驟 3：在其他 StorSimple 裝置上更新服務資料加密金鑰

如果有多個裝置向您的 StorSimple Manager 服務註冊，您就必須在 StorSimple 裝置的 Windows PowerShell 介面中執行這些步驟。您必須使用您在「步驟 2：使用 Windows PowerShell for StorSimple 起始服務資料加密金鑰變更」中取得的金鑰，更新已向 StorSimple Manager 服務註冊的其餘所有 StorSimple 裝置。

請執行下列步驟，在您的裝置上更新服務資料加密。

#### 更新服務資料加密金鑰

1. 使用 Windows PowerShell for StorSimple 連線到主控台。選取選項 1 以使用完整存取權登入。

2. 在命令提示字元中，輸入：

    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`

3. 提供您在[步驟 2：使用 Windows PowerShell for StorSimple 起始服務資料加密金鑰變更](##to-initiate-the-service-data-encryption-key-change)中取得的服務資料加密金鑰。

<!---HONumber=July15_HO2-->
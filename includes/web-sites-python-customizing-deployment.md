如果以下條件成立，Azure 將會確定您的應用程式使用 Python：

- 根資料夾中有 requirements.txt 檔案
- 根資料夾中有任何 .py 檔案，或擁有指定 Python 的 runtime.txt

若是如此，它將使用 Python 專屬的部署指令碼，此指令碼可執行檔案的標準同步處理，以及其他 Python 作業，例如：

- 自動管理虛擬環境
- 使用 PIP 安裝列在 requirements.txt 中的封裝
- 根據選取的 Python 版本建立適當的 web.config。
- 收集適用於 Django 應用程式的靜態檔案

您可以控制某些方面的預設部署步驟，而不需要自訂指令碼。

如果您想要略過所有 Python 專屬的部署步驟，可以建立此空白檔案：

    .skipPythonDeployment

如果您想要跳過 Django 應用程式的靜態檔案收集：

    .skipDjango 

若要更能掌控部署，您可以建立以下檔案來覆寫預設的部署指令碼：

    .deployment
    \deploy.cmd

您可以使用 [Azure 命令列介面][]建立檔案。從您的專案資料夾中使用此命令：

    azure site deploymentscript --python

當這些檔案不存在時，Azure 會建立一個暫存的部署指令碼，並執行該指令碼。它與您使用上述命令建立的指令碼完全相同。

[Azure 命令列介面]: http://azure.microsoft.com/downloads/

<!---HONumber=62-->
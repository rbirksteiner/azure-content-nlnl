在 Azure 上執行時，有些封裝可能無法使用 PIP 安裝。這可能只是因為在 Python 封裝索引上無法使用該封裝。這可能是需要有編譯器 (在 Azure App Service 中執行 Web 應用程式的電腦上無法使用編譯器)。

在本節中，我們將探討如何處理這個問題。

### 要求 Wheel

如果封裝安裝需要編譯器，您應該嘗試連絡封裝擁有者，要求提供適用於封裝的 Wheel。

透過最近上市的 [Microsoft Visual C++ Compiler for Python 2.7][]，現在建置擁有 Python 2.7 原生程式碼的封裝更容易。

### 建置 Wheel (需要 Windows)

注意：使用此選項時，請務必使用符合 Azure App Service 中 Web 應用程式上使用之平台/架構/版本 (Windows/32 位元/2.7 或 3.4) 的 Python 環境編譯封裝。

如果封裝因為需要編譯器而無法安裝，您可以在本機電腦上安裝編譯器，並建置封裝的 Wheel，然後將其包含在您的儲存機制中。

Mac/Linux 使用者：如果您無法存取 Windows 電腦，請參閱[建立執行 Windows 的虛擬機器][]，了解如何在 Azure 上建立虛擬機器。您可以使用該虛擬機器建置 Wheel、將它們加入至儲存機制，以及在您想要捨棄虛擬機器時捨棄。

對於 Python 2.7，您可以安裝 [Microsoft Visual C++ Compiler for Python 2.7][]。

對於 Python 3.4，您可以安裝 [Microsoft Visual C++ 2010 Express][]。

若要建置 Wheel，您需要有 Wheel 封裝：

    env\scripts\pip install wheel

您將使用 `pip wheel` 編譯相依性：

    env\scripts\pip wheel azure==0.8.4

這會在 \wheelhouse 資料夾中建立一個 .whl 檔。將 \wheelhouse 資料夾與 Wheel 檔案加入至您的儲存機制。

編輯 requirements.txt 以便在頂端加入 `--find-links` 選項。這會告訴 PIP 尋找本機資料夾中完全相符的項目，才能進入 Python 封裝索引。

    --find-links wheelhouse
    azure==0.8.4

如果您想要在 \wheelhouse 資料夾中包含所有相依性，而且完全不使用 Python 封裝索引，您可以將 `--no-index` 加入至 requirements.txt 的頂端，以強制 PIP 忽略封裝索引。

    --no-index

### 自訂安裝

您可以自訂部署指令碼，以使用替代的安裝程式 (例如 easy_install) 在虛擬環境中安裝封裝。如需已標成註解的範例，請參閱 deploy.cmd。請確定這類封裝未列在 requirements.txt 中，以避免 PIP 安裝這類封裝。

將以下加入至部署指令碼：

    env\scripts\easy_install somepackage

您也可以使用 easy_install，從 exe 安裝程式 (某些與 zip 相容，因此 easy_install 支援它們) 安裝。將安裝程式加入至您的儲存機制，並傳遞可執行檔的路徑以叫用 easy_install。

將以下加入至部署指令碼：

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### 在儲存機制中包含虛擬環境 (需要有 Windows)

注意：使用此選項時，請務必使用符合 Azure App Service 中 Web 應用程式上使用之平台/架構/版本 (Windows/32 位元/2.7 或 3.4) 的虛擬環境。

如果您在儲存機制中包含虛擬環境，可以防止部署指令碼透過建立空白檔案，在 Azure 上管理虛擬環境：

    .skipPythonDeployment

建議您刪除應用程式上現有的虛擬環境，以防止在自動管理虛擬環境時遺留檔案。


[建立執行 Windows 的虛擬機器]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/
[Microsoft Visual C++ Compiler for Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949

<!---HONumber=62-->
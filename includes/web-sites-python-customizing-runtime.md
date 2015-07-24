Azure 將會使用下列優先順序，決定要用於其虛擬環境的 Python 版本：

1. 在根資料夾的 runtime.txt 中指定的版本
1. 在 Web 應用程式組態 (在 Azure 入口網站中您 Web 應用程式的 [設定] > [應用程式設定] 刀鋒視窗) 中，由 Python 設定所指定的版本
1. 如果未指定上述任何版本，python 2.7 為預設值

內容的有效值

    \runtime.txt

包括：

- python-2.7
- python-3.4

如果指定微版本 (第三個數字)，則會忽略它。

<!---HONumber=62-->
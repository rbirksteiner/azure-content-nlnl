<properties 
   pageTitle="Azure 自動化 Runbook 概念"
   description="描述在 Azure 自動化中建立 Runbook 您應該了解的基本概念。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure 自動化 Runbook 概念

Azure 自動化中的 Runbook 會實作為 Windows PowerShell 工作流程。本節提供自動化 Runbook 常見工作流程的重要功能的簡短概觀。工作流程的完整資訊在此取得：[開始使用 Windows PowerShell 工作流程](http://technet.microsoft.com/library/jj134242.aspx)。


## Windows PowerShell 工作流程

工作流程是一連串的程式化、連接步驟，執行長時間執行的工作，或是需要跨多個裝置或受管理節點協調多個步驟。透過標準的指令碼工作流程的好處包括能夠同時對多個裝置執行動作，以及可自動從失敗復原的能力。Windows PowerShell 工作流程是運用 Windows Workflow Foundation 的 Windows PowerShell 指令碼。雖然工作流程是使用 Windows PowerShell 語法編寫，並由 Windows PowerShell 啟動，它是由 Windows Workflow Foundation 來處理。

### 基本結構

Windows PowerShell 工作流程是以 **Workflow** 關鍵字為開頭，後面接著括在大括弧中的指令碼主體。工作流程的名稱會遵循 **Workflow** 關鍵字，如下列語法所示。工作流程的名稱會符合自動化 Runbook 的名稱。

    Workflow Test-Runbook
    {
       <Commands>
    }

若要將參數加入至工作流程，請使用 **Param** 關鍵字，如下列語法所示。使用者啟動 Runbook 時，管理入口網站會提示使用者提供這些參數的值。這個範例使用選擇性的 Parameter 屬性，指定參數是否為必要參數。

    Workflow Test-Runbook
    {
      Param
      (
       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>,

       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>
      )
      <Commands>
    }

### 命名

工作流程的名稱應該符合 Windows PowerShell 的標準「動詞-名詞」格式。您可以參考 [Windows PowerShell 命令核准的動詞](http://msdn.microsoft.com/library/windows/desktop/ms714428(v=vs.85).aspx)，以取得可使用的已核准動詞清單。工作流程的名稱必須符合自動化 Runbook 的名稱。如果正在匯入 Runbook，檔案名稱必須符合工作流程名稱，並必須以 .ps1 結尾。

### 限制

如需限制和 Windows PowerShell 工作流程和 Windows PowerShell 之間的語法差異的完整清單，請參閱[指令碼工作流程和指令碼之間的語法差異](http://technet.microsoft.com/library/jj574140.aspx)。

## 活動

活動是工作流程中的特定工作。就像指令碼是由一或多個命令所組成，工作流程是由序列中執行的一或多個活動所組成。執行工作流程時，Windows PowerShell 工作流程會自動將許多 Windows PowerShell Cmdlet 轉換為活動。在 Runbook 中指定其中一個 Cmdlet 時，對應的活動實際上是由 Windows Workflow Foundation 執行。針對沒有對應活動的 Cmdlet，Windows PowerShell 工作流程會自動在 [InlineScript](#inlinescript) 活動內執行 Cmdlet。有一組 Cmdlet 被排除，除非您明確在 InlineScript 區塊中將其納入，否則無法用在工作流程中。如需這些概念的詳細資訊，請參閱[在指令碼工作流程中使用活動](http://technet.microsoft.com/library/jj574194.aspx)。

工作流程活動共用一組通用參數來設定其作業。如需有關工作流程通用參數的詳細資訊，請參閱[about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)。

## 整合模組

*整合模組*是封裝，其中包含 Windows PowerShell 模組，而且可以匯入 Azure 自動化。匯入至 Azure 自動化的整合模組中的 Cmdlet 會自動提供給同一個自動化帳戶中的所有 Runbook。由於 Azure 自動化以 Windows PowerShell 4.0 為基礎，它可支援自動載入模組，也就是說，利用 [Import-module](http://technet.microsoft.com/library/hh849725.aspx)，來自已安裝模組的 Cmdlet 不需匯入指令碼中即可供使用。

## 平行執行

Windows PowerShell 工作流程的優點之一是可平行執行一組命令，而不是如同一般的指令碼以循序方式執行。這在 Runbook 中特別有用，因為它們可以執行需要大量時間才能完成的多個動作。例如，Runbook 可能佈建一組虛擬機器。不需要一個接著一個循序執行每個佈建程序，可以同時執行動作，增加整體的效率。只有所有動作都完成時，Runbook 才會繼續。

您可以使用 **Parallel** 關鍵字來建立具有多個同時執行的命令的指令碼區塊。這會使用如下所示的語法。在此情況下，Activity1 和 Activity2 將同時開始。只有在 Activity1 和 Activity2 都已完成之後，Activity3 才會開始。

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

您可以使用 **ForEach-Parallel** 建構來並行處理集合中每個項目的命令。會以平行方式處理集合中的項目，而循序執行指令碼區塊中的命令。這會使用如下所示的語法。在此情況下，Activity1 將與集合中的所有項目同時開始。針對每個項目，Activity2 會在 Activity1 完成之後開始 。只有在 Activity1 和 Activity2 已完成所有項目之後，Activity3 才會開始。

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

**Sequence** 關鍵字用來在 **Parallel** 指令碼區塊內循序執行命令。**Sequence** 指令碼區塊會以平行與其他命令執行，但在區塊內的命令則循序執行。這會使用如下所示的語法。在此情況下，Activity1、Activity2 和 Activity3 將同時開始。只有在 Activity3 完成之後，Activity4 才會開始。所有其他活動完成之後，Activity5 就會開始。

    Parallel
    {
      <Activity1>
      <Activity2>

      Sequence 
      { 
        <Activity3>  
        <Activity4>
      }

    }
    <Activity5>

## 檢查點

*檢查點*是包含變數的目前值和在該點產生的任何輸出的工作流程的目前狀態的快照。在 Runbook 中完成的最後一個檢查點會儲存到自動化資料庫，以便在執行階段發生電腦中斷這類問題時，可以繼續工作流程。沒有檢查點，工作流程會從頭開始。Runbook 工作完成後，會移除檢查點資料。

您可以使用 **Checkpoint-Workflow** 活動來設定工作流程中的檢查點。當您在 Runbook 中包含這個活動時，會立即取得檢查點。如果在繼續工作時 Runbook 被例外狀況暫止，會從設定的最後一個檢查點繼續。

在下列範例程式碼中，Activity2 之後發生的例外狀況造成 Runbook 暫止。工作繼續時，它會先執行 Activity2，因為這是緊接在設定的最後一個檢查點之後。

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

在活動可能容易發生例外狀況，且不應在 Runbook 繼續執行之後重複執行時，您應該在 Runbook 中設定檢查點。例如，您的 Runbook 可能會建立虛擬機器。您可以在建立虛擬機器命令的前面和後面設定檢查點。如果建立失敗，則在 Runbook 繼續時會重複命令。如果建立成功而 Runbook 稍後失敗，則在 Runbook 繼續時將不會重新建立虛擬機器。

如需有關檢查點的詳細資訊，請參閱 [加入檢查點至指令碼工作流程](http://technet.microsoft.com/library/jj574114.aspx)。

## 暫止工作流程

您可以使用 **Suspend-Workflow** 活動來強制暫止 Runbook 本身。這個活動會設定檢查點，並導致立即暫止工作流程。暫止工作流程對於可能需要執行手動步驟，才能執行另一組活動的 Runbook 很實用。

如需有關暫止工作流程的詳細資訊，請參閱[使工作流程本身暫止](http://technet.microsoft.com/library/jj574175.aspx)。

## InlineScript

**InlineScript** 活動在傳統的 PowerShell 指令碼，而不是 PowerShell 工作流程中執行一個區塊的命令，並將其輸出傳回至工作流程。在工作流程中的命令會傳送至 Windows Workflow Foundation 進行處理，而 Windows PowerShell 會處理 InlineScript 區塊中的命令。活動會使用標準的工作流程通用參數，包括 **PSCredential** 可讓您指定使用替代認證執行的程式碼區塊。

InlineScript 使用如下所示的語法。

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

雖然 InlineScript 活動在特定 Runbook 中可能是關鍵，它們不支援工作流程建構，而且應該基於以下原因時使用：

- 您無法從 InlineScript 區塊內使用檢查點。如果失敗發生在區塊內，它必須從區塊的開頭繼續。
- InlineScript 會影響 Runbook 的延展性，因為它會保留 InlineScript 區塊的整個長度的 Windows PowerShell 工作階段。
- InlineScript 區塊中無法使用 Get-AutomationVariable 和 Get-AutomationPSCredential 之類的活動。  

如果您需要使用 InlineScript，您應該盡可能降低其範圍。例如，如果您的 Runbook 會在套用相同的作業至每個項目時逐一查看集合，迴圈應該發生在 InlineScript 外部。這會提供下列優點：

- 您可以在每個反覆項目之後設定工作流程的[檢查點](#checkpoints)。如果工作被暫止或中斷並繼續，迴圈將能夠繼續。
- 您可以使用 **ForEach – Parallel** 來並行處理集合項目。

如果在您的 Runbook 中使用 InlineScript，請記住下列建議：

- 您可以透過使用 **$Using** 範圍修飾子將值傳遞到指令碼。例如，設定在 InlineScript 外部稱為 $abc 的的變數在 InlineScript 內就會變成 $using:abc。

- 若要從 InlineScript 傳回輸出，請將輸出指派給變數，並輸出要傳回到輸出資料流的任何資料。下列範例會將字串「hi」指派給稱為 $output 的變數。

	<pre><code>$output = InlineScript { Write-Output "hi" }</code></pre>

- 應避免在 InlineScript 範圍內定義工作流程。即使某些工作流程看起來正常運作，這不是經測試的案例。因此，您可能會遇到令人混淆的錯誤訊息或非預期的行為。

如需使用 InlineScript 的進一步詳細資訊，請參閱[在工作流程中執行 Windows PowerShell 命令](http://technet.microsoft.com/library/jj574197.aspx)和[about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)。


## 相關文章

- [建立或匯入 Runbook](http://technet.microsoft.com/library/dn919921.aspx) 

<!---HONumber=62-->
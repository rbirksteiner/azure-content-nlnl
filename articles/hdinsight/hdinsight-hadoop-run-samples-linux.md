<properties
	pageTitle="在 HDInsight 上執行 Hadoop 範例 | Microsoft Azure"
	description="開始搭配以 Linux 為基礎的 HDInsight 使用 MapReduce 範例。使用 SSH 連接到叢集，然後使用 Hadoop 命令執行範例工作。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/25/2015"
	ms.author="larryfr"/>




#在 HDInsight 上執行 Hadoop 範例

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

以 Linux 為基礎的 HDInsight 叢集提供一組 MapReduce 範例，讓您可以用來了解如何執行 Hadoop MapReduce 工作。在本文件中，您將了解可用的範例，並逐步執行幾個範例。

##必要條件

- **Azure 訂用帳戶**：請參閱[取得 Azure 免費試用](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **以 Linux 為基礎的 HDInsight 叢集**：請參閱[在 Linux 上開始在 HDInsight 中搭配使用 Hadoop 與 Hive](hdinsight-hadoop-linux-tutorial-get-started.md)

- **SSH 用戶端**：如需搭配 HDInsight 使用 SSH 的資訊，請參閱下列文章：

    - [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

## 範例 ##

**位置**：範例位於 HDInsight 叢集上的 **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar** 中

**內容**：下列範例都包含在此封存中：

- **aggregatewordcount**：以彙總為基礎的 map/reduce 程式，可計算輸入檔中的字數
- **aggregatewordhist**：以彙總為基礎的 map/reduce 程式，可計算輸入檔中的字數長條圖
- **bbp**：map/reduce 程式，使用貝利-波爾溫-普勞夫公式 (Bailey-Borwein-Plouffe) 計算 Pi 的確切位數
- **dbcount**：範例工作，可從資料庫計算 pageview 的計數
- **distbbp**：map/reduce 程式，使用 BBP 類型的公式來計算 Pi 的確切位數
- **grep**：map/reduce 程式，可計算輸入中 regex 的相符項目
- **join**：工作，可影響已排序且平均分割之資料集的聯結
- **multifilewc**：可從數個檔案計算字數的工作
- **pentomino**：map/reduce 磚配置程式，可尋找五格骨牌 (pentomino) 問題的解答
- **pi**：map/reduce 程式，使用擬蒙特卡羅 (quasi-Monte Carlo) 方法估計 Pi
- **randomtextwriter**：map/reduce 程式，可為每個節點寫入 10 GB 的隨機文字資料
- **randomwriter**：map/reduce 程式，可為每個節點寫入 10 GB 的隨機資料
- **secondarysort**：範例，定義要簡化的二次排序
- **sort**：map/reduce 程式，可排序隨機寫入器所寫入的資料
- **sudoku**：數獨解答程式
- **teragen**：產生用於 TeraSort 的資料
- **terasort**：執行 TeraSort
- **teravalidate**：檢查 TeraSort 的結果
- **wordcount**：map/reduce 程式，可計算輸入檔中的字數
- **wordmean**：map/reduce 程式，可計算輸入檔中字詞的平均長度
- **wordmedian**：map/reduce 程式，可計算輸入檔中字詞的中位數長度
- **wordstandarddeviation**：map/reduce 程式，可計算輸入檔中字詞長度的標準差

**原始程式碼**：HDInsight 叢集上的 **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples** 中含有這些範例的原始程式碼

> [AZURE.NOTE]路徑中的 `2.2.4.9-1` 是 HDInsight 叢集所使用的 Hortonworks Data Platform 版本，且可能會隨著 HDInsight 的更新而變更。

## 如何執行範例 ##

1. 如下列文章所述，使用 SSH 連接至 HDInsight：

    - [從 Linux、Unix 或 OS X 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [從 Windows 在 HDInsight 上搭配使用 SSH 與以 Linux 為基礎的 Hadoop](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 在 `username@headnode0:~$` 提示中，使用下列命令來列出範例：

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    這麼做會產生本文件上一節中的範例清單。

3. 使用下列命令可取得特定範例的說明。在此情況下為 **wordcount** 範例：

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    您應該會看見下列訊息：

        Usage: wordcount <in> [<in>...] <out>

    這表示您可以提供數個來源文件的輸入路徑，而最後一個路徑就是要放置輸出 (來源文件中的字數計數) 的位置。

4. 使用以下命令計算達文西手稿筆記中的所有字數，該文件已隨附於您的叢集做為範例資料：

    	hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    系統會從 **wasb:///example/data/gutenberg/davinci.txt** 讀取這項工作的輸入。

    此範例的輸出會儲存在 **wasb:///example/data/davinciwordcount**。

    > [AZURE.NOTE]如 wordcount 範例的說明所述，您也可以指定多個輸入檔。例如，`hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` 會計算 davinci.txt 和 ulysses.txt 中的字數。

5. 工作完成後，使用以下命令來檢視輸出：

        hadoop fs -cat /example/data/davinciwordcount/*

    這麼做會串連工作所產生的所有輸出檔，並加以顯示。此為基本範例，只有一個檔案，但如果有多個檔案，此命令就會逐一計算所有檔案。

    您將透過此命令看到類似以下的輸出：

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    每一行代表一個字詞，以及該字詞在輸入資料中出現的次數。

## 數獨

Sudoku 範例的使用指示似乎有點不實用：「在命令列中包含謎題 (Include a puzzle on the command line)」。

[數獨](https://en.wikipedia.org/wiki/Sudoku)是由九個 3x3 的方格所組成的邏輯謎題。方格中的一些格子含有數字，而有些格子則為空白，因此目標就是解出空白格子的數字。上述連結中提供謎題的詳細資訊，而此範例的目的是要解出空白格子的數字。所以我們的輸入應該是具有下列格式的檔案：

- 具有九個資料列的九個資料行

- 每個資料行可以包含一個數字或 `?` (表示空白的格子)

- 格子之間以空格分隔

現在，必須以特定的方法建立數獨謎題，那就是您不能在某個資料行或資料列中使用重複的數字。幸好 HDInsight 叢集上已經有一個正確建立的範例。它位於 **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** 且包含下列內容：

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE]路徑的 `2.2.4.9-1` 部分可能會隨著 HDInsight 叢集的更新而變更。

若要在 Sudoku 範例中執行此範例，請使用以下命令：

    hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

結果應該會類似如下：

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## Pi (π)

Pi 範例會使用統計 (擬蒙特卡羅法) 方法來估計 pi 的值。單位正方形內隨機散佈的點，也會落在該正方形的內切圓之內，且機率等於圓面積 Pi/4。Pi 的值可從 4R 的值來估計，其中 R 是圓內點數佔正方形內總點數的比例。使用的樣本點越多，估計越準確。

此範例的對應器會在單位正方形內隨機產生一些散佈的點，然後計算落在圓內的點數。

接著歸納器會累計對應器所計算的點數，並從公式 4R 估計 Pi 的值，其中 R 是圓內計算的點數佔正方形內總點數的比例。

使用以下命令來執行此範例。這會每次使用 16 個對應搭配 10,000,000 個取樣來估計 Pi 的值：

    hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

此命令傳回的值應該會類似於 **3.14159155000000000000**。Pi 的前 10 個小數位數是 3.1415926535，供您參考。

##10 GB Greysort

GraySort 是一種效能評定排序，其度量為排序極大資料量時 (通常至少為 100TB) 所達成的排序速率 (TB/分鐘)。

本範例使用不太大的 10GB 資料，所以執行起來相對較快。本範例使用 Owen O'Malley 和 Arun Murthy 所開發的 MapReduce 應用程式，此應用程式於 2009 年的年度一般目的 (「耐力賽」) TB 排序效能評定中，以 0.578TB/分鐘 (173 分鐘內達到 100TB) 的速率獲勝。如需此效能評比和其他排序效能評比的詳細資訊，請參閱 [Sortbenchmark](http://sortbenchmark.org/) 網站。

本範例使用三組 MapReduce 程式：

- **TeraGen**：MapReduce 程式，產生要排序的資料列

- **TeraSort**：可取樣輸入資料，並利用 MapReduce 將資料排列為全序

    TeraSort 是 MapReduce 函數的標準排序，但自訂分割器除外，它使用 N-1 個樣本索引鍵的排序清單來定義每次歸納的索引鍵範圍。尤其是，會傳送使得 sample[i-1] <= key < sample[i] 的所有索引鍵給歸納 i。這保證歸納 i 的輸出全都小於歸納 i+1 的輸出。

- **TeraValidate**：可驗證全域排序輸出的 MapReduce 程式

    它會在輸出目錄中為每一個檔案建立一個對應，而每個對應可確保每一個索引鍵一定小於或等於前一個對應。對應函數也會產生每個檔案的第一個和最後一個索引鍵的記錄，而歸納函數可確保檔案 i 的第一個索引鍵大於檔案 i-1 的最後一個索引鍵。任何問題皆會回報為具錯誤索引鍵的歸納輸出。

使用下列步驟來產生資料、排序，並驗證輸出：

1. 產生 10 GB 的資料，這些資料稍後會儲存在 HDInsight 叢集上預設儲存體的 **wasb:///example/data/10GB-sort-input** 中：
        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

	`-Dmapred.map.tasks` 會告訴 Hadoop 在這項工作中要使用多少 map 工作。最後兩個參數會指示工作建立 10 GB 的資料量，並將資料儲存在 **wasb:///example/data/10GB-sort-input**。

2. 使用以下命令來排序資料：

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

	`-Dmapred.reduce.tasks` 會告訴 Hadoop 在這項工作中要使用多少 reduce 工作。最後兩個參數只是資料的輸入和輸出位置。

3. 使用以下命令驗證依排序產生的資料：

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##後續步驟 ##

您已在本文中學到如何執行以 Linux 為基礎的 HDInsight 叢集所隨附的範例。如需透過 HDInsight 使用 Pig、Hive 和 MapReduce 的教學課程，請參閱下列主題：

* [搭配使用 Pig 與 HDInsight 上的 Hadoop][hdinsight-use-pig]
* [搭配使用 Hive 與 HDInsight 上的 Hadoop][hdinsight-use-hive]
* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop][hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=July15_HO2-->
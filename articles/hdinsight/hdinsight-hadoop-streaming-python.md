<properties
   pageTitle="使用 HDInsight 開發 Python MapReduce 工作 | Microsoft Aure"
   description="了解如何在以 Linux 為基礎的 HDInsight 叢集上建立和執行 Python MapReduce 工作。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/19/2015"
   ms.author="larryfr"/>

#開發適用於 HDInsight 的 Python 串流程式

Hadoop 為 MapReduce 提供一個串流 API，可讓您以 Java 以外的語言撰寫 map 和 reduce 函數。在本文中，您將學習如何使用 Python 來執行 MapReduce 作業。

> [AZURE.NOTE]本文是根據 Michael Noll 在 [http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/] (以 Python 撰寫 Hadoop MapReduce 程式) 所發佈的資訊和範例所撰寫而成。

##必要條件

若要完成本文中的步驟，您需要下列項目：

* HDInsight 叢集上的 Linux 型 Hadoop

* 文字編輯器

* 若為 Windows 用戶端，則需要 PuTTY 和 PSCP。這些公用程式可從 <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY 下載頁面</a>取得。

##字數統計

在此範例中，您將使用對應器和歸納器實作基本字數計數。對應器會將句子拆解成個別文字，歸納器則會彙總文字和計數來產生輸出。

下列流程圖說明在對應和歸納階段期間所執行的程序。

![Map Reduce 的插圖](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##為何使用 Python？

Python 是一般用途的高階程式設計語言，可讓您以少於許多其他語言的程式碼行數快速表達概念。資料科學家最近很喜歡用它做為原型語言，因為它的解譯本質、動態型別和簡潔語法讓它非常適合用於快速開發應用程式。

Python 會安裝在所有 HDInsight 叢集上。

##串流 MapReduce

Hadoop 可讓您指定包含工作所使用的對應和歸納邏輯的檔案。對應和歸納邏輯的特殊需求如下：

* **輸入**：對應和歸納元件必須從 STDIN 讀取輸入資料。

* **輸出**：對應和歸納元件必須將輸出資料寫入至 STDOUT。

* **資料格式**：所取用和產生的資料必須是機碼/值組，並以 Tab 字元隔開。

Python 可以使用 **sys** 模組從 STDIN 讀取資料並使用 **print** 來列印到 STDOUT，輕鬆應付這些需求。剩下的工作便只需要在機碼和值之間以 Tab (`\t`) 字元格式化資料。

##建立對應器和歸納器

對應器和歸納器是文字檔案，在此案例中為 **mapper.py** 和 **reducer.py** (以便清楚知道各自的功用)。您可以使用自行選擇的編輯器建立這些檔案。

###Mapper.py

建立名為 **mapper.py** 的新檔案並使用下列程式碼做為內容：

	#!/usr/bin/env python

	# Use the sys module
	import sys

	# 'file' in this case is STDIN
	def read_input(file):
		# Split each line into words
		for line in file:
			yield line.split()

	def main(separator='\t'):
		# Read the data using read_input
		data = read_input(sys.stdin)
		# Process each words returned from read_input
		for words in data:
			# Process each word
			for word in words:
				# Write to STDOUT
				print '%s%s%d' % (word, separator, 1)

	if __name__ == "__main__":
		main()

請花一點時間仔細閱讀程式碼，以便了解它的功用。

###Reducer.py

建立名為 **reducer.py** 的新檔案並使用下列程式碼做為內容：

	#!/usr/bin/env python
	
	# import modules
	from itertools import groupby
	from operator import itemgetter
	import sys
	
	# 'file' in this case is STDIN
	def read_mapper_output(file, separator='\t'):
		# Go through each line
	    for line in file:
			# Strip out the separator character
	        yield line.rstrip().split(separator, 1)
	
	def main(separator='\t'):
	    # Read the data using read_mapper_output
	    data = read_mapper_output(sys.stdin, separator=separator)
		# Group words and counts into 'group'
		#   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
	    for current_word, group in groupby(data, itemgetter(0)):
	        try:
				# For each word, pull the count(s) for the word
				#   from 'group' and create a total count
	            total_count = sum(int(count) for current_word, count in group)
				# Write to stdout
	            print "%s%s%d" % (current_word, separator, total_count)
	        except ValueError:
	            # Count was not a number, so do nothing
	            pass
	
	if __name__ == "__main__":
	    main()

##上傳檔案

**mapper.py** 和 **reducer.py** 必須同時位於叢集的前端節點上才能加以執行。最簡單的上傳方式是使用 **scp** (如果您使用 Windows 用戶端則是 **pscp**)。

從用戶端上與 **mapper.py** 和 **reducer.py** 相同的目錄中，使用下列命令。將 **username** 取代為 SSH 使用者，並將 **clustername** 取代為叢集名稱。

	scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

這樣就會將檔案從本機系統複製到前端節點。

> [AZURE.NOTE]如果您使用密碼保護 SSH 帳戶，系統會提示您輸入密碼。如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑，例如 `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`。

##執行 MapReduce

1. 使用 SSH 連線到叢集：

		ssh username@clustername-ssh.azurehdinsight.net

	> [AZURE.NOTE]如果您使用密碼保護 SSH 帳戶，系統會提示您輸入密碼。如果您使用 SSH 金鑰，您可能必須使用 `-i` 參數和私密金鑰的路徑，例如 `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`。

2. 使用下列命令啟動 MapReduce 工作。

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasb:///example/data/davinci.txt -output wasb:///example/wordcountout

	此命令有下列幾個部分：

	* **hadoop-streaming.jar**：執行串流 MapReduce 作業時使用。它能連結 Hadoop 和您提供的外部 MapReduce 程式碼。
	
	* **-files**：告訴 Hadoop 此 MapReduce 工作需要指定的檔案，而且應複製到所有背景工作節點。

	* **-mapper**：告訴 Hadoop 要做為對應器的檔案。

	* **-reducer**：告訴 Hadoop 要做為歸納器的檔案。

	* **-input**：要從中計算字數的輸入檔案。

	* **-output**：要將輸出寫入到的目錄。

		> [AZURE.NOTE]該工作會建立此目錄。

工作啟動後您會看到一堆 **INFO** 陳述式，最後便會看到以百分比顯示的 **map** 和 **reduce** 作業。

	15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
	15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
	15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

工作完成後，您會收到工作的狀態資訊。

##檢視輸出

工作完成後，使用以下命令檢視輸出：

	hadoop fs -text /example/wordcountout/part-00000

這應該會顯示文字及文字出現次數的清單。以下是輸出資料的範例：

	wrenching       1
	wretched        6
	wriggling       1
	wrinkled,       1
	wrinkles        2
	wrinkling       2

##後續步驟

現在您已學會如何搭配 HDInsight 使用串流 MapRedcue 工作，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [搭配 HDInsight 使用 Hivet](hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Pig](hdinsight-use-pig.md)
* [搭配 HDInsight 使用 MapReduce 工作](hdinsight-use-mapreduce.md)
 

<!---HONumber=July15_HO2-->
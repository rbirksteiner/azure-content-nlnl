<properties
   pageTitle="利用 HDInsight 中的 Hive 分析和處理 JSON 文件 |Microsoft Azure"
   description="了解如何使用 JSON 文件，以集使用 HDInsight 中的 Hive 分析它們。"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# 使用 HDInsight 中的 Hive 處理並分析 JSON 文件

了解如何使用 HDInsight 中的 Hive 處理並分析 JSON 文件。本教學課程中將使用下列 JSON 文件

	{
	    "StudentId": "trgfg-5454-fdfdg-4346",
	    "Grade": 7,
	    "StudentDetails": [
	        {
	            "FirstName": "Peggy",
	            "LastName": "Williams",
	            "YearJoined": 2012
	        }
	    ],
	    "StudentClassCollection": [
	        {
	            "ClassId": "89084343",
	            "ClassParticipation": "Satisfied",
	            "ClassParticipationRank": "High",
	            "Score": 93,
	            "PerformedActivity": false
	        },
	        {
	            "ClassId": "78547522",
	            "ClassParticipation": "NotSatisfied",
	            "ClassParticipationRank": "None",
	            "Score": 74,
	            "PerformedActivity": false
	        },
	        {
	            "ClassId": "78675563",
	            "ClassParticipation": "Satisfied",
	            "ClassParticipationRank": "Low",
	            "Score": 83,
	            "PerformedActivity": true
	        }
	    ]
	}

檔案位於 wasb://processjson@hditutorialdata.blob.core.windows.net/。如需關於搭配 HDInsight 使用 Azure Blob 儲存體的詳細資訊，請參閱[在 HDInsight 上搭配 Hadoop 使用 HDFS 相容的 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。如果您想要的話，可以將檔案複製到叢集的預設容器。

在本教學課程中，您將使用 Hive 主控台。如需開啟 Hive 主控台的指示，請參閱[利用遠端桌面搭配使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-hadoop-use-hive-remote-desktop.md)。

##簡維 JSON 文件

下一節所列的方法需要 JSON 文件在單一資料列中。因此，您必須將 JSON 文件簡維成一個字串。如果已簡維 JSON 文件，您就可以略過此步驟，直接進入與分析 JSON 資料相關的下一節。

	DROP TABLE IF EXISTS StudentsRaw;
	CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";
	
	DROP TABLE IF EXISTS StudentsOneLine;
	CREATE EXTERNAL TABLE StudentsOneLine
	(
	  json_body string
	)
	STORED AS TEXTFILE LOCATION '/json/students';
	
	INSERT OVERWRITE TABLE StudentsOneLine
	SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON 
	      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
	      GROUP BY INPUT__FILE__NAME;
	
	SELECT * FROM StudentsOneLine

原始 JSON 檔案位於 **wasb://processjson@hditutorialdata.blob.core.windows.net/**。*StudentsRaw* Hive 資料表指向原始未簡維的 JSON 文件。

*StudentsOneLine* Hive 資料表會將資料儲存在 HDInsight 預設檔案系統的 */json/students/* 路徑下。

INSERT 陳述式會簡維後的 JSON 資料填入 StudentOneLine 資料表。

SELECT 陳述式應該只會傳回 1 個資料列。

以下是 SELECT 陳述式的輸出：

![JSON 文件簡維。][image-hdi-hivejson-flatten]

##在 Hive 中分析 JSON 文件

Hive 提供三種不同的機制，可在 JSON 文件上執行查詢：

- 使用 GET_JSON_OBJECT UDF (使用者定義函數)
- 使用 JSON_TUPLE UDF
- 使用自訂 SerDe
- 使用 Python 或其他語言撰寫您自己的 UDF。請參閱[這篇文章][hdinsight-python]，了解如何使用 Hive 執行您自己的 Python 程式碼。 

### 使用 GET_JSON_OBJECT UDF
Hive 提供內建的 UDF，稱為 [get json objec](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)，可在執行階段期間執行 JSON 查詢。此方法採用兩個引數 – 資料表名稱和方法名稱，後者具有簡維 JSON 文件和必須剖析的 JSON 欄位。讓我們看看此 UDF 如何運作的範例。

取得每個學生的姓氏與名字

	SELECT 
	  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'), 
	  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName') 
	FROM StudentsOneLine;

以下是在主控台視窗中執行此查詢時的輸出。

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

get-json_object UDF 有幾項限制。

- 因為查詢中的每個欄位都需要重新剖析查詢，所以它會影響效能。
- GET_JSON_OBJECT() 會傳回陣列的字串表示法。若要將其轉換成 Hive 陣列，您必須使用規則運算式來取代方括號 ‘[‘ 和 ‘]’，然後呼叫分割以取得陣列。


這就是 Hive wiki 建議使用 json_tuple 的原因。

### 使用 JSON_TUPLE UDF

Hive 所提供的另一個 UDF 稱為 [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple)，其效能比 [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object)) 更高。這個方法會採用一組索引鍵和 JSON 字串，並使用一個函式傳回值的 tuple。下列查詢會從 JSON 文件傳回學生識別碼以及年級：

    SELECT q1.StudentId, q1.Grade 
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

這個指令碼在 Hive 主控台的輸出：

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON_TUPLE 使用 Hive 中的[橫向檢視](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView)語法，可讓 json_tuple 將 UDT 函數套用到原始資料表的每個資料列，以建立一個虛擬資料表。複雜 JSON 會重複使用橫向檢視，因此變得難以使用。此外，JSON_TUPLE 無法處理巢狀 JSON。


###使用自訂 SerDe

SerDe 是用來剖析巢狀 JSON 文件的最佳選擇，它可讓您定義 JSON 結構描述，並使用該結構描述來剖析文件。在本教學課程中，您將使用其中一個已由 [rcongiu](https://github.com/rcongiu) 開發的熱門 SerDe。

**使用自訂 SerDe：**

1. 安裝 [Java SE 開發套件 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR)。如果您要使用 HDInsight 的 Windows 部署，請選擇 Windows x64 版本的 JDK。

	>[AZURE.WARNING]JDK 1.8 不適用於此 SerDe。

	安裝完成之後，請加入新的使用者環境變數：

	1. 從 Windows 畫面開啟 [檢視進階系統設定]。
	2. 按一下 [環境變數]。  
	3. 加入新的 **JAVA_HOME** 環境變數，其指向 **C:\\Program Files\\Java\\jdk1.7.0_55** 或任何安裝 JDK 的位置。

	![為 JDK 設定正確的組態值][image-hdi-hivejson-jdk]

2. 安裝 [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

	移至 [控制台] --> [編輯帳戶的系統變數]  環境變數，即可新增 bin 資料夾至您的路徑。以下螢幕擷取畫面顯示如何執行此動作。

	![設定 Maven][image-hdi-hivejson-maven]

3. 從 [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github 網站複製專案。您可以按一下 [下載 Zip] 按鈕即可完成，如以下螢幕擷取畫面所示。

	![複製專案][image-hdi-hivejson-serde]

4：移至您已將此封裝下載至其中的資料夾，並輸入 “mvn package”。這樣應該會建立您稍後會複製到叢集的必要 jar 檔案。

5：移至根資料夾 (您已將封裝下載至其中) 下的目標資料夾。上傳 json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar 檔案到叢集的前端節點。我通常會將它放在 hive 二進位資料夾底下：C:\\apps\\dist\\hive-0.13.0.2.1.11.0-2316\\bin 或類似位置。
 
6：在 hive 提示字元中，輸入 “add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar”。因為在我的案例中，jar 位於 C:\\apps\\dist\\hive-0.13.x\\bin 資料夾中，所以我可以直接以此名稱新增 jar，如下所示：

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

	![Adding JAR to your project][image-hdi-hivejson-addjar]

現在，您已準備好使用 SerDe 對 JSON 文件執行查詢。

下列陳述式會利用已定義的結構描述建立資料表

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

列出學生的姓氏與名字

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

以下是 Hive 主控台的結果。

![SerDe 查詢 1][image-hdi-hivejson-serde_query1]

計算 JSON 文件的分數總和

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;
       
上述查詢會使用[橫向檢視切割](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF 展開分數的陣列，讓它們可以加總。

以下是 Hive 主控台的輸出。

![SerDe 查詢 2][image-hdi-hivejson-serde_query2]

若要尋找特定學生分數超過 80 分的科目，請使用 SELECT jt.StudentClassCollection.ClassId FROM json_table jt lateral view explode(jt.StudentClassCollection.Score) collection as score where score > 80;
      
上述查詢傳回的 Hive 陣列與傳回字串的 get_json_object 不同。

![SerDe 查詢 3][image-hdi-hivejson-serde_query3]

如果您想要刪除格式不正確的 JSON，然後依照此 SerDe 之 [wiki 頁面](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) 的說明內容，您可以輸入下列程式碼即可達成：

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##摘要
總而言之，您在 Hive 中選擇的 JSON 運算子類型取決於您的案例。如果您有一個簡單的 JSON 文件，且您只需要查閱一個欄位 – 您就可以選擇使用 Hive UDF get_json_object。如果您有多個索引鍵需要查閱，則您可以使用 json_tuple。如果您有巢狀文件，您應該使用 JSON SerDe。

如需其他相關文章，請參閱

- [搭配 HDInsight 中的 Hadoop 使用 Hive 和 HiveQL 來分析範例 Apache Log4j 檔案](hdinsight-use-hive.md)
- [在 HDInsight 中使用 Hive 分析航班延誤資料](hdinsight-analyze-flight-delay-data.md)
- [在 HDInsight 中使用 Hive 分析 Twitter 資料](hdinsight-analyze-twitter-data.md)
- [使用 DocumentDB 和 HDInsight 執行 Hadoop 工作](documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
 

<!---HONumber=July15_HO2-->
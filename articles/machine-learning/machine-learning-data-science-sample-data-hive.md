<properties 
	pageTitle="在 Azure HDInsight Hive 資料表中進行資料取樣 | Azure" 
	description="在 Azure HDInsight Hive 資料表中進行資料向下取樣" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="hangzh;bradsev" />

# 在 Azure HDInsight Hive 資料表中進行資料取樣 

如果您規劃分析的資料集很大，通常最好是對資料進行向下取樣，將資料縮減為更小但具代表性且更容易管理的大小。這有助於資料了解、探索和功能工程。它在資料科學程序中扮演的角色是，能夠快速建立資料處理函式與機器學習服務模型的原型。

本文將說明如何使用 Hive 查詢，對 Azure HDInsight Hive 資料表中的資料進行向下取樣。我們將討論三個普遍使用的取樣方法：統一隨機取樣、依群組隨機取樣，以及分層取樣。

您應該從 Hadoop 叢集前端節點上的 Hadoop 命令列主控台提交 Hive 查詢。若要執行這個動作，請登入 Hadoop 叢集的前端節點、開啟 Hadoop 命令列主控台，然後從該處提交 Hive 查詢。如需在 Hadoop 命令列主控台中提交 Hive 查詢的相關指示，請參閱[如何提交 Hive 查詢](machine-learning-data-science-process-hive-tables.md#submit)。 

## <a name="uniform"></a> 統一隨機取樣 ##
統一隨機取樣表示資料集中的每個資料列都具有相等的取樣機率。這可藉由在內部 "select" 查詢中，以及在外部 "select" 查詢 (在該隨機欄位中設定條件) 中，將額外的欄位 rand() 新增至資料集中來實作。 

查詢範例如下：

	SET sampleRate=<sample rate, 0-1>;
	select
		field1, field2, ..., fieldN
	from
		(
		select
			field1, field2, ..., fieldN, rand() as samplekey 
		from <hive table name>
		)a
	where samplekey<='${hiveconf:sampleRate}'

在此處，`<取樣率, 0-1>` 會指定使用者想要取樣的記錄比例。 

## <a name="group"></a> 依群組隨機取樣 ##

對類別資料進行取樣時，您可能想要包含或排除類別變數中某些特殊值的所有執行個體。這就是「依群組取樣」的意思。
例如，如果您有一個類別變數 "State"，其擁有 NY、MA、CA、NJ、PA 等值，則您會想要讓相同狀態的記錄一律在一起，而不論是否要對它們進行取樣。 

以下是依群組取樣的查詢範例：

	SET sampleRate=<sample rate, 0-1>;
    select 
		b.field1, b.field2, ..., b.catfield, ..., b.fieldN
	from
		(
		select 
			field1, field2, ..., catfield, ..., fieldN
		from <table name>
		)b
	join
		(
		select
			catfield
		from
			(
			select 
				catfield, rand() as samplekey
			from <table name>
			group by catfield
			)a
		where samplekey<='${hiveconf:sampleRate}'
		)c
	on b.catfield=c.catfield

## <a name="stratified"></a>分層取樣

在類別變數方面，若取得的樣本具有該類別的值，而這些值的比率與從中取得樣本的母體相同，則隨機取樣就會進行分層。使用上述同一個範例，假設您的資料擁有依狀態分層的子母體，例如 NJ 具有 100 個觀察、NY 具有 60 個觀察，而 WA 具有 300 個觀察。如果您將分層取樣的比率指定為 0.5，則針對 NJ、NY 及 WA 所獲得的樣本分別應大約有 50、30 及 150 個觀察。

查詢範例如下：

	SET sampleRate=<sample rate, 0-1>;
    select 
		field1, field2, field3, ..., fieldN, state 
	from
		(
		select 
			field1, field2, field3, ..., fieldN, state,
			count(*) over (partition by state) as state_cnt, 
      		rank() over (partition by state order by rand()) as state_rank
      	from <table name>
		) a 
	where state_rank <= state_cnt*'${hiveconf:sampleRate}'


如需可在 Hive 中使用的更進階取樣方法相關資訊，請參閱 [LanguageManual 取樣](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)。

<!--HONumber=49--> 
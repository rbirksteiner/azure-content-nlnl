<properties
	pageTitle="透過 R 擴展您的經驗 | Microsoft Azure"
	description="如何利用「執行 R 指令碼」模組，透過 R 語言擴充 Azure Machine Learning Studio 的功能。"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="garye" />


#透過 R 擴展您的經驗 

您可以利用[執行 R 指令碼][execute-r-script]模組，透過 R 語言擴充 ML Studio 的功能。

此模組接受多個輸入資料集，並產生單一資料集作為輸出。您可以將 R 指令碼輸入至[執行 R 指令碼][execute-r-script]模組的 [**R 指令碼**] 參數。

您可使用類似下面的程式碼，存取模組的每個輸入連接埠：

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [電腦-學習-免費-試用](../../includes/machine-learning-free-trial.md)]

##列出所有目前安裝的封裝

可以變更已安裝的封裝清單。若要取得完整清單，請在[執行 R 指令碼][execute-r-script]模組中包含下列各行，以將清單傳送至輸出資料集：

    out <- data.frame(installed.packages())
    maml.mapOutputPort("out")

若要檢視封裝清單，請將轉換模組 (例如[轉換至 CSV][convert-to-csv]) 連接至[執行 R 指令碼][execute-r-script]模組的輸出，執行實驗，然後按一下轉換模組的輸出並選取 [**下載**]。

##匯入封裝

您也可以使用[執行 R 指令碼][execute-r-script]模組和已壓縮封裝封存檔中的下列命令，匯入尚未從分段 ML Studio 儲存機制安裝的封裝：

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

其中 `my_favorite_package.zip` 包含封裝的壓縮檔。

##已安裝的封裝清單

為了方便起見，下表提供目前版本中包含的封裝清單。

若要取得目前可用的完整封裝清單，請參閱上面**列出目前已安裝的所有封裝**一節。[這裡](http://cran.r-project.org/manuals.html)可取得目前的 R 文件。

- [開頭為 A 到 E 的 R 模組]
- [開頭為 F 到 L 的 R 模組]
- [開頭為 M 到 R 的 R 模組]
- [開頭為 S 到 Z 的 R 模組]

[開頭為 A 到 E 的 R 模組]: #r-modules-beginning-with-a-through-e
[開頭為 F 到 L 的 R 模組]: #r-modules-beginning-with-f-through-l
[開頭為 M 到 R 的 R 模組]: #r-modules-beginning-with-m-through-r
[開頭為 S 到 Z 的 R 模組]: #r-modules-beginning-with-s-through-z


###開頭為 A 到 E 的 R 模組

| 封裝名稱 | 封裝說明 |
| ------------ | ------------------- |
| abc | 近似貝氏計算 (ABC) 的工具 |
| abind | 結合多維度陣列 |
| actuar | 精算函數 |
| ade4 | 生態資料的分析：環境科學中的探索和歐幾里得方法 |
| AdMit | Student-t 分配的調適性混合 |
| aod | 過度離散資料的分析 |
| ape | 系統發生學和進化的分析 |
| 近似值 | 複雜電腦程式碼的貝氏預測 |
| arm | 使用迴歸和多層次/階層性模型的資料分析 |
| arules | 挖掘關聯規則和頻繁項目集 |
| arulesViz | 進行關聯規則和頻繁項目集的視覺化 |
| ash | David Scott 的 ASH 慣例 |
| assertthat | 簡易前後判斷提示 |
| AtelieR | GTK GUI，用於教導統計推斷的基本概念，以及進行基本貝氏檢定。 |
| BaBooN | 貝氏靴環法預測平均配對 - 離散資料的多重和單一插補法 |
| BACCO | 電腦程式碼輸出的貝氏預測 (BACCO) |
| BaM | Jeff Gill 的書籍函數和資料集 |
| bark | 貝氏加法迴歸核 |
| BAS | 使用貝氏調適性取樣的貝氏模型平均 |
| base | R 基準封裝 |
| BayesDA | Bayesian Data Analysis 一書的函數和資料集 |
| bayesGARCH | GARCH(1,1) 方法 (具 Student-t 創新) 的貝氏估計 |
| bayesm | 行銷/個體計量經濟學的貝氏推斷 |
| bayesmix | 貝氏混合模式 (包含 JAGS) |
| bayesQR | 貝氏分位數迴歸 |
| bayesSurv | 具有彈性誤差和隨機效應分配的貝氏存活迴歸 |
| Bayesthresh | 分類資料的貝氏臨界值混合效應模型 |
| BayesTree | 樹狀模型的貝氏方法 |
| BayesValidate | BayesValidate 封裝 |
| BayesX | 隨附軟體封裝 BayesX 的 R 公用程式 |
| BayHaz | 貝氏危險率估計的 R 函數 |
| bbemkr | 具有高斯誤差之多變量核迴歸的貝氏頻寬估計 |
| BCBCSF | 具所選特性的偏誤校正貝氏分類 |
| BCE | 貝氏組合估計量：從生物標記資料估計樣本 (分類) 組合 |
| bclust | 使用 spike-and-slab 階層式模型的貝氏分群法，適合用於將高維度資料分群 |
| bcp | 可供執行變更點問題貝氏分析的封裝 |
| BenfordTests | 評估班佛定律符合性的統計檢定 |
| bfp | 貝氏分數多項式 |
| BH | Boost C++ 標頭檔案 |
| bisoreg | 具有伯氏多項氏的貝氏同向性迴歸 |
| bit | 1 位元布林值的向量類別 |
| bitops | 位元運算 |
| BLR | 貝氏線性迴歸 |
| BMA | 貝氏模型平均 |
| Bmix | Stick-breaking 混合的貝氏取樣 |
| BMS | 貝氏模型平均程式庫 |
| bnlearn | 貝氏網路結構學習、母數學習和推斷 |
| boa | 適用於 MCMC 的貝氏輸出分析程式 (BOA) |
| Bolstad | Bolstad 函數 |
| boot | Bootstrap 函數 (原先由 Angelo Canty for S 提出) |
| bootstrap | An Introduction to the Bootstrap 一書的函數 |
| bqtl | 貝氏 QTL 對應工具組 |
| BradleyTerry2 | Bradley-Terry 模型 |
| brew | 可供產生報表的範本架構 |
| brglm | 二項式回應廣義線性模型中的偏差縮減 |
| bspec | 貝氏光譜推斷 |
| bspmma | bspmma：統合分析的貝氏半母數模型 |
| BVS | 貝氏變量選取：基因關聯研究的貝氏模型不確定量技術 |
| cairoDevice | 以 Cairo 為基礎的交叉平台抗鋸齒圖形裝置驅動程式 |
| calibrator | 複雜電腦程式碼的貝氏校準 |
| car | 應用迴歸手冊 |
| caret | 分類和迴歸定型 |
| catnet | 分類貝氏網路推斷 |
| caTools | 工具：移動視窗統計值、GIF、Base64、ROC AUC 等 |
| chron | 可處理日期和時間的年代式物件 |
| class | 用於分類的函數 |
| 叢集 | 叢集分析延伸 Rousseeuw 等 |
| clusterSim | 搜尋資料集的最佳叢集程序 |
| coda | MCMC 的輸出分析和診斷 |
| codetools | R 的程式碼分析工具 |
| coin | 排列檢定架構中的條件式推斷程序 |
| colorspace | 顏色空間操作 |
| combinat | 組合學公用程式 |
| compiler | R 編譯器封裝 |
| corpcor | 共變異數和 (偏) 相關的有效估計 |
| cslogistic | 有條件地指定的羅吉斯迴歸 |
| ctv | CRAN 工作檢視 |
| cubature | 具有超立方體的調適性多變量整合 |
| data.table | data.frame 的延伸 |
| datasets | R 資料集封裝 |
| 日期 | 用於處理日期的函數 |
| dclone | 最大近似法的資料複製和 MCMC 工具 |
| deal | 學習具有混合變數的貝氏網路 |
| Deducer | Deducer：R 的資料分析 GUI |
| DeducerExtras | Deducer 的其他對話方塊和函數 |
| deldir | Delaunay 三角化和 Dirichlet (Voronoi) 拼貼。 |
| DEoptimR | 純 R 的差分演化最佳化 |
| deSolve | 常微分方程式 (ODE)、偏微分方程式 (PDE)、微分代數方程式 (DAE) 和時滯微分方程式 (DDE) 之初始值問題的一般規劃求解 |
| devtools | 方便開發 R 程式碼的工具 |
| dichromat | 二色覺者的顏色配置 |
| digest | 建立 R 物件的密碼編譯雜湊摘要 |
| distrom | 分散式多項式迴歸 |
| dlm | 動態線性模型的貝氏和概度分析 |
| doSNOW | Snow 封裝的 Foreach 平行配接器 |
| dplyr | dplyr：資料操作的文法 |
| DPpackage | 採用 R 的貝氏無母數模型 |
| dse | 動態系統估計 (時間序列封裝) |
| e1071 | 維也納科技大學統計系的其他函數 (e1071) |
| EbayesThresh | 經驗貝氏臨界值和相關方法 |
| ebdbNet | 動態貝氏網路的經驗貝氏估計 |
| effects | 線性、常態線性、 多項式對數優劣比、 等比例勝算對數優劣比模型和混合效應模型的效應顯示 |
| emulator | 電腦程式的貝氏模擬 |
| ensembleBMA | 使用整體和貝氏模型平均的機率預測 |
| entropy | 熵、相互資訊和相關數量的估計 |
| EvalEst | 動態系統估計 - 擴充功能 |
| evaluate | 可提供比預設更多詳細資料的剖析和評估工具 |
| evdbayes | 極值原理中的貝氏分析 |
| evora | 風險預測分析的表觀遺傳變數離群值 |
| exactLoglinTest | 對數線性模型的 Monte Carlo 精確檢定 |
| expm | 矩陣指數 |
| extremevalues | 單變量離群值偵測 |


###開頭為 F 到 L 的 R 模組

| 封裝名稱 | 封裝說明 |
| ------------ | ------------------- |
| factorQR | 貝氏分位數迴歸因子模型 |
| faoutlier | 因子分析和 SEM 的具影響力個案偵測方法 |
| fitdistrplus | 有助於將母數統計配適至非設限或設限資料 |
| FME | 逆建模、敏感度、可識別性、Monte Carlo 分析的彈性建模環境 |
| foreach | R 的 Foreach 迴圈結構 |
| forecast | 時間序列和線性模型的預測函數 |
| foreign | 讀取 Minitab、S、SAS、SPSS、Stata、Systat、Weka、dBase 等儲存的資料 |
| formatR | 自動格式化 R 程式碼 |
| Formula | 延伸模型公式 |
| fracdiff | 分數維度差分 ARIMA aka ARFIMA(p,d,q) 模型 |
| gam | 廣義加法模型 |
| gamlr | Gamma Lasso 迴歸 |
| gbm | 廣義促進式迴歸模型 |
| gclus | 叢集圖形 |
| gdata | 用於資料操作的各種 R 程式設計工具 |
| gee | 廣義估計方程式規劃求解 |
| genetics | 族群遺傳學 |
| geoR | 地理統計資料的分析 |
| geoRglm | geoRglm - 廣義線性空間模型的封裝 |
| geosphere | 球面三角幾何 |
| ggmcmc | 用於從貝氏推斷分析 Markov Chain Monte Carlo 模擬的圖形化工具 |
| ggplot2 | 圖形文法的實作 |
| glmmBUGS | 具有 WinBUGS、BRugs 或 OpenBUGS 的廣義線性混合模型和空間模型 |
| glmnet | Lasso 和彈性網路規律性常態線性模型 |
| gmodels | 用於模型配適的各種 R 程式設計工具 |
| gmp | 多精度算術 |
| gnm | 廣義非線性模型 |
| googlePublicData | 用以建置 Google 的 Public Data Explorer DSPL 中繼資料檔案的 R 程式庫 |
| googleVis | R 和 Google 圖表之間的介面 |
| GPArotation | GPA 因素轉軸 |
| gplots | 用於繪製資料圖的各種 R 程式設計工具 |
| graphics | R 圖形封裝 |
| grDevices | 顏色和字型的 R 圖形裝置和支援 |
| gregmisc | Greg 的其他函數 |
| grid | 格線圖形封裝 |
| gridExtra | 格線圖形中的函數 |
| growcurves | 額外包含多重成員關係隨機效應的貝氏半母數和無母數成長曲線模型 |
| grpreg | 具群組共變量的迴歸模型的正則化路徑 |
| gsubfn | 字串和函數引數的公用程式 |
| gtable | 在資料表中排列 grob |
| gtools | 各種 R 程式設計工具 |
| gWidgets | 用於建置工具組相依、互動式 GUI 的 gWidgets API |
| gWidgetsRGtk2 | gWidgets for RGtk2 的工具組實作 |
| haplo.stats | 當連結階段不明確時，具性狀和共變量之單型的統計分析 |
| hbsae | 階層性貝氏小區域估計 |
| hdrcde | 最高密度區域和條件式密度估計 |
| heavy | 使用重尾分配的離群值調整之封裝 |
| hflights | 2011 年從休士頓啟程的航班 |
| hh | 統計分析和資料顯示：Heiberger 和 Holland |
| HI | 巢狀超平面所支援分配的模擬 |
| highr | R 的語法醒目提示 |
| Hmisc | Harrell 其他資訊 |
| htmltools | HTML 工具 |
| httpuv | HTTP 和 WebSocket 伺服器程式庫 |
| httr | 用於處理 URL 和 HTTP 的工具 |
| IBrokers | Interactive Brokers Trader Workstation 的 R API |
| igraph | 網路分析和視覺效果 |
| intervals | 用於處理各點和間隔的工具 |
| iplots | iPlots - R 的互動式圖形 |
| ipred | 已改進的預測量 |
| irr | 施測者間信度和一致性的各種係數 |
| iterators | R 的迭代器結構 |
| JavaGD | Java 圖形裝置 |
| JGR | JGR - Java GUI for R |
| kernlab | 核化機器學習實驗室 |
| KernSmooth | Wand 和 Jones 的核平滑函數 (1995) |
| KFKSDS | 卡爾曼濾波器、平滑因子和干擾平滑因子 |
| kinship2 | Pedigree 函數 |
| kknn | 加權 K 最近鄰法 |
| klaR | 分類和視覺效果 |
| knitr | 使用 R 產生動態報表的一般目的封裝 |
| ks | 核平滑 |
| labeling | 軸標籤 |
| Lahman | Sean Lahman 的棒球資料庫 |
| lars | 最小角度迴歸、Lasso 和 Forward Stagewise |
| lattice | 斜格紋圖形 |
| latticeExtra | 以斜格紋為基礎的其他圖形化公用程式 |
| lava | 線性潛在變數模型 |
| lavaan | 潛在變數分析 |
| leaps | 迴歸子集選取 |
| LearnBayes | 用於學習貝氏推斷的函數 |
| limSolve | 線性逆模型求解 |
| lme4 | 使用 Eigen 和 S4 的線性混合效應模型 |
| lmm | 線性混合模型 |
| lmPerm | 線性模型的排列檢定 |
| lmtest | 檢定線性迴歸模型 |
| locfit | 區域迴歸、 概度和密度估計 |
| lpSolve | Lp_solve v. 5.5 的介面，用以求解線性/整數程式 |


###開頭為 M 到 R 的 R 模組

| 封裝名稱 | 封裝說明 |
| ------------ | ------------------- |
| magic | 建立和調查魔方陣 |
| magrittr | magrittr - R 的向前管線運算子 |
| mapdata | 其他地圖資料庫 |
| mapproj | 地圖投影 |
| maps | 繪製地圖 |
| maptools | 用於讀取和處理空間物件的工具 |
| maptree | 對應、剪除和圖解樹狀模型 |
| markdown | R 的 Markdown 轉譯 |
| MASS | 支援變數和 Ripley's MASS 的函數和資料集 |
| MasterBayes | Pedigree 重建和分析的 ML 和 MCMC 方法 |
| Matrix | 疏鬆和密集矩陣類別和方法 |
| matrixcalc | 矩陣計算的函數集合 |
| MatrixModels | 使用疏鬆和密集矩陣建模 |
| maxent | 支援文字分類的低記憶體多項式羅吉斯迴歸 |
| maxLik | 最大概度估計 |
| mcmc | Markov Chain Monte Carlo |
| MCMCglmm | MCMC 廣義線性混合模型 |
| MCMCpack | Markov Chain Monte Carlo (MCMC) 封裝 |
| memoise | Memoise 函數 |
| methods | 正規方法和類別 |
| mgcv | 具 GCV/AIC/REML 平滑度估計的混合 GAM 計算工具 |
| mice | 使用連鎖方程式的多變量插補法 |
| microbenchmark | 亞微秒精確計時函數 |
| mime | 將檔案名稱對應至 MIME 類型 |
| minpack.lm | 在 MINPACK 中找到的 Levenberg-Marquardt 非線性最小平方演算法的 R 介面 (外加界限支援) |
| minqa | 使用二次近似的無導數最佳化演算法 |
| misc3d | 其他 3D 圖 |
| miscF | 其他函數 |
| miscTools | 其他工具和公用程式 |
| mixtools | 用於分析有限混合模型 |
| mlbench | 機器學習基準問題 |
| mlogitBMA | 多項式對數優劣比模型的貝氏模型平均 |
| mnormt | 多變量常態和 t 分配 |
| MNP | 用於配適多項式機率單位模型的 R 封裝 |
| modeltools | 統計模型的工具和類別 |
| mombf | 動差和逆動差貝氏因子 |
| monomvn | 具單調遺漏之多變量常態和 Student-t 資料的估計 |
| monreg | 無母數單調迴歸 |
| mosaic | 專案 MOSAIC (mosaic-web.org) 統計和數學教學公用程式 |
| MSBVAR | Markov 轉換、貝氏、向量自迴歸模型 |
| msm | 連續時間內的多狀態 Markov 和隱藏 Markov 模型 |
| multcomp | 一般參數模型中的同時推斷 |
| multicool | 採用 cool-lex 順序的多重資料集排列。 |
| munsell | Munsell 顏色系統 |
| mvoutlier | 以穩健方法為基礎的多變量離群值偵測 |
| mvtnorm | 多變量常態和 t 分配 |
| ncvreg | SCAD 和 MCP懲罰迴歸模型的正則化路徑 |
| nlme | 線性和非線性混合效應模型 |
| NLP | 自然語言處理基礎結構 |
| nnet | 前饋類神經網路和多項式對數線性模型 |
| numbers | 數論函數 |
| numDeriv | 精確數值導數 |
| openNLP | Apache OpenNLP 工具介面 |
| openNLPdata | Apache OpenNLP Jars 和基本英語模型 |
| OutlierDC | 使用設限資料分位數迴歸的離群值偵測 |
| OutlierDM | 複寫之高輸送量資料的離群值偵測 |
| outliers | 離群值檢定 |
| pacbpred | 疏鬆加法模型中 PAC-Bayesian 估計和預測 |
| parallel | 支援使用 R 進行平行計算 |
| partitions | 整數的加法分割 |
| party | 遞迴分割的實驗室 |
| PAWL | PAWL 演算法的實作 |
| pbivnorm | 向量化二元常態 CDF |
| pcaPP | 投影追蹤產生的穩健 PCA |
| permute | 用於產生資料限制排列的函數 |
| pls | 偏最小平方和主成分迴歸 |
| plyr | 用於分割、套用和合併資料的工具 |
| png | 讀取和寫入 PNG 影像 |
| polynom | 函數集合，用以實作多變量二項式操作的類別 |
| PottsUtils | Potts 模型的公用程式函數 |
| predmixcor | 以已校正特性選取偏誤的貝氏混合模型為基礎的分類規則 |
| PresenceAbsence | 出缺席模型評估 |
| prodlim | 乘積極限估計設限事件歷史 (存活) 分析的 Kaplan-Meier 和 Aalen-Johansson 方法 |
| profdpm | 概述 Dirichlet 處理混合 |
| profileModel | 用於概述各種模型類別之推斷函數的工具 |
| proto | 原型物件導向程式設計 |
| pscl | 史丹佛大學的政治科學計算實驗室 |
| psych | 心理學、心理測驗和人格研究的程序 |
| quadprog | 用以解決二次規劃問題的函數 |
| quantreg | 分位數迴歸 |
| qvcalc | 統計模型中因此效應的準變異數 |
| R.matlab | 透過 R 與 MATLAB 的連線一起讀取和寫入 MAT 檔案 |
| R.methodsS3 | 用於定義 S3 方法的公用程式函數 |
| R.oo | R 物件導向程式設計 (不論是否有參考) |
| R.utils | 各種程式設計公用程式 |
| R2HTML | R 物件的 HTML 匯出 |
| R2jags | 用於從 R 執行 jag 的封裝 |
| R2OpenBUGS | 從 R 執行 OpenBUGS |
| R2WinBUGS | 從 R / S-PLUS 執行 WinBUGS 和 OpenBUGS |
| ramps | 採用 RAMPS 的貝氏地理統計模型 |
| RandomFields | 隨機欄位的模擬和分析 |
| randomForest | Breiman 和 Cutler 用於分類和迴歸的隨機樹系 |
| RArcInfo | 用以從 Arc/Info V7.x 二進位覆蓋率匯入資料的函數 |
| raster | raster：地理資料分析和建模 |
| rbugs | 融合 R 和 OpenBugs 等 |
| RColorBrewer | ColorBrewer 調色盤 |
| Rcpp | 完美的 R 與 C++ 整合 |
| RcppArmadillo | Armadillo 範本化線性代數程式庫的 Rcpp 整合 |
| rcppbugs | cppbugs 的 R 繫結 |
| RcppEigen | 特徵範本化線性代數程式庫的 Rcpp 整合 |
| RcppExamples | 使用 Rcpp 連接 R 與 C++ 的範例 |
| RCurl | R 的一般網路 (HTTP/FTP/...) 用戶端介面 |
| relimp | 迴歸模型中效應的相對貢獻 |
| reshape | 有彈性地重塑資料 |
| reshape2 | 有彈性地重塑資料：重新啟動重塑封裝 |
| rgdal | 地理空間資料抽象程式庫 |
| rgeos | 幾何引擎的介面 - 開放原始碼 (GEOS) |
| rgl | 3D 視覺效果裝置系統 (OpenGL) |
| RGraphics | R Graphics, Second Edition 一書中的資料和函數 |
| RGtk2 | Gtk 2.8.0 和更新版本的 R 繫結 |
| RJaCGH | CGH 陣列分析的可逆跳躍式 MCMC |
| rjags | 使用 MCMC 的貝氏圖形化模型 |
| rJava | Java 介面的低階 R |
| RJSONIO | 將 R 物件序列化為 JSON (JavaScript 物件標記法) |
| robCompositions | 成分資料的穩健估計 |
| robustbase | 基本穩健統計 |
| RODBC | ODBC 資料庫存取 |
| rootSolve | 常微分方程式的非線性求根、均衡和穩態分析 |
| roxygen | 使用 R 進行文學編程 |
| roxygen2 | R 的 In-source 文件 |
| rpart | 遞迴分割和迴歸樹狀結構 |
| rrcov | 具有破壞點的可調整穩健估計量 |
| rscproxy | statconn：提供 R 的可攜式 C 型介面 (StatConnector) |
| RSGHB | 階層性貝氏估計的函數：彈性方法 |
| RSNNS | 使用 R 並採用 Stuttgart Neural Network Simulator (SNNS) 的類神經網路 |
| RTextTools | 透過監督式學習的自動文字分類 |
| RUnit | R 單元檢定架構 |
| runjags | 使用 JAGS 之 MCMC 模型的介面公用程式、平行計算方法和其他分配 |
| Runuran | UNU.RAN 隨機變量產生器的 R 介面 |
| rworldmap | 對應全域資料、向量和網格 |
| rworldxtra | 高解析度的國界 |


###開頭為 S 到 Z 的 R 模組

| 封裝名稱 | 封裝說明 |
| ------------ | ------------------- |
| SampleSizeMeans | 常態平均值的樣本大小計算 |
| SampleSizeProportions | 計算在估計兩個二項式比例之間差異時的樣本大小需求 |
| sandwich | 穩健共變異數矩陣估計量 |
| sbgcop | 半母數貝氏高斯 copula 估計和插補 |
| scales | 圖形的尺度函數 |
| scapeMCMC | MCMC 診斷圖 |
| scatterplot3d | 3D 散佈圖 |
| sciplot | 因子設計的科學圖形函數 |
| segmented | 具有中斷點/變更點估計的迴歸模型中的分段關係 |
| sem | 結構方程式模型 |
| seriation | 系列化的基礎結構 |
| setRNG | 設定 (常態) 隨機數字產生器和種子 |
| sgeostat | S+ 中地理統計模型的物件導向架構 |
| shapefiles | 讀取和寫入 ESRI Shapefile |
| shiny | R 的 Web 應用程式架構 |
| SimpleTable | 存在不可測干擾因素的 2 x 2 和 2 x 2 x K 資料表中因果效應的貝氏推斷和敏感度分析 |
| slam | 疏鬆輕量型陣列和矩陣 |
| smoothSurv | 具有平滑誤差分配的存活迴歸 |
| sna | 社交網路分析的工具 |
| snow | 工作站的簡易網路 |
| SnowballC | 以 C libstemmer UTF-8 程式庫為基礎的 Snowball stemmer |
| snowFT | 工作站的容錯簡易網路 |
| sp | 空間資料的類別和方法 |
| spacetime | 時空資料的類別和方法 |
| SparseM | 疏鬆線性代數 |
| Spatial | Kriging 和點模式分析的函數 |
| spBayes | 單變量和多變量時空模型 |
| spdep | 空間相依：加權配置、統計和模型 |
| spikeslab | 使用 spike and slab 迴歸的預測和變數選取 |
| splancs | 空間和時空點模式分析 |
| splines | 迴歸雲線函數和類別 |
| spTimer | 使用 R 的時空貝氏模型 |
| stats | R 統計封裝 |
| stats4 | 使用 S4 類別的統計函數 |
| stochvol | 隨機波動率 (SV) 模型的有效貝氏推斷 |
| stringr | 更容其處理字串 |
| strucchange | 測試、監視和鑑定結構化變更 |
| stsm | 結構化時間序列模型 |
| stsm.class | 結構化時間序列模型的類別和方法 |
| SuppDists | 補充分配 |
| survival | 存活分析 |
| svmpath | svmpath：SVM 路徑演算法 |
| tau | 文字分析公用程式 |
| tcltk | Tcl/Tk 介面 |
| tcltk2 | Tcl/Tk 加法 |
| TeachingDemos | 教學和學習的示範 |
| tensorA | 包含具名索引的進階張量計算 |
| testthat | Testthat 程式碼讓測試變有趣的工具 |
| textcat | 以 N-Gram 為基礎的文字分類 |
| textir | 文字分析的逆迴歸 |
| tfplot | 時間範圍使用者公用程式 |
| tframe | 時間範圍編碼核 |
| tgp | 貝氏樹狀高斯過程模型 |
| TH.data | TH 資料封存 |
| timeDate | Rmetrics - 年代式和日曆物件 |
| tm | 文字採礦封裝 |
| 工具 | 封裝開發的工具 |
| translations | R 轉譯封裝 |
| tree | 分類和迴歸樹 |
| tseries | 時間序列分析和計算金融 |
| tsfa | 時間序列因子分析 |
| tsoutliers | 時間序列中離群值的自動偵測 |
| TSP | 旅遊銷售人員問題 (TSP) |
| UsingR | Using R for Introductory Statistics 文字的資料集 |
| utils | R Utils 封裝 |
| varSelectIP | 客觀貝氏模型選取 |
| vcd | 視覺化分類資料 |
| vegan | 社群生態學封裝 |
| VGAM | 向量廣義線性和加法模型 |
| VIF | VIF 迴歸：大型資料的快速迴歸演算法 |
| whisker | R 的 {{mustache}} ，無邏輯範本 |
| wordcloud | 文字雲 |
| XLConnect | R 的 Excel 連接器 |
| XML | 用於在 R 和 S-Plus 中剖析和產生 XML 的工具 |
| xtable | 將資料表匯出至 LaTeX 或 HTML |
| xts | 可延伸時間序列 |
| yaml | 將 R 資料轉換為 YAML (反向) 的方法 |
| zic | 零膨脹計數模型的貝氏推斷 |
| zipfR | 文字次數分配的統計方法 |
| zoo | 規則和不規則時間序列的 S3 基礎結構 (Z 的已排序觀測值) |


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
 

<!---HONumber=July15_HO2-->
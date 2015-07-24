<properties 
	pageTitle="如何調整雲端服務 | Azure" 
	description="了解如何在 Azure 中調整雲端服務和連結的資源。" 
	services="cloud-services" 
	documentationCenter="" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015"
	ms.author="adegeo"/>





# 如何調整應用程式

在 Azure 管理入口網站的 [調整] 頁面上，您可以手動調整應用程式，也可以設定參數來自動調整應用程式。您可以調整執行 Web 角色、背景工作角色或虛擬機器的應用程式。若要調整執行 Web 角色或背景工作角色執行個體的應用程式，您可以新增或移除角色執行個體來容納工作負載。

當您向上或向下調整執行虛擬機器的應用程式時，系統並不會新建或刪除機器，而是在由先前建立的機器所組成之可用性設定組中開啟或關閉機器。您可以根據平均 CPU 使用量百分比或根據佇列中的訊息數目來指定調整。

在設定應用程式的調整之前，您應該先考量下列資訊：

- 您必須先將自己建立的虛擬機器新增到可用性設定組，才能調整使用這些虛擬機器的應用程式。您所新增的虛擬機器可以一開始處於開啟或關閉狀態，但是它們將在向上調整動作中開啟，並在向下調整動作中關閉。如需虛擬機器和可用性設定組的詳細資訊，請參閱[管理虛擬機器的可用性](../virtual-machines-manage-availability.md) (英文)。

- 調整受到核心使用量影響。較大型的角色執行個體或虛擬機器會使用較多的核心。您只能在訂閱的核心限制內調整應用程式。例如，如果您的訂閱設有 20 個核心的限制，而您使用兩個中型大小的虛擬機器來執行應用程式 (總計 4 個核心)，則您只能在訂閱中最多增加 16 個核心的其他雲端服務部署。可用性設定組中所有用於調整應用程式的虛擬機器都必須具有相同大小。如需核心使用量和電腦大小的詳細資訊，請參閱 [Azure 的虛擬機器和雲端服務大小](http://msdn.microsoft.com/library/dn197896.aspx)。

- 您必須先建立佇列並且將它與角色或可用性設定組建立關聯，然後才能根據訊息閾值調整應用程式。如需詳細資訊，請參閱[如何使用佇列儲存體服務](../storage-dotnet-how-to-use-queues.md) (英文)。

- 您可以調整與您雲端服務連結的資源。如需關於連結資源的詳細資訊，請參閱[作法：將資源連結到雲端服務](cloud-services-how-to-manage.md#how-to-link-a-resource-to-a-cloud-service)。

- 若要對應用程式啟用高可用性，您應該確定應用程式是以兩個以上的角色執行個體或虛擬機器來部署。如需詳細資訊，請參閱[服務等級協定](http://azure.microsoft.com/support/legal/sla/)。


## 手動調整執行 Web 角色或背景工作角色的應用程式

在 [調整] 頁面上，您可以手動增加或減少雲端服務中執行的執行個體數目。

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。

2. 按一下 [調整]。所有角色預設都已停用自動調整，這表示您可以手動變更應用程式所使用的執行個體數目。

    ![Scale page][manual_scale]

3. 雲端服務中的每個角色都有個滑桿可變更要使用的執行個體數目。若要新增角色執行個體，請將條塊往右拖曳。若要移除執行個體，請將條塊往左拖曳。
    
    ![Scale role][slider_role]
    
    只有在有適當數目的核心可以支援執行個體時，您才能增加使用的執行個體數目。滑桿的色彩代表您的訂閱中已使用和可用的核心：
    
    - 藍色代表所選角色所使用的核心
    
    - 深灰色代表訂閱中所有的角色和虛擬機器所使用的核心
    
    - 淺灰色代表可用於調整的核心
    
    - 粉紅色代表尚未儲存的已進行變更

4. 按一下 [儲存]。系統隨即根據您做的選取來新增或移除角色執行個體。

## 自動調整執行 Web 角色、背景工作角色或虛擬機器的應用程式

在 [調整] 頁面上，您可以設定雲端服務來自動增加或減少應用程式所使用的執行個體或虛擬機器數目。您可以根據下列參數來設定調整：

- [平均 CPU 使用量](#averagecpu) - 如果平均 CPU 使用量百分比高於或低於指定的閾值，則會建立或刪除角色執行個體，或在可用性設定組中開啟或關閉虛擬機器。
- [佇列訊息](#queuemessages) - 如果佇列中的訊息數目高於或低於指定的閾值，則會建立或刪除角色執行個體，或在可用性設定組中開啟或關閉虛擬機器。

## 平均 CPU 使用量

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。

2. 按一下 [調整]。

3. 捲動到角色或可用性設定組的區段，然後按一下 [CPU]。如此即會自動根據應用程式所使用的平均 CPU 資源百分比來調整應用程式。

    ![Autoscale on][autoscale_on]

4. 每個角色或可用性設定組都有個滑桿來變更可以使用的執行個體數目。若要設定可以使用的執行個體數目上限，請將右側條塊往右拖曳。若要設定可使用的執行個體數目下限，請將左側條塊往左拖曳。
    
    **注意：**在 [調整] 頁面中，[**執行個體**] 表示角色執行個體或虛擬機器的執行個體。
    
    ![Instance range][instance_range]
    
    執行個體數目上限受限於訂閱中的可用核心數目。滑桿的色彩代表您的訂閱中已使用和可用的核心：
    
    - 藍色代表角色可以使用的核心數目上限。
    
    - 深灰色代表訂閱中所有的角色和虛擬機器所使用的核心。當此值與角色所使用的核心重疊時，色彩會變成深藍色。
    
    - 淺灰色代表可用於調整的核心。
    
    - 粉紅色代表尚未儲存的已進行變更。

5. 有個滑桿用於指定平均 CPU 使用量百分比範圍。當平均 CPU 使用量百分比高於上限設定時，則會建立較多的角色執行個體，或開啟虛擬機器。當平均 CPU 使用量百分比低於下限設定時，則會刪除角色執行個體，或關閉虛擬機器。若要設定平均 CPU 百分比上限，請將右側條塊往右拖曳。若要設定平均 CPU 百分比下限，請將左側條塊往左拖曳。

    ![目標 CPU][target_cpu]

6. 您可以指定每次向上調整應用程式時要新增或開啟的執行個體數目。若要增加在向上調整應用程式時所建立或開啟的執行個體數目，請將條塊往右拖曳。若要減少數目，請將條塊往左拖曳。

    ![Scale cpu up][scale_cpuup]

7. 設定上一個調整動作與下一個向上調整動作之間應該等待的分鐘數。上一個調整動作可以是向上調整或向下調整動作。

    ![Up time][scale_uptime]

    計算平均 CPU 使用量百分比時，所有執行個體都納入計算範圍，而且此平均值是以上一個小時的使用量為基礎來計算。視應用程式使用的執行個體數目而定，如果等待時間設得很低，則需要的時間可能會比指定等調整動作發生的時間還要久。調整動作之間的最短時間是 5 分鐘。如果有任何執行個體處於轉換中狀態，則無法發生調整動作。

8. 您也可以指定當向下調整應用程式時要刪除或關閉的執行個體數目。若要增加在向下調整應用程式時所刪除或關閉的執行個體數目，請將條塊往右拖曳。若要減少數目，請將條塊往左拖曳。

    ![Scale cpu down][scale_cpudown]
    
    如果應用程式的 CPU 使用量可能會突然增加，您必須確定執行個體數目下限足以用來進行處理。

9. 設定上一個調整動作與下一個向下調整動作之間應該等待的分鐘數。上一個調整動作可以是向上調整或向下調整動作。

    ![Down time][scale_downtime]

10. 按一下 [儲存]。調整動作最多可能需要 5 分鐘的時間才能完成。

## 佇列訊息

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。
2. 按一下 [調整]。
3. 捲動到角色或可用性設定組的區段，然後按一下 [佇列]。如此即會自動根據目標的佇列訊息數目來調整應用程式。

    ![Scale queue][scale_queue]

4. 雲端服務中的每個角色或可用性設定組都有個滑桿來變更可以使用的執行個體數目。若要設定可以使用的執行個體數目上限，請將右側條塊往右拖曳。若要設定可使用的執行個體數目下限，請將左側條塊往左拖曳。

    ![Queue range][queue_range]
    
    **注意：**在 [調整] 頁面中，[**執行個體**] 表示角色執行個體或虛擬機器的執行個體。
    
    執行個體數目上限受限於訂閱中的可用核心數目。滑桿的色彩代表您的訂閱中已使用和可用的核心：- 藍色代表角色可以使用的核心數目上限。 - 深灰色代表訂閱中所有的角色和虛擬機器所使用的核心。當此值與角色所使用的核心重疊時，色彩會變成深藍色。 - 淺灰色代表可用於調整的核心。 - 粉紅色代表尚未儲存的已進行變更。

5. 選取要使用之佇列的相關聯儲存體帳戶。

    ![Storage name][storage_name]

6. 選取佇列。

    ![Queue name][queue_name]

7. 指定您預期每個執行個體要支援的訊息數目。執行個體會根據訊息總數除以每部電腦的目標訊息數目來調整。

    ![Message number][message_number]

8. 您可以指定每次向上調整應用程式時要新增或開啟的執行個體數目。若要增加在向上調整應用程式時所新增或開啟的執行個體數目，請將條塊往右拖曳。若要減少數目，請將條塊往左拖曳。

    ![Scale cpu up][scale_cpuup]

9. 設定上一個調整動作與下一個向上調整動作之間應該等待的分鐘數。上一個調整動作可以是向上調整或向下調整動作。

    ![Up time][scale_uptime]
    
    調整動作之間的最短時間是 5 分鐘。如果有任何執行個體處於轉換中狀態，則無法發生調整動作。

10. 您也可以指定向下調整應用程式時要刪除或停用的執行個體數目。有個滑桿用來指定調整增幅。若要增加在向下調整應用程式時所刪除或停用的執行個體數目，請將條塊往右拖曳。若要減少數目，請將條塊往左拖曳。

    ![Scale cpu down][scale_cpudown]

11.	設定上一個調整動作與下一個向下調整動作之間應該等待的分鐘數。上一個調整動作可以是向上調整或向下調整動作。

    ![Down time][scale_downtime]

12. 按一下 [儲存]。調整動作最多可能需要 5 分鐘的時間才能完成。

## 調整連結的資源

通常，當您調整角色時，連應用程式所使用的資料庫也一起調整會比較好。如果您將資料庫連結到雲端服務，則您是在 [調整] 頁面變更 SQL Database 版本及調整資料庫大小。

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。
2. 按一下 [調整]。
3. 在 [Linked Resources] 區段中，選取用於資料庫的版本。

    ![Linked resources][linked_resources]

4. 選取資料庫的大小。
5. 按一下 [儲存] 以更新連結的資源。

## 排定應用程式調整

您可以設定不同時間的排程，來排定自動調整應用程式。您有下列選項可以進行自動調整：

- **No schedule** - 這是預設選項，表示總是以相同方式來自動調整應用程式。

- **Day and night** - 此選項可讓您指定在白天與晚上的特定時間進行的調整。

**注意：**排程目前不適用於使用虛擬機器的應用程式。

1. 在[管理入口網站](https://manage.windowsazure.com/)中，按一下 [雲端服務]，然後按一下雲端服務的名稱以開啟儀表板。
2. 按一下 [調整]。
3. 在 [調整] 頁面上，按一下 [set up schedule times]。

    ![Schedule scaling][scale_schedule]

4. 選取想要設定的調整排程類型。

5. 指定白天的開始與結束時間，並設定時區。針對 day and night 排程，這些時間代表白天的開始與結束時間，而其餘時間代表晚上。

6. 按一下頁面底部的打勾記號來儲存排程。

7. 儲存排程之後，排程就會出現在清單中。您可以選取想要使用的時間排程，然後修改您的調整設定。只有在您選取的排程期間，才會套用調整設定。您可以按一下 [set up schedule times] 來編輯排程。

[manual_scale]: ./media/cloud-services-how-to-scale/CloudServices_ManualScaleRoles.png
[slider_role]: ./media/cloud-services-how-to-scale/CloudServices_SliderRole.png
[autoscale_on]: ./media/cloud-services-how-to-scale/CloudServices_AutoscaleOn.png
[instance_range]: ./media/cloud-services-how-to-scale/CloudServices_InstanceRange.png
[target_cpu]: ./media/cloud-services-how-to-scale/CloudServices_TargetCPURange.png
[scale_cpuup]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpBy.png
[scale_uptime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleUpWaitTime.png
[scale_cpudown]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownBy.png
[scale_downtime]: ./media/cloud-services-how-to-scale/CloudServices_ScaleDownWaitTime.png
[scale_queue]: ./media/cloud-services-how-to-scale/CloudServices_QueueScale.png
[queue_range]: ./media/cloud-services-how-to-scale/CloudServices_QueueRange.png
[storage_name]: ./media/cloud-services-how-to-scale/CloudServices_StorageAccountName.png
[queue_name]: ./media/cloud-services-how-to-scale/CloudServices_QueueName.png
[message_number]: ./media/cloud-services-how-to-scale/CloudServices_TargetMessageNumber.png
[linked_resources]: ./media/cloud-services-how-to-scale/CloudServices_ScaleLinkedResources.png
[scale_schedule]: ./media/cloud-services-how-to-scale/CloudServices_SetUpSchedule.png
 

<!---HONumber=62-->
## Wat is Queue Storage?

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount.

Veelvoorkomende toepassingen van Queue Storage zijn onder andere:

-   Het maken van een voorraad werk dat asynchroon moet worden verwerkt
-   Het doorgeven van berichten van een Azure-webrol aan een Azure-werkrol

## Concepten van Queue-service

De Queue-service bevat de volgende onderdelen:

![Queue1](./media/storage-queue-concepts-include/queue1.png)


- **URL-indeling:** Wachtrijen kunnen worden opgevraagd met de volgende URL-indeling:   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
    Met de volgende URL wordt een wachtrij in het diagram opgevraagd:  
        
        http://myaccount.queue.core.windows.net/images-to-download

- **Opslagaccount:** Alle toegang tot Azure Storage vindt plaats via een opslagaccount. Zie [Azure Storage Scalability and Performance Targets](../articles/storage/storage-scalability-targets.md) (Schaalbaarheids- en prestatiedoeleinden in Azure Storage) voor meer informatie over opslagaccountcapaciteit.

- **Wachtrij:** Een wachtrij bevat een set berichten. Alle berichten moeten zich in een wachtrij bevinden. De naam van een wachtrij mag alleen kleine letters bevatten. Zie [Naming Queues and Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx) (Wachtrijen en metagegevens een naam geven) voor informatie over de naamgeving van wachtrijen.

- **Bericht:** Een bericht in een willekeurige indeling, van maximaal 64 KB. Een bericht kan maximaal 7 dagen in de wachtrij blijven staan.



<!--HONumber=Jun16_HO2-->



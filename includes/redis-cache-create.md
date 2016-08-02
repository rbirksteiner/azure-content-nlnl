Als u een cache wilt maken, meldt u zich eerst aan bij de [Azure-portal](https://portal.azure.com) en klikt u vervolgens op **Nieuw**, **Gegevens en opslag**, **Redis-cache**.

>[AZURE.NOTE] Als u geen Azure-account hebt, kunt u binnen een paar minuten [gratis een Azure-account openen](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero).

![Nieuwe cache](media/redis-cache-create/redis-cache-new-cache-menu.png)

>[AZURE.NOTE] U kunt caches niet alleen maken in de Azure-portal, maar u kunt deze ook maken met ARM-sjablonen, PowerShell of Azure CLI.
>
>-  Zie [Create a Redis cache using a template](../articles/redis-cache/cache-redis-cache-arm-provision.md) (Een Redis-cache maken met een ARM-sjabloon) als u een cache wilt maken met ARM-sjablonen.
>-  Zie [Manage Azure Redis Cache with Azure PowerShell](../articles/redis-cache/cache-howto-manage-redis-cache-powershell.md) (Azure Redis-cache beheren met Azure PowerShell) als u een cache wilt maken met Azure PowerShell.
>-  Zie [How to create and manage Azure Redis Cache using the Azure Command-Line Interface (Azure CLI)](../articles/redis-cache/cache-manage-cli.md) (Azure Redis-cache maken en beheren met de Azure-opdrachtregelinterface (Azure CLI)) als u een cache wilt maken met Azure CLI.

Geef in de blade **Nieuwe Redis-cache** de gewenste configuratie voor de cache op.

![Cache maken](media/redis-cache-create/redis-cache-cache-create.png) 

-   Voer in **DNS-naam** de cachenaam in die u voor het eindpunt van de cache wilt gebruiken. De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en het teken `-` mag bevatten. De cachenaam mag niet beginnen of eindigen met het teken `-`, en opeenvolgende `-`-tekens zijn niet toegestaan.
-   Selecteer bij **Abonnement** het Azure-abonnement dat u voor de cache wilt gebruiken. Als uw account maar één abonnement heeft, wordt dit automatisch geselecteerd en wordt de vervolgkeuzelijst **Abonnement** niet weergegeven.
-   In **Resourcegroep** selecteert of maakt u een resourcegroep voor uw cache. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../articles/resource-group-overview.md) voor meer informatie. 
-   Gebruik **Locatie** om de geografische locatie op te geven waar uw cache wordt gehost. Voor de beste prestaties wordt aangeraden de cache te maken in dezelfde regio als de cacheclienttoepassing.
-   Gebruik **Prijscategorie** om de gewenste cachegrootte en -functies te selecteren.
-   Met **Redis-cluster** kunt u caches maken die groter zijn dan 53 GB en kunt u gegevens verdelen over meerdere Redis-knooppunten. Zie [How to configure clustering for a Premium Azure Redis Cache](../articles/redis-cache/cache-how-to-premium-clustering.md) (Clustering voor een Premium Azure Redis-cache configureren) voor meer informatie.
-   **Redis-persistentie** biedt de mogelijkheid om de cache vast te leggen in een Azure Storage-account. Zie [How to configure persistence for a Premium Azure Redis Cache](../articles/redis-cache/cache-how-to-premium-persistence.md) (Persistentie voor een Premium Azure Redis-cache configureren) voor instructies over het configureren van persistentie.
-   **Virtueel netwerk** biedt verbeterde beveiliging en isolatie door de toegang tot uw cache te beperken tot alleen de clients binnen het opgegeven Azure Virtual Network. U kunt alle functies van VNet, zoals subnetten, toegangscontrolebeleid en andere functies, gebruiken om de toegang tot Redis verder te beperken. Zie [How to configure Virtual Network support for a Premium Azure Redis Cache](../articles/redis-cache/cache-how-to-premium-vnet.md) (Virtual Network-ondersteuning voor een Premium Azure Redis-cache configureren) voor meer informatie.

Klik op **Maken** wanneer de opties voor de nieuwe cache zijn geconfigureerd. Het kan enkele minuten duren voordat de cache is gemaakt. Als u de status wilt zien, kunt u de voortgang bekijken via het startboard. Nadat de cache is gemaakt, heeft de nieuwe cache de status **Wordt uitgevoerd** en is deze gereed voor gebruik met de standaardinstellingen.

![Cache gemaakt](media/redis-cache-create/redis-cache-cache-created.png)




<!--HONumber=Jun16_HO2-->



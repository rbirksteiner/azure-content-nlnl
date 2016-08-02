<properties
    pageTitle="Azure Batch-account maken | Microsoft Azure"
    description="Informatie over het maken van een Azure Batch-account in Azure Portal voor het uitvoeren van grootschalige parallelle workloads in de cloud"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="marsma"/>

# Azure Batch-account in Azure Portal maken en beheren

> [AZURE.SELECTOR]
- [Azure Portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

[Azure Portal][azure_portal] biedt u de benodigde hulpmiddelen voor het maken en beheren van een Azure Batch-account, dat u voor de workload van grootschalige parallelle verwerking kunt gebruiken. In dit artikel leert u hoe u een Batch-account maakt met behulp van de portal en ontdekt u de belangrijke instellingen en eigenschappen van een Batch-account. Voor bijvoorbeeld toepassingen en services die u met Batch ontwikkelt, zijn uw account-URL en een toegangssleutel nodig voor de communicatie met de API's van de Batch-service. Beide gegevens vindt u in Azure Portal.

>[AZURE.NOTE] Azure Portal ondersteunt momenteel een subset van de functies in de Batch-service, waaronder het maken van een account, het beheer van de instellingen en eigenschappen van een Batch-account en het maken en bewaken van pools en jobs. De volledige functieset van Batch is beschikbaar voor ontwikkelaars via de Batch-API's.

## Batch-account maken

1. Meld u aan bij [Azure Portal][azure_portal].

2. Klik op **Nieuw** > **Virtuele machines** > **Batch-service**.

    ![Batch in de Marketplace][marketplace_portal]

3. De blade **Nieuw Batch-account** wordt weergegeven. Bij de items *a* tot *e* hieronder vindt u voor elk blade-element een beschrijving.

    ![Batch-account maken][account_portal]

    a. **Accountnaam**: Een unieke naam voor uw Batch-account. Deze naam moet uniek zijn in de Azure-regio waarin het account wordt gemaakt (zie *Locatie* hieronder). Deze naam mag alleen kleine letters en cijfers bevatten en moet 3 tot 24 tekens lang zijn.

    b. **Abonnement**: Een abonnement om het Batch-account in te maken. Als u slechts één abonnement hebt, is het standaard geselecteerd.

    c. **Resourcegroep**: Een bestaande resourcegroep voor het nieuwe Batch-account. U kunt ook optioneel een nieuwe resourcegroep maken.

    d. **Locatie**: Een Azure-regio om het Batch-account in te maken. Alleen de regio's die door uw abonnement en resourcegroep worden ondersteund, worden als opties weergegeven.

    e. **Opslagaccount** (optioneel): Een **algemeen** opslagaccount dat u aan het nieuwe Batch-account koppelt. De functie voor [toepassingspakketten](batch-application-packages.md) van Batch zal het gekoppelde opslagaccount gebruiken voor het opslaan en ophalen van toepassingspakketten. Zie [Application deployment with Azure Batch application packages](batch-application-packages.md) (Toepassingsimplementatie met Azure Batch-toepassingspakketten) voor meer informatie over deze functie.

     > [AZURE.IMPORTANT] Voor het opnieuw genereren van sleutels in een gekoppelde opslagaccount zijn speciale overwegingen vereist. Zie [Overwegingen voor Batch-accounts](#considerations-for-batch-accounts) hieronder voor meer informatie.

4. Klik op **Maken** om het account te maken.

  In de portal wordt aangegeven dat het account wordt **geïmplementeerd**. Bij voltooiing wordt de melding **Implementaties voltooid** weergegeven in *Meldingen*.

## Eigenschappen van Batch-account weergeven

Op de blade van het Batch-account worden verschillende eigenschappen voor het account weergegeven. U hebt er ook toegang tot extra instellingen zoals toegangstoetsen, quota, gebruikers en opslagaccountkoppelingen.

* **URL van Batch-account**: deze URL biedt toegang tot uw Batch-account bij het gebruik van API's zoals de API [Batch REST][api_rest] of de clientbibliotheek [Batch .NET][api_net] en voldoet aan de volgende indeling:

    `https://<account_name>.<region>.batch.azure.com`

* **Toegangssleutels**: Als u de toegangssleutels van uw Batch-account wilt bekijken en beheren, klikt u op het sleutelpictogram om de blade **Sleutels beheren** te openen of klikt u op **Alle instellingen** > **Sleutels**. Een toegangssleutel is vereist voor de communicatie met de API's van de Batch-service, zoals [Batch REST][api_rest], of met de clientbibliotheek [Batch .NET][api_net].

    ![Sleutels van Batch-account][account_keys]

* **Alle instellingen**: Als u alle instellingen voor het Batch-account wilt beheren of de eigenschappen ervan wilt weergeven, klikt u op **Alle instellingen** om de blade **Instellingen** te openen. Deze blade biedt toegang tot alle instellingen en eigenschappen voor het account. U kunt er ook de accountquota bekijken, een Azure-opslagaccount selecteren om aan het Batch-account te koppelen en gebruikers beheren.

    ![De blades Instellingen en Eigenschappen van Batch-account][5]

## Overwegingen voor Batch-accounts

* U kunt ook Batch-accounts maken en beheren met de [Batch PowerShell-cmdlets](batch-powershell-cmdlets-get-started.md) en de bibliotheek [Batch Management .NET](batch-management-dotnet.md).

* De Batch-account zelf wordt niet in rekening gebracht. De Azure-rekenresources die door uw Batch-oplossingen worden gebruikt, worden wel in rekening gebracht, evenals de resources die door andere services worden gebruikt wanneer uw workloads worden uitgevoerd. Zo zullen bijvoorbeeld de rekenknooppunten in uw pools in rekening worden gebracht, en als u de functie voor [toepassingspakketten](batch-application-packages.md) gebruikt, worden de Azure Storage-resources die worden gebruikt om uw toepassingspakketversies op te slaan in rekening gebracht. Zie [Prijzen van Batch][batch_pricing] voor meer informatie.

* U kunt in één Batch-account meerdere Batch-workloads uitvoeren of uw workloads verdelen over Batch-accounts in verschillende Azure-regio's.

* Als u verschillende grootschalige Batch-workloads uitvoert, moet u rekening houden met bepaalde [quota en limieten voor de Batch-service](batch-quota-limit.md) die gelden voor uw Azure-abonnement en elk Batch-account. De huidige quota voor een Batch-account worden in de portal weergegeven bij de eigenschappen van het account.

* Als u een opslagaccount aan uw Batch-account koppelt, wees dan voorzichtig wanneer u de toegangssleutels van het opslagaccount opnieuw genereert. U kunt het best slechts één opslagaccountsleutel opnieuw genereren, op de blade van de gekoppelde opslagaccount op **Sleutels synchroniseren** klikken, 5 minuten wachten tot de sleutels zijn doorgegeven aan de rekenknooppunten in uw pools en vervolgens, indien nodig, de andere sleutel opnieuw genereren en synchroniseren. Als u beide sleutels tegelijk opnieuw genereert, kunnen uw rekenknooppunten geen van beide sleutels synchroniseren en zullen zij toegang verliezen tot het opslagaccount.

  ![Sleutels van opslagaccount opnieuw genereren][4]

> [AZURE.IMPORTANT] Batch ondersteunt momenteel *alleen* het opslagaccounttype **Algemeen**, zoals beschreven in stap 5 [Een opslagaccount maken](../storage/storage-create-storage-account.md#create-a-storage-account) in [Over Azure-opslagaccounts](../storage/storage-create-storage-account.md). Wanneer u een Azure-opslagaccount aan uw Batch-account koppelt, koppelt u *alleen* een **algemeen** opslagaccount.

## Volgende stappen

* Zie [Overzicht van Azure Batch-functies](batch-api-basics.md) voor meer informatie over concepten en functies van de Batch-service. In het artikel worden de primaire Batch-resources zoals pools, rekenknooppunten, jobs en taken besproken en vindt u een overzicht van de servicefuncties waarmee grootschalige rekenworkloads kunnen worden uitgevoerd.

* Leer de basisbeginselen van het ontwikkelen van een voor Batch geschikte toepassing met behulp van de [clientbibliotheek Batch .NET](batch-dotnet-get-started.md). Het [inleidende artikel](batch-dotnet-get-started.md) leidt u door een werkende toepassing die gebruikmaakt van de Batch-service voor het uitvoeren van een workload op meerdere rekenknooppunten. U vindt er ook informatie over het gebruik van Azure Storage voor het faseren en ophalen van een workloadbestand.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Sleutels van opslagaccount opnieuw genereren"
[5]: ./media/batch-account-create-portal/batch_acct_05.png "De blades Instellingen en Eigenschappen van Batch-account"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG



<!--HONumber=Jun16_HO2-->



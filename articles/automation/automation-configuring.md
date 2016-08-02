<properties
   pageTitle="Azure Automation configureren"
   description="Hierin worden stappen beschreven die u moet uitvoeren om Azure Automation te configureren voor het eerste gebruik."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="magoedte;bwren" />

# Azure Automation configureren

In dit artikel wordt beschreven welke acties u moet uitvoeren om te beginnen met het gebruik van Azure Automation.

## Automation-accounts

Wanneer u Azure Automation voor het eerst start, moet u ten minste één Automation-account maken. Met Automation-accounts kunt u uw Automation-resources (runbooks, assets, configuraties) isoleren van de Automation-resources in andere Automation-accounts. U kunt Automation-accounts gebruiken om Automation-resources onder te verdelen in afzonderlijke logische omgevingen. U kunt bijvoorbeeld een account gebruiken voor ontwikkeling en een ander account voor productie.

De Automation-resources voor elk Automation-account zijn gekoppeld aan één Azure-regio, maar met Automation-accounts kunnen Azure-services in elke regio worden beheerd. De voornaamste reden om Automation-accounts in verschillende regio's te maken zou zijn als u beleid hebt waardoor gegevens en resources moeten worden geïsoleerd in een specifieke regio.

>[AZURE.NOTE] Automation-accounts, en de resources die deze bevatten, die met de Azure Portal worden gemaakt, zijn niet toegankelijk in de klassieke Azure-portal. Als u deze accounts of de bijbehorende resources wilt beheren met Windows PowerShell, moet u gebruikmaken van de Azure Resource Manager-modules. 
>
>Automation-accounts die met de klassieke Azure-portal zijn gemaakt, kunnen worden beheerd via een van de portals en een van de sets met cmdlets. Zodra het account is gemaakt, maakt het niet meer uit hoe u resources binnen het account maakt en beheert. Als u van plan bent om de klassieke Azure-portal te blijven gebruiken, moet u deze gebruiken in plaats van de Azure Portal voor het maken van Automation-accounts.


De toegang voor een Automation-account kan tijdelijk worden ingetrokken als er een probleem is met uw Azure-account, zoals een achterstallige betaling. In dit geval hebt u geen toegang tot het account, actieve jobs worden onderbroken en alle planningen worden uitgeschakeld. U kunt het account bekijken, maar u kunt geen resources zien in het account. Zodra u het probleem oplost en het Automation-account is ingeschakeld, moet u uw planningen inschakelen en eventuele runbooks opnieuw starten die waren onderbroken.


## Verificatie voor Azure-resources configureren

Wanneer u Azure-resources opent met behulp van de [Azure-cmdlets](http://msdn.microsoft.com/library/azure/jj554330.aspx), moet u verificatie voor uw Azure-abonnement opgeven. In Azure Automation wordt dit gedaan met een organisatieaccount in Azure Active Directory dat u als beheerder voor uw abonnement configureert. U kunt vervolgens een [referentie](http://msdn.microsoft.com/library/dn940015.aspx) voor dit gebruikersaccount maken en dit gebruiken met [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) in uw runbook.

>[AZURE.NOTE] Microsoft-accounts, voorheen bekend als LiveID's, kunnen niet worden gebruikt met Azure Automation.

## Een nieuwe Azure Active Directory-gebruiker maken voor het beheren van een Azure-abonnement

1. Meld u aan bij de klassieke Azure-portal als servicebeheerder voor het Azure-abonnement dat u wilt beheren.
2. Selecteer **Active Directory**.
3. Selecteer de mapnaam die is gekoppeld aan uw Azure-abonnement. Indien nodig kunt u deze koppeling wijzigen via **Instellingen > Abonnementen > Map bewerken**.
4. [Maak een nieuwe Active Directory-gebruiker](http://msdn.microsoft.com/library/azure/hh967632.aspx).  Selecteer **Nieuwe gebruiker in uw organisatie** voor het **type gebruiker** en schakel **Multi-Factor Authentication** niet in.
5. Noteer de volledige naam van de gebruiker en het tijdelijke wachtwoord.
7. Selecteer **Instellingen > Beheerders > Toevoegen**.
8. Typ de volledige gebruikersnaam van de gebruiker die u hebt gemaakt.
9. Selecteer het abonnement dat de gebruiker moet gaan beheren.
10. Meld u af bij Azure en meld u opnieuw aan met het account dat u zojuist hebt gemaakt. U wordt gevraagd om het wachtwoord van de gebruiker te wijzigen.
11. Maak een nieuwe [Azure Automation-referentieasset](automation-credentials.md) voor het gebruikersaccount dat u hebt gemaakt. Het **referentietype** moet **Windows PowerShell-referentie** zijn.

## Een Automation-account maken

Een Automation-account is een container voor uw Azure Automation-resources. Het biedt een manier om uw omgevingen te scheiden en uw werkstromen verder in te delen. Als u al een Automation-account hebt gemaakt, kunt u deze stap overslaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

2. Klik op **Nieuw** > **Beheer** > **Automation-account**.

3. Configureer op de blade **Automation-account toevoegen** de details van uw Automation-account. 

>[AZURE.NOTE] Wanneer een Automation-account is gemaakt met Azure Portal, worden het account en alle resources die eraan zijn gekoppeld niet teruggebracht naar de klassieke Azure-portal. 

Hieronder volgt de lijst met te configureren parameters:

|Parameter            |Beschrijving |
|:---|:---|
| Naam | Naam van uw Automation-account. Dit moet een unieke waarde zijn. |
| Resourcegroep | Met resourcegroepen is het eenvoudig om gerelateerde Azure-resources te zien en beheren. In Azure Portal kunt u een bestaande resourcegroep kiezen of een nieuwe maken voor uw Automation-account, terwijl in de klassieke Azure-portal alle Automation-accounts in een standaardresourcegroep zouden worden geplaatst. |
| Abonnement | Kies een abonnement in de lijst met beschikbare abonnementen. |
| Regio | De regio geeft aan waar de Automation-resources in het account worden opgeslagen. U kunt elke regio in de lijst kiezen, dit is niet van invloed op de functionaliteit van uw account, maar uw runbooks worden mogelijk sneller uitgevoerd als uw accountregio nabij de locatie is waar uw andere Azure-resources zijn opgeslagen. |
| Accountopties | Met deze optie kunt u kiezen welke resources in uw nieuwe Automation-account worden gemaakt. Als u **Ja** selecteert, wordt een zelfstudierunbook gemaakt. |

![Account maken](media/automation-configuration/automation-01-create-automation-account.png)

>[AZURE.NOTE] Wanneer een Automation-account dat is gemaakt met de klassieke Azure-portal wordt [verplaatst naar een andere resourcegroep](../resource-group-move-resources.md) met behulp van Azure Portal, is het Automation-account niet meer beschikbaar in de klassieke Azure-portal.



## De referentie in een runbook gebruiken

U kunt de referentie in een runbook ophalen met de activiteit [Get-AutomationPSCredential](http://msdn.microsoft.com/library/dn940015.aspx) en deze vervolgens gebruiken met [Add-AzureAccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) om verbinding te maken met uw Azure-abonnement. Als de referentie een beheerder van meerdere Azure-abonnementen is, dan moet u ook [Select-AzureSubscription](http://msdn.microsoft.com/library/dn495203.aspx) gebruiken om het juiste abonnement op te geven. Dit wordt getoond in de onderstaande voorbeeld-Windows PowerShell die doorgaans wordt weergegeven boven aan de meeste Azure Automation-runbooks.

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

U moet deze regels na [controlepunten](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) in uw runbook herhalen. Als het runbook wordt onderbroken en vervolgens voor een andere werkrol wordt hervat, moet de verificatie opnieuw worden uitgevoerd.

## Verwante artikelen
- [Azure Automation: Authenticating to Azure using Azure Active Directory (Azure Automation: Verificatie voor Azure met Azure Active Directory)](https://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 



<!--HONumber=Jun16_HO2-->



<properties 
   pageTitle="Op rollen gebaseerd toegangsbeheer in Azure Automation | Microsoft Azure"
   description="Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over toegangsbeheer voor Azure-resources. In dit artikel wordt beschreven hoe u RBAC instelt in Azure Automation."
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn"
   keywords="automation rbac, role based access control, azure rbac" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="magoedte;sngun"/>

# Op rollen gebaseerd toegangsbeheer in Azure Automation

## Op rollen gebaseerd toegangsbeheer

Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over toegangsbeheer voor Azure-resources. Met [RBAC](../active-directory/role-based-access-control-configure.md) kunt u taken scheiden binnen uw team en alleen de mate van toegang verlenen aan gebruikers, groepen en toepassingen die nodig is om de taken uit te voeren. Op rollen gebaseerde toegang kan worden verleend aan gebruikers via de Azure Portal, Azure-opdrachtregelprogramma's of Azure Management-API's.

## RBAC in Automation-accounts

In Azure Automation wordt toegang verleend door de juiste RBAC-rol toe te wijzen aan gebruikers, groepen en toepassingen bij het Automation-accountbereik. Hieronder vindt u de ingebouwde rollen die worden ondersteund met een Automation-account:  

|**Rol** | **Beschrijving** |
|:--- |:---|
| Eigenaar | De rol Eigenaar geeft toegang tot alle resources en acties binnen een Automation-account, inclusief het bieden van toegang aan andere gebruikers, groepen en toepassingen om het Automation-account te beheren. |
| Inzender | Met de rol Inzender kunt u alles beheren. U kunt alleen geen toegangsmachtigingen van andere gebruikers voor een Automation-account wijzigen. |
| Lezer | Met de rol Lezer kunt u alle resources in een Automation-account weergeven, maar kunt u geen wijzigingen aanbrengen. |
| Automation-operator | Met de rol Automation-operator kunt u operationele taken uitvoeren, zoals het starten, stoppen, onderbreken, hervatten en plannen van taken. Deze rol is handig als u niet wilt dat uw Automation-accountresources zoals referentieassets en runbooks worden weergegeven of gewijzigd, maar u wel wilt toestaan dat leden van uw organisatie deze runbooks uitvoeren. In [Automation-operatoracties](../active-directory/role-based-access-built-in-roles.md#automation-operator) worden de acties weergegeven die worden ondersteund met de rol Automation-operator voor het Automation-account en de bijbehorende resources. |
| Beheerder gebruikerstoegang | Met de rol Beheerder gebruikerstoegang kunt u gebruikerstoegang tot Azure Automation-accounts beheren. |

In dit artikel wordt een stapsgewijze beschrijving gegeven van het instellen van RBAC in Azure Automation. 

## RBAC configureren voor uw Automation-account met Azure Portal

1.  Meld u aan bij [Azure Portal](https://portal.azure.com/) en open uw Automation-account via de blade Automation-accounts.  

2.  Klik op het besturingselement **Toegang** in de rechterbovenhoek. Hiermee wordt de blade **Gebruikers** geopend, waar u nieuwe gebruikers, groepen en toepassingen kunt toevoegen om uw Automation-account te beheren en bestaande rollen kunt weergeven die kunnen worden geconfigureerd voor het Automation-account.  

    ![De knop Toegang](media/automation-role-based-access-control/automation-01-access-button.png)  

>[AZURE.NOTE]  **Abonnementsbeheerders** bestaat al als de standaardgebruiker. De Active Directory-groep Abonnementsbeheerders bevat de servicebeheerder(s) en medebeheerder(s) voor uw Azure-abonnement. De servicebeheerder is de eigenaar van uw Azure-abonnement en de bijhorende resources en neemt ook de eigenaarsrol over voor de Automation-accounts. Dit betekent dat de toegang wordt **overgenomen** voor **servicebeheerders en medebeheerders** van een abonnement en dat deze wordt **toegewezen** voor alle andere gebruikers. Klik op **Abonnementsbeheerders** om meer details over hun machtigingen weer te geven.  

### Een nieuwe gebruiker toevoegen en een rol toewijzen

1.  Klik op de blade Gebruikers op **Toevoegen** om de blade **Toegang toevoegen** te openen waar u een gebruiker, groep of toepassing kunt toevoegen, en hieraan een rol kunt toewijzen.  

    ![Gebruiker toevoegen](media/automation-role-based-access-control/automation-02-add-user.png)  

2.  Selecteer een rol in de lijst met beschikbare rollen. We kiezen de rol **Lezer**, maar u kunt elke beschikbare ingebouwde rol kiezen die met een Automation-account wordt ondersteund of elke aangepaste rol die u mogelijk hebt gedefinieerd.  

    ![Rol selecteren](media/automation-role-based-access-control/automation-03-select-role.png)  

3.  Klik op **Gebruikers toevoegen** om de blade **Gebruikers toevoegen** te openen. Als u gebruikers, groepen of toepassingen hebt toegevoegd om uw abonnement te beheren, dan worden die gebruikers weergegeven en kunt u deze selecteren om toegang toe te voegen. Als er geen gebruikers worden weergegeven of als de gebruiker die u wilt toevoegen, niet wordt weergegeven, klikt u op **Uitnodigen** om de blade **Een gast uitnodigen** te openen, waar u een gebruiker met een geldig e-mailadres voor een Microsoft-account kunt uitnodigen, zoals Outlook.com, OneDrive of Xbox Live-id's. Nadat u het e-mailadres van de gebruiker hebt ingevoerd, klikt u op **Selecteren** om de gebruiker toe te voegen en klikt u vervolgens op **OK**. 

    ![Gebruikers toevoegen](media/automation-role-based-access-control/automation-04-add-users.png)  
 
Nu moet u zien dat de gebruiker is toegevoegd aan de blade **Gebruikers** en dat de rol **Lezer** is toegewezen.  

![Gebruikers weergeven](media/automation-role-based-access-control/automation-05-list-users.png)  

U kunt ook een rol aan de gebruiker toewijzen via de blade **Rollen**. Klik op **Rollen** op de blade Gebruikers om de blade **Rollen** te openen. Via deze blade kunt u het volgende bekijken: de naam van de rol, het aantal gebruikers en de groepen die aan die rol zijn toegewezen.

![Rol vanuit blade Gebruikers toewijzen](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
>[AZURE.NOTE] Op rollen gebaseerd toegangsbeheer kan alleen worden ingesteld op het niveau van het Automation-account en niet op een resource onder het Automation-account.

U kunt meer dan één rol toewijzen aan een gebruiker, groep of toepassing. Als we bijvoorbeeld de rol **Automation-operator** samen met de rol **Lezer** toevoegen aan de gebruiker, dan kan deze alle Automation-resources bekijken, en ook de runbooktaken uitvoeren. U kunt de vervolgkeuzelijst uitvouwen om een lijst met rollen weer te geven die aan de gebruiker zijn toegewezen.  

![Meerdere rollen weergeven](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)  
 
### Een gebruiker verwijderen

U kunt de toegangsmachtigingen voor een gebruiker verwijderen die het Automation-account niet beheert, of die niet meer voor de organisatie werkt. Hieronder vindt u de stappen voor het verwijderen van een gebruiker: 

1.  Selecteer op de blade **Gebruikers** de roltoewijzing die u wilt verwijderen.

2.  Klik op de knop **Verwijderen** op de blade Toewijzingdetails.

3.  Klik op **Ja** om het verwijderen te bevestigen. 

    ![Gebruikers verwijderen](media/automation-role-based-access-control/automation-08-remove-users.png)  

## Gebruiker met toegewezen rol

Wanneer een gebruiker die aan een rol is toegewezen, zich aanmeldt bij zijn/haar Automation-account, ziet deze het account van de eigenaar weergegeven in de lijst met **Standaardmappen**. Als een gebruiker het Automation-account wil bekijken waaraan hij/zij is toegevoegd, moet deze overschakelen van de standaardmap naar de standaardmap van de eigenaar.  

![Standaardmap](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)  

### Gebruikerservaring voor de rol Automation-operator

Wanneer een gebruiker, aan wie de rol Automation-operator is toegewezen, het Automation-account bekijkt waaraan hij/zij is toegewezen, kan de gebruiker alleen de lijst met runbooks, runbooktaken en planningen weergeven die in het Automation-account zijn gemaakt, maar niet de bijbehorende definitie. De gebruiker kan de runbooktaak starten, stoppen, onderbreken, hervatten of plannen. De gebruiker heeft geen toegang tot andere Automation-resources, zoals configuraties, Hybrid Worker-groepen of DSC-knooppunten.  

![Geen toegang tot resources](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

Wanneer de gebruiker op het runbook klikt, worden de opdrachten voor het weergeven van de bron of het bewerken van het runbook niet aangeboden, omdat de rol Automation-operator geen toegang tot de opdrachten toestaat.  

![Geen toegang om runbook te bewerken](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)  

De gebruiker heeft toegang voor het weergeven en maken van planningen, maar heeft geen toegang tot andere assettypen.  

![Geen toegang tot assets](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)  

Deze gebruiker heeft ook geen toegang tot de weergave van de webhooks die aan een runbook zijn gekoppeld.

![Geen toegang tot webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## RBAC configureren voor uw Automation-account met Azure PowerShell

Op rollen gebaseerde toegang kan ook worden geconfigureerd voor een Automation-Account met de volgende [Azure PowerShell-cmdlets](../active-directory/role-based-access-control-manage-access-powershell.md).

• Met [Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) wordt een lijst weergegeven van alle rollen die beschikbaar zijn in Azure Active Directory RBAC. U kunt deze opdracht samen met de eigenschap **Naam** gebruiken om alle gebruikers met een specifieke rol weer te geven.  
    **Voorbeeld:**  
    ![Roldefinitie ophalen](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)  

• Met [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) worden Azure RBAC-roltoewijzingen weergegeven bij het opgegeven bereik. Zonder parameters worden met deze opdracht alle roltoewijzingen geretourneerd die onder het abonnement zijn gemaakt. Gebruik de parameter **ExpandPrincipalGroups** om toegangstoewijzingen voor de opgegeven gebruiker weer te geven en voor de groepen waarvan de gebruiker lid is.  
    **Voorbeeld:** gebruik de volgende opdracht om alle gebruikers en de bijbehorende rollen binnen een Automation-account weer te geven.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Roltoewijzing ophalen](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) om toegang te verlenen aan gebruikers, groepen en toepassingen bij een bepaald bereik.  
    **Voorbeeld:** gebruik de volgende opdracht om een nieuwe rol 'Automation-operator' te maken voor een gebruiker bij het Automation-accountbereik.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nieuwe roltoewijzing](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Gebruik [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) om toegang tot de opgegeven gebruiker, groep of toepassing te verwijderen bij een bepaald bereik.
    **Voorbeeld:** gebruik de volgende opdracht om een nieuwe rol 'Automation-operator' te maken voor een gebruiker bij het Automation-accountbereik.

    Remove-AzureRmRoleAssignment -SignInName "<sign-in Id of a user you wish to remove>" -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

Vervang in de bovenstaande cmdlets de aanmeldnaam, de abonnements-id, de resourcegroepnaam en de Automation-accountnaam door uw accountgegevens. Kies **Ja** bij de vraag of u wilt doorgaan met het verwijderen van de roltoewijzing.   


## Volgende stappen
-  Raadpleeg [RBAC met Azure PowerShell beheren](../active-directory/role-based-access-control-manage-access-powershell.md) voor informatie over verschillende manieren om RBAC voor Azure Automation te configureren.
- Zie [Een runbook starten](automation-starting-a-runbook.md) voor informatie over verschillende manieren om een runbook te starten.
- Raadpleeg [Azure Automation-runbooktypen](automation-runbook-types.md) voor informatie over verschillende typen.




<!--HONumber=Jun16_HO2-->



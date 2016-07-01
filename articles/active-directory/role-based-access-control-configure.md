<properties
    pageTitle="Op rollen gebaseerd toegangsbeheer gebruiken in de Azure Portal | Microsoft Azure"
    description="Ga aan de slag met toegangsbeheer met op rollen gebaseerd toegangsbeheer in de Azure Portal. Gebruik roltoewijzingen om machtigingen in uw directory toe te wijzen."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/03/2016"
    ms.author="kgremban"/>

# Roltoewijzingen gebruiken voor het beheren van toegang tot uw Azure Active Directory-resources

Met op rollen gebaseerd toegangsbeheer (RBAC) beschikt u over geavanceerd toegangsbeheer voor Azure. Met RBAC kunt u alleen de toegangsrechten aan gebruikers verlenen die ze nodig hebben om hun taken uit te voeren. In dit artikel leest u hoe u met RBAC kunt werken in de Azure Portal. Zie [Wat is op rollen gebaseerd toegangsbeheer](role-based-access-control-what-is.md) als u meer informatie wilt over het beheren van toegang met RBAC.

## Toegang voor weergeven
U kunt zien wie toegang heeft tot een resource, resourcegroep of abonnement op de hoofdblade in de [Azure Portal](https://portal.azure.com). We willen bijvoorbeeld zien wie toegang heeft tot een van onze resourcegroepen:

1. Selecteer **Resourcegroepen** op de linkernavigatiebalk.  
    ![Resourcegroepen - pictogram](./media/role-based-access-control-configure/resourcegroups_icon.png)
2. Selecteer de naam van de resourcegroep op de blade **Resourcegroepen**.
3. Selecteer **Gebruikers** rechtsboven van de blade met resourcegroepen.  
    ![Gebruikers - pictogram](./media/role-based-access-control-configure/users_icon.png)
4. Op de blade **Gebruikers** worden alle gebruikers, groepen en toepassingen weergegeven aan wie/waaraan toegang is verleend tot de resourcegroep.  

    ![Schermafbeelding van de blade Gebruikers - overgenomen en toegewezen toegang](./media/role-based-access-control-configure/view-access.png)

U ziet dat aan bepaalde gebruikers de toegang is **Toegewezen** en dat voor andere gebruikers de toegang is **Overgenomen**. De toegang is specifiek toegewezen aan de resourcegroep of is overgenomen van een toewijzing aan het bovenliggende abonnement.

> [AZURE.NOTE] Klassieke abonnementsbeheerders en co-beheerders worden beschouwd als eigenaar van het abonnement in het nieuwe RBAC-model.


## Toegang voor toevoegen
U verleent toegang vanuit de resource en resourcegroep die of het abonnement dat het bereik van de roltoewijzing is.

1. Selecteer **Toevoegen** op de blade **Gebruikers**.  
    ![Toevoegen - pictogram](./media/role-based-access-control-configure/add_icon.png)  
2. Selecteer de rol die u wilt toewijzen op de blade **Rol selecteren**.
3. Selecteer de gebruiker, groep of toepassing in de directory aan wie/waaraan u toegang wilt verlenen. U kunt zoeken in de directory met weergavenamen, e-mailadressen en object-id's.  

    ![Schermafbeelding van de blade Gebruikers toevoegen - zoeken](./media/role-based-access-control-configure/grant-access2.png)

4. Selecteer **OK** om de toewijzing te maken. Met de pop-up **Gebruiker toevoegen** wordt de voortgang bijgehouden.  
    ![Schermafbeelding van de voortgangsbalk Gebruiker toevoegen](./media/role-based-access-control-configure/addinguser_popup.png)

Nadat u een roltoewijzing hebt toegevoegd, wordt deze weergegeven op de blade **Gebruikers**.

## Toegang verwijderen

1. Selecteer de roltoewijzing op de blade **Gebruikers**.
2. Selecteer **Verwijderen** op de blade Toewijzingdetails.  
    ![Verwijderen - pictogram](./media/role-based-access-control-configure/remove_icon.png)
3. Selecteer **Ja** om het verwijderen te bevestigen.  
    ![Schermafbeelding van de blade Gebruikers - verwijderen uit rol](./media/role-based-access-control-configure/remove-access1.png)

Overgenomen toewijzingen kunnen niet worden verwijderd. In de onderstaande afbeelding wordt de knop Verwijderen grijs weergegeven. Kijk ook naar het item **Toegewezen op**. Ga naar de resource die hier wordt weergegeven om de roltoewijzing te verwijderen.

![Schermafbeelding van de blade Gebruikers - bij overgenomen toegang is knop Verwijderen uitgeschakeld](./media/role-based-access-control-configure/remove-access2.png)

## Andere hulpprogramma's om toegang te beheren
U kunt rollen toewijzen en toegang beheren met Azure RBAC-opdrachten met andere hulpprogramma's dan de Azure Portal.  Volg de koppelingen voor meer informatie over de vereisten en om aan de slag te gaan met de Azure RBAC-opdrachten.

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure-opdrachtregelinterface](role-based-access-control-manage-access-azure-cli.md)
- [REST API](role-based-access-control-manage-access-rest.md)

## Volgende stappen
- [Een geschiedenisrapport voor gewijzigde toegang maken](role-based-access-control-access-change-history-report.md)
- Zie de [Ingebouwde RBAC-rollen](role-based-access-built-in-roles.md)
- Definieer uw eigen [Aangepaste rollen in Azure RBAC](role-based-access-control-custom-roles.md)



<!--HONumber=Jun16_HO2-->



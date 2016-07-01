<properties
   pageTitle="De directory voor uw Office 365-abonnement in Azure beheren | Microsoft Azure"
   description="Een directory voor een Office 365-abonnement  beheren met Azure Active Directory en de klassieke Azure-portal"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/26/2016"
   ms.author="curtand"/>

# De directory voor uw Office 365-abonnement in Azure beheren

In dit artikel wordt beschreven hoe u een directory beheert die is gemaakt voor een Office 365-abonnement, maar we gebruiken hiervoor de klassieke Azure-portal. U moet de servicebeheerder of medebeheerder van een Azure-abonnement zijn om u aan te melden bij de klassieke Azure-portal. Als u nog geen Azure-abonnement hebt

1. U kunt u aanmelden met het werk- of schoolaccount waarmee u zich aanmeldt bij Office 365.

![Invoervak voor e-mailadres](./media/active-directory-manage-o365-subscription/AAD_O365_01.png)

U kunt hier geen bijbehorend abonnement voor Azure vinden, maar u kunt op **Aanmelden voor Azure** klikken, waarna relevante informatie uit het Office 365-account vooraf wordt ingevuld in het aanmeldingsformulier. Hetzelfde account wordt standaard toegewezen aan de rol Servicebeheerder.

![Koppeling voor het aanmelden voor een Azure-abonnement](./media/active-directory-manage-o365-subscription/AAD_O365_02.png)

Nadat u het Azure-abonnement hebt voltooid, kunt u zich aanmelden bij de klassieke Azure-portal en hebt u toegang tot Azure-services. Klik op de Active Directory-extensie om de directory te beheren waarmee uw Office 365-gebruikers worden geverifieerd.

Als u al een Azure-abonnement hebt, is het beheer van een extra directory ook eenvoudig. Jaap Kleefstra heeft bijvoorbeeld een Office 365-abonnement voor Contoso.com. Hij heeft ook een Azure-abonnement waarbij hij zich heeft aangemeld met zijn Microsoft-account, jkleefstra@hotmail.com. In dit geval beheert hij twee directory's.

  Abonnement |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  Weergavenaam |  Contoso  |     De standaarddirectory voor Azure Active Directory (Azure AD)
  Domeinnaam  |  contoso.com  | jkleefstrahotmail.onmicrosoft.com

Hij wil de gebruikersidentiteiten in de Contoso-directory beheren terwijl hij is aangemeld bij Azure met zijn Microsoft-account, zodat hij Azure AD-functies zoals Multi-Factor Authentication kan inschakelen. Het proces wordt verduidelijkt in het volgende diagram.

![Diagram voor het beheren van twee onafhankelijke directory's](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

In dit geval zijn de twee directory's onafhankelijk van elkaar.

## Twee onafhankelijke directory's beheren
Jaap Kleefstra moet de volgende stappen uitvoeren om beide directory's te kunnen beheren terwijl hij is aangemeld bij Azure als jkleefstra@hotmail.com:

> [AZURE.NOTE]
> Deze stappen kunnen alleen worden uitgevoerd wanneer een gebruiker is aangemeld met een Microsoft-account. Als de gebruiker is aangemeld met een werk- of schoolaccount, is de optie **Bestaande directory gebruiken** niet beschikbaar omdat een werk- of schoolaccount alleen kan worden geverifieerd met de basisdirectory (de directory waarin het werk- of schoolaccount is opgeslagen en die het eigendom is van werk of school).

1.  Meld u aan bij de [klassieke Azure-portal](https://manage.windowsazure.com) als jkleefstra@hotmail.com.
2.  Klik achtereenvolgens op **Nieuw** > **App Services** > **Active Directory** > **Directory** > **Aangepast maken**.
3.  Klik op Bestaande directory gebruiken en schakel het selectievakje **Ik kan nu worden afgemeld** in.
4.  Meld u aan bij de klassieke Azure-portal als globale beheerder van Contoso.onmicrosoft.com (bijvoorbeeld jkleefstra@contoso.com).
5.  Wanneer u wordt gevraagd **De Contoso-directory gebruiken met Azure?**, klikt u op **Doorgaan**.
6.  Klik op **Nu afmelden**.
7.  Meld u aan bij de klassieke Azure-portal als jkleefstra@hotmail.com. De Contoso-directory en de Standaarddirectory worden weergegeven in de Active Directory-extensie.

Wanneer u deze stappen hebt uitgevoerd, is jkleefstra@hotmail.com een globale beheerder in de Contoso-directory.

## Resources beheren als globale beheerder
Stel nu dat Dena Vloet zich wil aanmelden bij de klassieke Azure-portal en websites en databaseresources wil beheren die zijn gekoppeld aan het Azure-abonnement voor jkleefstra@hotmail.com. Voordat ze dit kan doen, moet Jaap Kleefstra deze extra stappen uitvoeren:

1.  Zich aanmelden bij de [Klassieke Azure-portal](https://manage.windowsazure.com) met de servicebeheerdersaccount voor het Azure-abonnement (in dit voorbeeld jkleefstra@hotmail.com).
2.  Het abonnement overbrengen naar de Contoso-directory: klik achtereenvolgens op **Instellingen** > **Abonnementen** > selecteer het abonnement > **Directory bewerken** > selecteer **Contoso (Contoso.com)**. Als onderdeel van de overdracht worden werk- of schoolaccounts die medebeheerder zijn van het abonnement, verwijderd.
3.  Dena Vloet toevoegen als medebeheerder van het abonnement: klik achtereenvolgens op **Instellingen** > **Beheerders** > selecteer het abonnement > **Toevoegen** > typ **DenaVloet@Contoso.com**.

##Volgende stappen
Zie [De manier waarop een abonnement is gekoppeld aan een directory](active-directory-how-subscriptions-associated-directory.md) voor meer informatie over de relatie tussen abonnementen en directory's.



<!--HONumber=Jun16_HO2-->



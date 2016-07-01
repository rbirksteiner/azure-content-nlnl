<properties
   pageTitle="Aan de slag met Azure AD Privileged Identity Management | Microsoft Azure"
   description="Informatie over het beheren van bevoegde identiteiten met de Azure Active Directory Privileged Identity Management-toepassing in Azure-portal."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Aan de slag met Azure AD Privileged Identity Management


Met Azure Active Directory (AD) Privileged Identity Management kunt u bevoegde identiteiten en de toegang tot bronnen in Azure AD en andere Microsoft Online Services, zoals Office 365 of Microsoft Intune, beheren en controleren.  

In dit artikel leest u hoe u de PIM-app van Azure AD toevoegt aan uw Azure-portaldashboard.

## De Privileged Identity Management-toepassing toevoegen

Voordat u Azure AD Privileged Identity Management gebruikt, moet u de toepassing toevoegen aan uw Azure-portaldashboard.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als globale beheerder van uw directory.
2. Als uw organisatie meerdere directory's heeft, klikt u op uw gebruikersnaam rechtsboven in de Azure-portal en selecteert u de directory waarin u PIM gebruikt.
3. Selecteer het pictogram **Nieuw** in de linkernavigatiebalk.
4. Selecteer **Beveiliging en identiteit**.
5. Selecteer **Azure AD Privileged Identity Management**.
6. Schakel **Vastmaken aan dashboard** in en klik op de knop **Maken**. De Privileged Identity Management-toepassing wordt geopend.


Als u de eerste persoon bent die Azure AD Privileged Identity Management in uw directory gebruikt, wordt u via de [wizard Beveiliging](active-directory-privileged-identity-management-security-wizard.md) stapsgewijs begeleid bij de eerste toewijzing. Hierna wordt u automatisch de eerste **beveiligingsbeheerder** en **beheerder met bevoorrechte rol** van de directory. Alleen een beheerder met bevoorrechte rol heeft toegang tot deze toepassing om de toegang voor andere beheerders te beheren.  

Anders, als u aan een of meer rollen bent toegewezen door een andere beheerder met bevoorrechte rol, kunt u kiezen welke rol u wilt activeren. Als u zelf een beheerder met bevoorrechte rol bent, is ook de optie **Identiteiten beheren** beschikbaar.  


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Volgende stappen

Het [overzicht van Azure AD Privileged Identity Management](active-directory-privileged-identity-management-configure.md) bevat meer informatie over het beheren van beheerderstoegang in uw organisatie.

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]



<!--HONumber=Jun16_HO2-->



<properties
   pageTitle="Aan de slag met persoonlijke sjablonen | Microsoft Azure"
   description="Uw persoonlijke sjablonen toevoegen, beheren en delen met de Azure Portal, de Azure CLI of PowerShell."
   services="marketplace-customer"
   documentationCenter=""
   authors="VybavaRamadoss"
   manager="asimm"
   editor=""
   tags="marketplace, azure-resource-manager"
   keywords=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="vybavar"/>

# Aan de slag met persoonlijke sjablonen in de Azure Portal

Een [Azure Resource Manager](../resource-group-authoring-templates.md) sjabloon is een declaratief sjabloon om uw implementatie te definiëren. U kunt de resources bepalen die u wilt implementeren in een oplossing en de parameters en variabelen vaststellen waarmee u waarden kunt invoeren voor verschillende omgevingen. De sjabloon bestaat uit JSON en uitdrukkingen die u kunt gebruiken om waarden voor uw implementatie samen te stellen.

U kunt de nieuwe **sjabloon**mogelijkheden van de [Azure Portal](https://portal.azure.com) gebruiken in combinatie met de **Microsoft.Gallery** resourceprovider als een uitbreiding van de [Azure Marketplace](https://azure.microsoft.com/marketplace/). Zo kunnen gebruikers persoonlijke sjablonen maken, beheren en implementeren vanuit een persoonlijke bibliotheek.

Dit document is een handleiding voor het toevoegen, beheren en delen van een persoonlijk **sjabloon** via de Azure Portal.

## Richtlijnen

Met de volgende tips kunt u profiteren van de **sjablonen** wanneer u aan de slag gaat met uw oplossingen:

- Een **sjabloon** is een inkapselende resource met een Resource Manager-sjabloon en aanvullende metagegevens. Het werkt op dezelfde manier als een item in de Marketplace. Het belangrijkste verschil is dat het een persoonlijk item is, in plaats van een openbaar Marketplace-item.
- De **sjablonen**bibliotheek is zeer geschikt voor gebruikers die hun implementaties willen aanpassen.
- **Sjablonen** zijn handig voor gebruikers die een eenvoudige opslagplaats in Azure nodig hebben.
- Begin met een bestaand Resource Manager-sjabloon. Zoek sjablonen in [github](https://github.com/Azure/azure-quickstart-templates) of [exporteer een sjabloon](https://azure.microsoft.com/blog/export-template/) vanuit een bestaande resourcegroep.
- **Sjablonen** zijn gekoppeld aan de gebruiker die deze publiceert. De naam van de uitgever is zichtbaar voor alle gebruikers met leestoegang.
- **Sjablonen** zijn resources van de Resource Manager en kunnen na publicatie niet meer worden gewijzigd.

## Een sjabloonresource toevoegen

Er zijn twee manieren om een **sjabloon**resource te maken in de Azure Portal.

### Methode 1: Maak een nieuwe sjabloonresource vanuit een bestaande resourcegroep

1. Navigeer naar een bestaande resourcegroep in de Azure Portal. Klik op **Sjabloon exporteren** in **Instellingen**.
2. Zodra de Resource Manager-sjabloon is geëxporteerd, gebruikt u de knop **Sjabloon opslaan** om de sjabloon op te slaan in de  **Sjabloon**opslag. Meer informatie over het exporteren van sjablonen vindt u [hier](https://azure.microsoft.com/blog/export-template/).
<br /><br />
![Resourcegroep exporteren](media/rg-export-portal1.PNG)  <br />

3. Selecteer de opdrachtknop **Opslaan in sjabloon**.
<br /><br />

4. Voer de volgende informatie in:

    - Naam: naam van het sjabloonobject (LET OP: dit is een naam op basis van Azure Resource Manager. Alle naamsbeperkingen zijn van toepassing en de naam kan achteraf niet meer worden gewijzigd).
    - Beschrijving – korte samenvatting van de sjabloon.

    ![Sjabloon opslaan](media/save-template-portal1.PNG)  <br />

5. Klik op **Opslaan**.

    > [AZURE.NOTE] De blade Sjabloon exporteren geeft een melding weer wanneer de geëxporteerde Resource Manager-sjabloon fouten bevat. U kunt deze Resource Manager-sjabloon nog steeds opslaan bij de Sjablonen. Controleer en corrigeer alle problemen met de Resource Manager-sjabloon voordat u deze opnieuw implementeert.

### B. Methode 2: Een nieuwe sjabloonresource toevoegen door middel van bladeren

U kunt ook een nieuw **Sjabloon** maken en toevoegen met behulp van de knop Toevoegen in **Bladeren > sjablonen**. U moet een naam, beschrijving en JSON opgeven voor de Resource Manager-sjabloon.

![Sjabloon toevoegen](media/add-template-portal1.PNG)  <br />

> [AZURE.NOTE] Microsoft.Gallery is een op tenants gebaseerde resourceprovider van Azure. De sjabloonresource is gekoppeld aan de gebruiker die deze gemaakt heeft. Deze is niet gekoppeld aan een bepaald abonnement. U hoeft alleen een abonnement te kiezen wanneer u een sjabloon implementeert.

## Sjabloonresources bekijken

U kunt alle beschikbare **sjablonen** bekijken via **Bladeren > sjablonen**. Dit zijn zowel **sjablonen** die u hebt gemaakt als sjablonen met verschillende bevoegdheidsniveaus die met u zijn gedeeld. Meer informatie vindt u in het gedeelte [toegangscontrole](#access-control-for-a-tenant-resource-provider) hieronder.

![Sjabloon weergeven](media/view-template-portal1.PNG)  <br />

U kunt de gegevens van een **sjabloon** bekijken door op een item in de lijst te klikken.

![Sjabloon weergeven](media/view-template-portal2c.png)  <br />

## Een sjabloonresource bewerken

U kunt een **sjabloon** bewerken door met de rechtermuisknop te klikken op het item in de lijst Bladeren of op de knop Bewerken.

![Sjabloon bewerken](media/edit-template-portal1a.PNG)  <br />

U kunt de beschrijving of de tekst van de  Resource Manager-sjabloon bewerken. U kunt de naam niet bewerken, omdat dit de naam is van een Resource Manager-resource. Wanneer u de JSON van de Resource Manager-sjabloon bewerkt, zullen we dit valideren om er zeker van te zijn dat deze geldig is. Klik op **OK** en vervolgens op **Opslaan** om uw bijgewerkte sjabloon op te slaan.

![Sjabloon bewerken](media/edit-template-portal2a.PNG)  <br />

Wanneer het **sjabloon** is opgeslagen, wordt een bevestigingsbericht weergegeven.

![Sjabloon bewerken](media/edit-template-portal3b.png)  <br />

## Een sjabloonresource implementeren

U kunt elk **sjabloon** implementeren waarvoor u een **lees**machtiging heeft. Tijdens de implementatie wordt de standaardblade voor het implementeren van Azure-sjablonen geopend. Vul de parameterwaarden voor de Resource Manager-sjabloon in om verder te gaan met de implementatie.

![Sjabloon implementeren](media/deploy-template-portal1b.png)  <br />

## Een sjabloonresource delen

U kunt een **sjabloon**resource delen met anderen. Delen werkt op ongeveer dezelfde manier als [het toewijzen van rollen voor een resource in Azure](../active-directory/role-based-access-control-configure.md). De eigenaar van de **sjabloon** geeft andere gebruikers toestemming om te werken met een sjabloonresource. De persoon of groep personen met wie u het **sjabloon** heeft gedeeld kan de Resource Manager-sjabloon en de galerie-eigenschappen zien.

### Toegangsbeheer voor Microsoft.Gallery-resources

Rol | Machtigingen
---|----
Eigenaar | Heeft volledige controle over de sjabloonresource, onder andere het delen ervan
Lezer | Kan de sjabloonresource lezen en uitvoeren (implementeren)
Inzender | Kan de sjabloonresource bewerken en verwijderen. Gebruiker kan de sjabloon niet delen met anderen

Selecteer **Delen** in het bladeritem door te klikken met de rechtermuisknop of op de blade voor het weergeven van een specifiek item. Hiermee opent u een omgeving om het sjabloon te delen.

![Sjabloon delen](media/share-template-portal1a.png)  <br />

 U kunt nu een rol en een gebruiker of groep kiezen die u toegang wilt geven tot een bepaald **sjabloon**. De beschikbare rollen zijn Eigenaar, Lezer en Inzender. Meer informatie vindt u in het gedeelte [toegangscontrole](#access-control-for-a-tenant-resource-provider) hieronder.

![Sjabloon delen](media/share-template-portal2b.png)  <br />

![Sjabloon delen](media/share-template-portal3b.png)  <br />

Klik op **Selecteren** en vervolgens op **Ok**. U kunt nu de gebruikers of groepen bekijken die u aan de resource hebt toegevoegd.

![Sjabloon delen](media/share-template-portal4b.png)  <br />

> [AZURE.NOTE] U kunt een sjabloon alleen delen met gebruikers en groepen in dezelfde tenant van Azure Active Directory. Als u een sjabloon deelt met een e-mailadres dat zich niet in uw tenant bevindt, wordt een uitnodiging verstuurd waarin de gebruiker wordt gevraagd om als gast deel te nemen aan de tenant.

## Volgende stappen

- Zie [Sjablonen samenstellen](../resource-group-authoring-templates.md) voor meer informatie over het maken van Resource Manager-sjablonen
- Zie [Sjabloonfuncties](../resource-group-template-functions.md) voor inzicht in de functies die u in een Resource Manager-sjabloon kunt gebruiken.
- Zie [Best practices voor het ontwerpen van Azure Resource Manager-sjablonen](../best-practices-resource-manager-design-templates.md) voor meer informatie over het ontwerpen van uw sjablonen



<!--HONumber=Jun16_HO2-->



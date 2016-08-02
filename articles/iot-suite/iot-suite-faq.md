<properties
  pageTitle="Veelgestelde vragen over Azure IoT Suite | Microsoft Azure"
  description="Veelgestelde vragen over IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/09/2016"
  ms.author="araguila"/>
   
# Veelgestelde vragen over IoT Suite

### Wat is het verschil tussen het verwijderen van een resourcegroep in Azure Portal en op verwijderen te klikken in een vooraf geconfigureerde oplossing op azureiotsuite.com?

- Als u de vooraf geconfigureerde oplossing in [azureiotsuite.com][lnk azureiotsuite] verwijdert, verwijdert u alle resources die zijn ingericht toen u de vooraf geconfigureerde oplossing maakte; als u aanvullende resources hebt toegevoegd aan de resourcegroep, worden deze ook verwijderd. 

- Als u de resourcegroep in [Azure Portal][lnk-azure-portal] verwijdert, verwijdert u alleen de resources in die resourcegroep; u zult ook de toepassing Azure Active Directory moeten verwijderen die is gekoppeld aan de vooraf geconfigureerde oplossing in de [klassieke Azure-portal][lnk-classic-portal].

### Hoeveel exemplaren van DocumentDB kan ik inrichten met een abonnement?

Vijf. U kunt een [Azure-ondersteuningsticket][link-azuresupportticket] maken om deze limiet te verhogen, maar standaard kunt u slechts vijf exemplaren van DocumentDB per abonnement inrichten. Als gevolg hiervan kunt u met een bepaald abonnement maximaal vijf vooraf geconfigureerde oplossingen voor externe controle inrichten.

### Hoeveel gratis Bing Kaarten-API's kan ik inrichten met een abonnement?

Twee. U kunt slechts twee gratis Bing Kaarten-API's met een abonnement maken. De externe bewakingsoplossing is standaard ingericht met een gratis Bing Kaarten-API . Als gevolg hiervan kunt u met een abonnement waaraan geen wijzigingen zijn aangebracht maximaal twee externe bewakingsoplossingen inrichten.

### Ik heb een implementatie van een externe bewakingsoplossing met een statische kaart, hoe voeg ik een interactieve Bing-kaart toe? 
1. Download uw Bing Kaarten-API voor Enterprise-querysleutel in de [Azure portal][lnk-azure-portal]: 
 1. Navigeer naar de resourcegroep waar uw Bing Kaarten-API voor Enterprise zich in de [Azure-portal][lnk-azure-portal] bevindt.
 2. Klik op Alle instellingen en vervolgens op Sleutelbeheer. 
 3. U ziet twee sleutels: hoofdsleutel en querysleutel. Kopieer de waarde voor querysleutel.

     > [AZURE.NOTE] U hebt geen Bing Kaarten-API voor Enterprise-account? Maak er een in [Azure Portal][lnk-azure-portal] door te klikken op + Nieuw, te zoeken naar Bing Kaarten-API voor Enterprise en de aanwijzingen te volgen om een account te maken.

2. Haal de meest recente code op uit [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].

3. Voer een lokale implementatie of een cloudimplementatie uit met behulp van de richtlijnen voor de implementatie vanaf een opdrachtregel, in de map /docs/ in de bibliotheek. 

4. Nadat u een lokale implementatie of een cloudimplementatie hebt uitgevoerd, zoekt u in de hoofdmap naar het bestand *.user.config dat is gemaakt tijdens de implementatie. Open dit bestand in een teksteditor. 

5. Wijzig de volgende regel zodat die de waarde bevat die u voor uw querysleutel hebt gekopieerd: 
   
  `<setting name="MapApiQueryKey" value="" />`

### Kan ik een vooraf geconfigureerde oplossing maken als ik Microsoft Azure voor DreamSpark heb?
Op dit moment kunt u een vooraf geconfigureerde oplossing maken met een [Microsoft Azure voor DreamSpark][lnk dreamspark]-account. U kunt echter in een paar minuten een [gratis proefaccount voor Azure][lnk 30daytrial] maken. Hiermee kunt u een vooraf geconfigureerde oplossing maken.

### Hoe verwijder ik een AAD-tenant?

Zie de blog van Eric Golpe [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant] (Stapsgewijze instructies voor het verwijderen van een Azure AD-tenant).

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[lnk-azure-portal]: https://portal.azure.com
[lnk azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk 30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=Jun16_HO2-->



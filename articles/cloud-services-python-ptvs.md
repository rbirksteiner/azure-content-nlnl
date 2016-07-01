<properties
    pageTitle="Python-web- en -werkrollen met Python-tools 2.2 voor Visual Studio | Microsoft Azure"
    description="Overzicht van het gebruik van Python-tools voor Visual Studio voor het maken van Azure Cloud Services, met inbegrip van webrollen en werkrollen."
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/30/2015"
    ms.author="adegeo"/>




# Python-web- en -werkrollen met Python-tools 2.2 voor Visual Studio

Dit artikel biedt een overzicht van het gebruik van Python-web- en -werkrollen met [Python-tools voor Visual Studio][].

## Vereisten

 - Visual Studio 2013 of 2015
 - [Python-tools 2.2 voor Visual Studio][] (PTVS)
 - [Azure SDK-tools voor VS 2013][] of [Azure SDK-tools voor VS 2015][]
 - [Python 2.7 32-bits][] of [Python 3.4 32-bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Wat zijn Python-web- en -werkrollen?

Azure biedt drie compute-modellen voor het uitvoeren van toepassingen: [Web Apps in Azure App Service][uitvoeringsmodel voor websites], [Azure Virtual Machines][uitvoeringsmodel voor virtuele machines] en [Azure Cloud Services][uitvoeringsmodel voor cloudservices]. Alle drie modellen ondersteunen Python. Cloud Services, die web- en werkrollen bevatten, bieden *Platform as a Service (PaaS)*. Binnen een cloudservice biedt een webrol een speciale IIS-webserver (Internet Information Services) voor het hosten van front-end webtoepassingen. Een werkrol kan asynchrone langlopende of permanente taken uitvoeren onafhankelijk van de interactie of invoer van de gebruiker.

Zie [Wat is een cloudservice?] voor meer informatie.

> [AZURE.NOTE] *Wilt u een eenvoudige website bouwen?*
Als uw scenario slechts een ongecompliceerde website-front-end omvat, kunt u overwegen de eenvoudige functie Web Apps in Azure App Service te gebruiken. U kunt vervolgens gemakkelijk upgraden naar een cloudservice naarmate uw website groeit en de vereisten veranderen. Zie het <a href="/develop/python/">Python Developer Center</a> voor artikelen over ontwikkeling met de functie Web Apps in Azure App Service.
<br />


## Een project maken

In Visual Studio kunt u **Azure Cloud Service** selecteren in het dialoogvenster **Nieuw project** onder **Python**.

![Het dialoogvenster Nieuw project](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

U kunt nieuwe web- en werkrollen maken in de Azure Cloud Service-wizard.

![Het dialoogvenster Azure Cloud Service](./media/cloud-services-python-ptvs/new-service-wizard.png)

De werkrolsjabloon wordt geleverd met standaardcode om verbinding maken met een Azure-opslagaccount of Azure Service Bus.

![Cloudserviceoplossing](./media/cloud-services-python-ptvs/worker.png)

U kunt op elk gewenst moment web- of werkrollen toevoegen aan een bestaande cloudservice.  U kunt bestaande projecten aan uw oplossing toevoegen of nieuwe maken.

![De opdracht Rol toevoegen](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

Uw cloudservice kan rollen bevatten die zijn geïmplementeerd in verschillende talen.  U kunt bijvoorbeeld een Python-webrol hebben die is geïmplementeerd met Django, met Python of met C#-werkrollen.  U kunt eenvoudig communiceren tussen uw rollen met behulp van de Service Bus-wachtrijen of opslagwachtrijen.

## Lokaal uitvoeren

Als u uw cloudserviceproject instelt als opstartproject en op F5 drukt, wordt de cloudservice in de lokale Azure-emulator uitgevoerd.

Hoewel PTVS opstarten in de emulator ondersteunt, werkt foutopsporing (bijvoorbeeld onderbrekingspunten) niet.

Voor foutopsporing in uw web- en werkrollen kunt u het rolproject instellen als opstartproject en daarop foutopsporing uitvoeren.  U kunt ook meerdere opstartprojecten instellen.  Klik met de rechtermuisknop op de oplossing en selecteer vervolgens **Opstartprojecten instellen**.

![Eigenschappen van opstartprojecten in de oplossing](./media/cloud-services-python-ptvs/startup.png)

## Publiceren naar Azure

Om te publiceren, klikt u met de rechtermuisknop op het cloudserviceproject in de oplossing en selecteert u vervolgens **Publiceren**.

![Aanmelden voor publiceren in Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Selecteer op de instellingenpagina de cloudservice waarnaar u wilt publiceren.

![Publicatie-instellingen in Microsoft Azure](./media/cloud-services-python-ptvs/publish-settings.png)

U kunt een nieuwe cloudservice maken als u er nog geen heeft.

![Het dialoogvenster Cloudservice maken](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Het is ook handig om op de computer(s) een verbinding via Extern bureaublad in te schakelen voor het opsporen van fouten.

![Het dialoogvenster Extern bureaublad-configuratie](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Klik op **Publiceren** wanneer u klaar bent met het configureren van instellingen.

De voortgang wordt gedeeltelijk weergegeven in het uitvoervenster. Vervolgens ziet u het venster Activiteitenlogboek van Microsoft Azure.

![Het venster Activiteitenlogboek van Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

De implementatie duurt enkele minuten. Daarna worden uw web- en/of werkrollen uitgevoerd in Azure.

## Volgende stappen

Zie de documentatie bij PTVS voor meer informatie over het werken met web- en werkrollen in Python-tools voor Visual Studio:

- [Cloudserviceprojecten][]

Zie de volgende artikelen voor meer informatie over het gebruik van Azure-services via uw web- en werkrollen, zoals het gebruik van Azure Storage of Service Bus.

- [Blob-service][]
- [Tabelservice][]
- [Queue-service][]
- [Service Bus-wachtrijen][]
- [Service Bus-onderwerpen][]


<!--Link references-->

[Wat is een cloudservice?]: ./cloud-services/cloud-services-choose-me.md
[uitvoeringsmodel voor websites]: ./app-service-web/app-service-web-overview.md
[uitvoeringsmodel voor virtuele machines]: ./virtual-machines/virtual-machines-windows-about.md
[uitvoeringsmodel voor cloudservices]: ./cloud-services/cloud-services-choose-me.md
[Python Developer Center]: /develop/python/

[Blob-service]: ./storage/storage-python-how-to-use-blob-storage.md
[Queue-service]: ./storage/storage-python-how-to-use-queue-storage.md
[Tabelservice]: ./storage/storage-python-how-to-use-table-storage.md
[Service Bus-wachtrijen]: ./service-bus/service-bus-python-how-to-use-queues.md
[Service Bus-onderwerpen]: ./service-bus/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Python-tools voor Visual Studio]: http://aka.ms/ptvs
[Documentatie bij Python-tools voor Visual Studio]: http://aka.ms/ptvsdocs
[Cloudserviceprojecten]: http://go.microsoft.com/fwlink/?LinkId=624028
[Python-tools 2.2 voor Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK-tools voor VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK-tools voor VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 32-bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bits]: http://go.microsoft.com/fwlink/?LinkId=517191



<!--HONumber=Jun16_HO2-->



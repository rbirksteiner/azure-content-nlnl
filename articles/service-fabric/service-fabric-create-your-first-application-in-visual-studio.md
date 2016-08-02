<properties
   pageTitle="Uw eerste Service Fabric-toepassing in Visual Studio maken | Microsoft Azure"
   description="Een Service Fabric-toepassing met Visual Studio maken, implementeren en foutopsporing uitvoeren"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/27/2016"
   ms.author="ryanwi"/>

# Uw eerste Azure Service Fabric-toepassing in Visual Studio maken

De Service Fabric SDK bevat een invoegtoepassing voor Visual Studio voor sjablonen en hulpprogramma's om Service Fabric-toepassingen te maken, te implementeren en foutopsporing uit te voeren. In dit onderwerp vindt u meer informatie over het maken van uw eerste toepassing in Visual Studio.

## Vereisten

Voordat u begint, zorgt u ervoor dat u [uw ontwikkelingsomgeving hebt ingesteld](service-fabric-get-started.md).

## Video-overzicht

De volgende video leidt u door de stappen in deze zelfstudie:

>[AZURE.VIDEO creating-your-first-service-fabric-application-in-visual-studio]

## De toepassing maken

Een Service Fabric-toepassing kan een of meer services bevatten, elk met een specifieke functie met betrekking tot het leveren van de functionaliteit van de toepassing. Met de wizard Nieuw project kunt u een toepassingsproject maken, samen met uw eerste serviceproject. U kunt later meer services toevoegen.

1. Visual Studio starten als beheerder.

2. Klik op **Bestand > Nieuw Project > Cloud > Service Fabric-toepassing**.

3. Geef een naam op voor de toepassing en klik op **OK**.

    ![Dialoogvenster voor nieuw project in Visual Studio][1]

4. Op de volgende pagina wordt u gevraagd het eerste servicetype te kiezen dat u wilt opnemen in uw toepassing. In deze zelfstudie houden we het eenvoudig door **Stateful** te kiezen. Geef het servicetype een naam en klik op **OK**.

    ![Dialoogvenster voor nieuwe service in Visual Studio][2]

    >[AZURE.NOTE] Zie [Een raamwerk kiezen](service-fabric-choose-framework.md) voor meer informatie over de opties.

    Visual Studio maakt het toepassingsproject en het stateful service-project en geeft deze weer in Solution Explorer.

    ![Solution Explorer na het maken van de stateful service-toepassing][3]

    Het toepassingsproject bevat geen directe code. Het verwijst naar een reeks serviceprojecten. Daarnaast bevat het project drie andere typen inhoud:

    - **Profielen publiceren**: deze optie wordt gebruikt voor het beheren van voorkeuren voor tools voor verschillende omgevingen.

    - **Scripts**: bevat een PowerShell-script voor het implementeren van/bijwerken van uw toepassing. Dit script wordt achter de schermen gebruikt door Visual Studio en kan rechtstreeks worden aangeroepen op de opdrachtregel.

    - **Definitie van toepassing**: bevat het toepassingsmanifest onder *ApplicationPackageRoot* en de bijbehorende parameter voor toepassingsbestanden onder *ApplicationParameters* die de toepassing definiëren. U kunt deze toepassing configureren voor een bepaalde omgeving.

    Zie [Aan de slag met Reliable Services](service-fabric-reliable-services-quick-start.md) voor een overzicht van de inhoud van het serviceproject.

## De toepassing implementeren en fouten opsporen in de toepassing

Nu u een toepassing hebt, kunt u deze uitvoeren.

1. Druk op F5 in Visual Studio om de toepassing voor foutopsporing te implementeren.

    >[AZURE.NOTE] Dit duurt even de eerste keer. Visual Studio maakt een lokaal cluster. Een lokaal cluster voert dezelfde platformcode uit als op een cluster met meerdere machines, alleen op één computer. U ziet de status van het maken van het cluster in het venster Visual Studio.

    Als het cluster gereed is, ontvangt u een melding van het lokale toepassingsbewakingsprogramma voor het systeemvak dat is opgenomen in de SDK.

    ![Melding van het systeemvak van het lokale cluster][4]

2. Als de toepassing wordt gestart, toont Visual Studio automatisch de diagnostische logboeken waarin u de uitvoer van de service kunt bekijken.

    ![Diagnostische logboeken][5]

    Als het gaat om een stateful servicesjabloon geeft het bericht een itemwaarde weer die wordt verhoogd in de methode `RunAsync` van MyStatefulService.cs.

3. Vouw een van de gebeurtenissen uit voor meer informatie, zoals het knooppunt waarop de code wordt uitgevoerd. In dit geval is het _Node_2. Dit kan anders zijn op uw machine.

    ![Details van de gebeurtenissenviewer][6]

    Het lokale cluster bevat vijf knooppunten die worden gehost op een enkele computer. Het lijkt op een cluster met vijf knooppunten, waarbij de knooppunten zich op afzonderlijke computers bevinden. We bekijken een van de knooppunten op het lokale cluster om het verlies van gegevens van een machine te simuleren en tegelijkertijd te oefenen met de foutopsporingsfunctie.

    >[AZURE.NOTE] De diagnostische gebeurtenissen van de toepassing die worden gegenereerd door de projectsjabloon, maken gebruik van de klasse `ServiceEventSource`. Zie [Services lokaal bewaken en onderzoeken](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) voor meer informatie.

4. Zoek de klasse in uw serviceproject die is afgeleid van StatefulService (bijvoorbeeld MyStatefulService) en stel een onderbrekingspunt in op de eerste regel van de `RunAsync`-methode.

    ![Onderbrekingspunt in de stateful service RunAsync-methode][7]

5. Klik met de rechtermuisknop op het systeemvak Lokaal clusterbeheer en lies **Lokaal cluster beheren** om Service Fabric Explorer te starten.

    ![Start Service Fabric Explorer vanuit Lokaal clusterbeheer][systray-launch-sfx]

    Service Fabric Explorer biedt een visuele representatie van een cluster, met inbegrip van een set gedistribueerde toepassingen die zijn geïmplementeerd en de set van fysieke knooppunten waaruit het bestaat. Zie [Uw cluster visualiseren](service-fabric-visualizing-your-cluster.md) voor meer informatie over Service Fabric Explorer.

6. Vouw in het linkerdeelvenster **Cluster > Knooppunten** uit en zoek het knooppunt waarop uw code wordt uitgevoerd.

7. Klik op **Acties > uitschakelen (opnieuw opstarten)** simuleren een machine opnieuw te starten. (U kunt deze stap ook uitvoeren in een contextmenu in de lijstweergave van knooppunten in het linkerdeelvenster door te klikken op de drie puntjes.)

    ![Een knooppunt in Service Fabric Explorer stoppen][sfx-stop-node]

    U ziet uw onderbrekingspunt in Visual Studio, terwijl de berekening die u uitvoerde op één knooppunt naadloos wordt overgenomen door een ander.

8. Terugkeren naar de details van de gebeurtenisviewer en de berichten bekijken. Houd er rekening mee dat het item is voortgezet en oplopend in stappen, zelfs als de gebeurtenissen daadwerkelijk afkomstig zijn uit een ander knooppunt.

    ![Details van de gebeurtenissenviewer na een failover][diagnostic-events-viewer-detail-post-failover]

### Opschonen

  Voordat u afsluit, moet u weten dat het lokale cluster erg puur is. Zelfs nadat u het foutopsporingsprogramma stopt en Visual Studio sluit, blijven uw toepassingen op de achtergrond actief. Afhankelijk van de aard van uw apps maakt deze activiteit op de achtergrond behoorlijk aanspraak op uw computer. U hebt verschillende mogelijkheden om dit te beheren:

  1. Als u een afzonderlijke toepassing en alle bijbehorende gegevens wilt verwijderen, gebruikt u **Toepassing verwijderen** in Service Fabric Explorer via het menu **ACTIES** of het contextmenu in de lijstweergave van de toepassing in het linkerdeelvenster.

    ![Een toepassing verwijderen in Service Fabric Explorer][sfe-delete-application]

  2. Na het verwijderen van de toepassing uit het cluster vervolgens kunt u **Type verwijderen** kiezen voor de toepassing. Hiermee wordt het pakket van de toepassing van de installatiekopie van het cluster verwijderd.
  3. Als u het cluster wilt verwijderen, maar de toepassingsgegevens en -traceringen wilt behouden, klikt u op **Lokaal cluster stoppen** in het systeemvak.

  4. Als u het cluster volledig wilt verwijderen, klikt u op **Lokaal cluster verwijderen** in het systeemvak van de app. Als u dit doet, verloopt de implementatie mogelijk langzamer wanneer u op F5 in Visual Studio klikt. Gebruik deze optie alleen als u het lokale cluster voor een tijdje niet gaat gebruiken of als geen resources hoeft vrij te maken.



## Volgende stappen

<!--
Temporarily removing this link because we have removed the ASP.NET template.

 - [See how you can expose your services to the Internet with a web service front end](service-fabric-add-a-web-frontend.md)
-->
- [Informatie over het maken van een cluster in Azure](service-fabric-cluster-creation-via-portal.md)
- [Meer informatie over Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Probeer een service te maken op basis van het model voor Reliable Actors](service-fabric-reliable-actors-get-started.md)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-knooppunt]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-starten-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png
[toepassing van sfe verwijderen]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-delete-application.png



<!--HONumber=Jun16_HO2-->



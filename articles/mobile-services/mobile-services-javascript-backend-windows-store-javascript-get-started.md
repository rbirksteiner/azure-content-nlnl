<properties
    pageTitle="Aan de slag met Mobile Services voor Windows Store JavaScript-apps | Azure Mobile Services"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor ontwikkeling van Windows Store in JavaScript."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="javascript"
    ms.topic="get-started-article"
    ms.date="03/06/2016"
    ms.author="glenga"/>

# Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Voor de corresponderende Mobile Apps-versie van dit onderwerp raadpleegt u [Een Windows-app maken in Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).  

Deze zelfstudie laat zien hoe u met Azure Mobile Services een cloudgebaseerde back-endservice toevoegt aan een Windows Store JavaScript-app. In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige *takenlijst*-app die app-gegevens opslaat in de nieuwe mobiele service. De mobiele service die u maakt, gebruikt JavaScript voor bedrijfslogica op de server. 

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F) voor meer informatie.
* [Visual Studio 2013 Express voor Windows]

## Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Een nieuwe Windows Store-app maken

Nadat u uw mobiele service hebt gemaakt, kunt u in de klassieke Azure Portal een eenvoudige Quick Start volgen om een nieuwe Windows Store 8.1 JavaScript-app te maken die verbinding maakt met uw mobiele service.

1.  Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.


2. Klik op het tabblad Quick Start op **Windows** onder **Platform kiezen** en vouw **Een nieuwe Windows Store-app maken** uit.

3. Als u dit nog niet hebt gedaan, kunt u [Visual Studio 2013][Visual Studio 2013 Express voor Windows] downloaden en installeren op uw lokale computer of virtuele machine.

4. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.

5. Selecteer onder **Uw app downloaden en uitvoeren** een taal voor uw app en klik vervolgens op **Downloaden**.

    Hiermee downloadt u het project voor de *takenlijst*-voorbeeldtoepassing die is verbonden met uw mobiele service. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## De Windows-app uitvoeren

De laatste fase van deze zelfstudie is het bouwen en uitvoeren van uw nieuwe app.

1. Blader naar de locatie waar u de gecomprimeerde projectbestanden hebt opgeslagen, vouw de bestanden op uw computer uit en open het oplossingsbestand in Visual Studio.

2. Druk op de toets **F5** om het project te herbouwen en de app te starten.

3. Typ in **Nieuwe taak invoeren** een stukje zinvolle tekst in de app, zoals *Voltooi de zelfstudie*, en klik op **Toevoegen**.

    Er wordt nu een POST-aanvraag verzonden naar de nieuwe mobiele service die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. De items die in de tabel zijn opgeslagen, worden door de mobiele service geretourneerd. Vervolgens worden de gegevens weergegeven in de tweede kolom in de app.

4. (Optioneel) Voer de app opnieuw uit. Na het starten van de app worden de gegevens die in de vorige stap zijn opgeslagen, vanuit de mobiele service geladen.
 
4. Klik in de [klassieke Azure Portal] op het tabblad **Gegevens** en klik vervolgens op de tabel **Taken**.

    U kunt nu door de gegevens bladeren die door de app in de tabel zijn ingevoegd.

>[AZURE.NOTE] U kunt de code die toegang heeft tot uw mobiele service, bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het bestand default.js.

## Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u nagaan hoe u de [Mobile Services-client voor HTML/JavaScript](mobile-services-html-how-to-use-client-library.md) gebruikt. 

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Aan de slag met Mobile Services]:#getting-started
[Een nieuwe mobiele service maken]:#create-new-service
[Het mobiele service-exemplaar definiëren]:#define-mobile-service-instance
[Volgende stappen]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Visual Studio 2013 Express voor Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[klassieke Azure Portal]: https://manage.windowsazure.com/



<!--HONumber=Jun16_HO2-->



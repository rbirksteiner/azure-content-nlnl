<properties
    pageTitle="Aan de slag met Mobile Services voor Windows Store-apps (C#) | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor ontwikkeling van Windows Store in C#."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="get-started-article" 
    ms.date="05/11/2016"
    ms.author="glenga"/>

# <a name="getting-started"> </a>Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Voor de corresponderende Mobile Apps-versie van dit onderwerp raadpleegt u [Een Windows-app maken](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).

Deze zelfstudie laat zien hoe u met Azure Mobile Services een cloudgebaseerde back-endservice toevoegt aan een universele Windows-app. Bij universele Windows-app-oplossingen horen ook projecten voor apps voor Windows Store 8.1 en Windows Phone Store 8.1, evenals een gemeenschappelijk gedeeld project. Zie voor meer informatie [Universele Windows-apps bouwen die zijn gericht op Windows en Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige *takenlijst*-app die app-gegevens opslaat in de nieuwe mobiele service. De mobiele service die u maakt, gebruikt JavaScript voor bedrijfslogica op de server. Voor het maken van een mobiele service waarmee u uw bedrijfslogica in de ondersteunde .NET-talen met Visual Studio op de server kunt schrijven, raadpleegt u de .NET-back-endversie van dit onderwerp.

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure. U krijgt dan maximaal 10 gratis mobiele services, die u ook na het einde van de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-javascript-backend-windows-store-javascript-get-started%2F) voor meer informatie.
* [Visual Studio 2013 Express voor Windows]

## Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Een nieuwe universele Windows-app maken

Nadat u uw mobiele service hebt gemaakt, kunt u in de klassieke Azure Portal een eenvoudige Quick Start volgen om een nieuwe universele Windows-app te maken of een bestaande Windows Store- of Windows Phone-app zodanig te wijzigen dat deze verbinding maakt met uw mobiele service.

In deze sectie maakt u een nieuwe universele Windows-app die is verbonden met uw mobiele service.

1.  Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.


2. Klik op het tabblad Quick Start op **Windows** onder **Platform kiezen** en vouw **Een nieuwe Windows Store-app maken** uit.

    U ziet nu de drie eenvoudige stappen om een Windows Store-app te maken die is verbonden met uw mobiele service.

    ![Mobile Services-stappen voor een snelle start](./media/mobile-services-javascript-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Als u dit nog niet hebt gedaan, kunt u [Visual Studio 2013 Express voor Windows] downloaden en installeren op uw lokale computer of virtuele machine.

4. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.

5. Selecteer onder **Uw app downloaden en uitvoeren** een taal voor uw app en klik vervolgens op **Downloaden**.

    Hiermee downloadt u het project voor de *takenlijst*-voorbeeldtoepassing die is verbonden met uw mobiele service. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## De Windows-app uitvoeren

[AZURE.INCLUDE [mobile-services-javascript-backend-run-app](../../includes/mobile-services-javascript-backend-run-app.md)]

>[AZURE.NOTE]U kunt de code die toegang heeft tot uw mobiele service, bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het bestand MainPage.xaml.cs.

## Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u nagaan hoe u in Mobile Services ook diverse andere belangrijke taken uitvoert:

* [Aan de slag met het offline synchroniseren van gegevens] Ontdek hoe u de offline gegevenssynchronisatie gebruikt om uw app responsief en robuust te maken.

* [Verificatie toevoegen aan uw Mobile Services-app ][Aan de slag met verificatie]  
  Ontdek hoe u gebruikers van uw app verifieert met een id-provider.

* [Pushmeldingen toevoegen aan uw app][Aan de slag met pushmeldingen]  
  Ontdek hoe u een zeer eenvoudige pushmelding verzendt naar uw app.

* [De .NET-clientbibliotheek gebruiken](mobile-services-dotnet-how-to-use-client-library.md)  
 Ontdek hoe u een query voor de mobiele service uitvoert, met gegevens werkt en aangepaste API's gebruikt.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Aan de slag met Mobile Services]:#getting-started
[Een nieuwe mobiele service maken]:#create-new-service
[Het mobiele service-exemplaar definiëren]:#define-mobile-service-instance
[Volgende stappen]:#next-steps

<!-- Images. -->



<!-- URLs. -->
[Aan de slag met het offline synchroniseren van gegevens]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Aan de slag met verificatie]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md
[Aan de slag met pushmeldingen]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-push.md
[Visual Studio 2013 Express voor Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[klassieke Azure Portal]: https://manage.windowsazure.com/
 


<!--HONumber=Jun16_HO2-->



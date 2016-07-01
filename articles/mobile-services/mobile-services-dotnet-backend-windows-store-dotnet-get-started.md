<properties
    pageTitle="Aan de slag met Mobile Services voor universele Windows-apps | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor ontwikkeling van universele Windows-apps in C#."
    services="mobile-services"
    documentationCenter="windows"
    authors="ggailey777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/11/2016"
    ms.author="glenga"/>


# <a name="getting-started"> </a>Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Voor de corresponderende Mobile Apps-versie van dit onderwerp raadpleegt u [Een Windows-app maken in Mobile Apps](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started.md).

Deze zelfstudie laat zien hoe u met Azure Mobile Services een cloudgebaseerde back-endservice toevoegt aan een universele Windows-app. Bij universele Windows-app-oplossingen horen ook projecten voor apps voor Windows Store 8.1 en Windows Phone Store 8.1, evenals een gemeenschappelijk gedeeld project. Zie voor meer informatie [Universele Windows-apps bouwen die zijn gericht op Windows en Windows Phone](http://msdn.microsoft.com/library/windows/apps/xaml/dn609832.aspx).

In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige *takenlijst*-app die app-gegevens opslaat in de nieuwe mobiele service. De mobiele service die u gaat maken, gebruikt de ondersteunde .NET-talen met behulp van Visual Studio voor bedrijfslogica op de server en voor het beheren van de mobiele service. Voor het maken van een mobiele service waarmee u uw bedrijfslogica op de server in JavaScript kunt schrijven, raadpleegt u de JavaScript back-endversie van dit onderwerp.

>[AZURE.NOTE]In dit onderwerp wordt beschreven hoe u een nieuw mobiele-serviceproject-app en universele Windows-app maakt met behulp van de klassieke Azure Portal. Met behulp van Visual Studio 2013 Update 3 kunt u ook een nieuw mobiele-serviceproject toevoegen aan een bestaande Visual Studio-oplossing. Zie voor meer informatie [Mobile Services toevoegen aan een bestaande app](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md).

>Zie voor informatie over het toevoegen van een mobiele service aan een app-project voor Windows Phone 8.0 of Windows Phone Silverlight 8.1, [Mobile Services toevoegen aan een bestaande Windows Phone-app](mobile-services-windows-phone-get-started-data.md).

[AZURE.INCLUDE [mobile-services-windows-universal-get-started](../../includes/mobile-services-windows-universal-get-started.md)]

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een actief Azure-account. Als u geen account hebt, kunt u zich aanmelden voor een proefversie van Azure. U krijgt dan maximaal 10 gratis mobiele services, die u ook na het einde van de proefperiode kunt blijven gebruiken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started%2F) voor meer informatie.
* [Visual Studio 2013].

## Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Een nieuwe universele Windows-app maken

Nadat u uw mobiele service hebt gemaakt, kunt u in de klassieke Azure Portal een eenvoudige Quick Start volgen om een nieuwe app te maken of een bestaande app zodanig te wijzigen dat deze verbinding maakt met uw mobiele service.

In deze sectie maakt u een nieuwe universele Windows-app die is verbonden met uw mobiele service.

1. Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

2. Klik op het tabblad Quick Start op **Windows** onder **Platform kiezen** en vouw **Een nieuwe Windows Store-app maken** uit.

    U ziet nu de drie eenvoudige stappen om een Windows Store-app te maken die is verbonden met uw mobiele service.

    ![Mobile Services-stappen voor een snelle start](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started/mobile-quickstart-steps.png)

3. Als u dit nog niet hebt gedaan, kunt u [Visual Studio 2013] downloaden en installeren op uw lokale computer of virtuele machine.

4. Selecteer onder **Uw app en service downloaden en lokaal uitvoeren** een taal voor uw Windows Store-app en klik vervolgens op **Downloaden**.

    Hiermee downloadt u een oplossing die projecten bevat voor zowel de mobiele service als voor de _takenlijst_-voorbeeldtoepassing die is verbonden met uw mobiele service. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## De app testen op de lokale mobiele service

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-dotnet](../../includes/mobile-services-dotnet-backend-test-local-service-dotnet.md)]

>[AZURE.NOTE]U kunt de code die toegang heeft tot uw mobiele service, bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het bestand MainPage.xaml.cs.


## Uw mobiele service publiceren

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


<ol start="4">
<li><p>Open in het codeproject Gedeeld het bestand App.xaml.cs, zoek de code die een <a href="http://msdn.microsoft.com/library/Windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx" target="_blank">MobileServiceClient</a>-exemplaar maakt, commentarieer de code die deze client aanmaakt met behulp van <em>localhost</em> uit en herstel de code die de client aanmaakt met behulp van de externe URL van de mobiele service. Deze ziet er als volgt uit:</p>

        <pre><code>public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://todolist.azure-mobile.net/",
            "XXXX-APPLICATION-KEY-XXXXX");</code></pre>

    <p>The client will now access the mobile service published to Azure.</p></li>
</ol>

## De app testen op de mobiele service die wordt gehost in Azure

Nu de mobiele service is gepubliceerd en de client is verbonden met de externe mobiele service die wordt gehost in Azure, kunnen we de app uitvoeren en Azure gebruiken voor de opslag van items.

[AZURE.INCLUDE [mobile-services-windows-universal-test-app](../../includes/mobile-services-windows-universal-test-app.md)]


## Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u nagaan hoe u in Mobile Services ook diverse andere belangrijke taken uitvoert:

* [Mobile Services toevoegen aan een bestaande app][Aan de slag met gegevens]
  <br/>Ontdek hoe u gegevens opslaat en gegevensquery‘s maakt met behulp van Mobile Services.

* [Aan de slag met het offline synchroniseren van gegevens]
  <br/>Ontdek hoe u offline gegevenssynchronisatie kunt gebruiken om uw app responsief en robuust te maken.

* [Verificatie toevoegen aan uw Mobile Services-app ][Aan de slag met verificatie]
  <br/>Ontdek hoe u gebruikers van uw app verifieert met een id-provider.

* [Pushmeldingen toevoegen aan uw app][Aan de slag met pushmeldingen]
  <br/>Ontdek hoe u een zeer eenvoudige pushmelding verzendt naar uw app.

* [Problemen met een Mobile Services .NET-back-end oplossen]
  <br/> Ontdek hoe u problemen met een Mobile Services .NET-back-end opspoort en oplost.

Zie voor meer informatie over universele Windows-apps [Ondersteuning van meerdere apparaatplatforms van een mobiele service](mobile-services-how-to-use-multiple-clients-single-service.md#shared-vs).

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->

<!-- Images. -->



<!-- URLs. -->
[Visual Studio 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Aan de slag met gegevens]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Aan de slag met het offline synchroniseren van gegevens]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[Aan de slag met verificatie]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Aan de slag met pushmeldingen]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript en HTML]: mobile-services-win8-javascript/
[klassieke Azure Portal]: https://manage.windowsazure.com/
[Problemen met een Mobile Services .NET-back-end oplossen]: mobile-services-dotnet-backend-how-to-troubleshoot.md



<!--HONumber=Jun16_HO2-->



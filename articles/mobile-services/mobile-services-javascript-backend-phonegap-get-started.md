<properties
    pageTitle="Aan de slag met Azure Mobile Services voor PhoneGap-/Cordova-apps | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor PhoneGap-ontwikkeling voor iOS, Android en Windows Phone."
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-phonegap"
    ms.devlang="multiple"
    ms.topic="get-started-article" 
    ms.date="02/10/2016"
    ms.author="ggailey777"/>

# Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een app met Azure Mobile Services. In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige _takenlijst_-app die app-gegevens opslaat in de nieuwe mobiele service.

Hieronder ziet u een schermafbeelding van de voltooide app:

![][3]

### Aanvullende vereisten

Om deze zelfstudie te voltooien, hebt u het volgende nodig:

+ PhoneGap-hulpprogramma's (v3.2+ is vereist voor Windows Phone 8-projecten).

+ Een actief Microsoft Azure-account.

+ PhoneGap ondersteunt ontwikkeling voor meerdere platforms. Naast de PhoneGap-hulpprogramma's moet u ook de hulpprogramma's installeren voor elk platform dat u als doel gebruikt:

    - Windows Phone: installeer [Visual Studio 2012 Express voor Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374)
    - iOS: installeer [Xcode] (v4.4+ vereist)
    - Android: installeer de [Android-ontwikkelaarshulpprogramma's][Android SDK]
        <br/>(De Mobile Services SDK voor Android ondersteunt apps voor Android 2.2 of hoger. Android 4.2 of hoger is vereist om de app Quick Start uit te voeren.)

## Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Een nieuwe PhoneGap-app maken

In deze sectie maakt u een nieuwe PhoneGap-app die is verbonden met uw mobiele service.

1.  Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.

2. Klik op het tabblad Quick Start op **PhoneGap** onder **Platform kiezen** en vouw **Een nieuwe PhoneGap-app maken** uit.

    ![][0]

    U ziet nu de drie eenvoudige stappen om een PhoneGap-app te maken die is verbonden met uw mobiele service.

    ![][1]

3. Als u dit nog niet hebt gedaan, downloadt en installeert u PhoneGap en ten minste een van de hulpprogramma's voor platformontwikkeling (voor Windows Phone, iOS of Android).

4. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.

5. Klik onder **Uw app downloaden en uitvoeren** op **Downloaden**.

    Hiermee downloadt u het project voor de _takenlijst_-voorbeeldtoepassing die is verbonden met uw mobiele service, samen met de JavaScript SDK. Sla het gecomprimeerde projectbestand op uw lokale computer op en noteer de opslaglocatie.

## De nieuwe PhoneGap-app uitvoeren

De laatste fase van deze zelfstudie is het bouwen en uitvoeren van uw nieuwe app.

1.  Blader naar de locatie waar u de gecomprimeerde projectbestanden hebt opgeslagen en vouw de bestanden op uw computer uit.

2.  Open het project en voer het voor elk platform uit volgens onderstaande instructies.

    + **Windows Phone 8**

        1. Windows Phone 8: open het .sln-bestand in de map **platforms\wp8** in Visual Studio 2012 Express voor Windows Phone.

        2. Druk op de toets **F5** om het project te herbouwen en de app te starten.

        ![][2]

    + **iOS**

        1. Open het project in de map **platforms/ios** in Xcode.

        2. Druk op de knop **Uitvoeren** om het project te bouwen en de app te starten in de iPhone-emulator. Dit is de standaardoptie voor dit project.

        ![][3]

    + **Android**

        1. Klik in Eclipse op **Bestand** en vervolgens op **Importeren**, vouw **Android** uit, klik op **Bestaande Android-code naar werkruimte** en klik vervolgens op **Volgende**.

        2. Klik op **Bladeren**, blader naar de locatie van de uitgevouwen projectbestanden, klik op **OK**, zorg ervoor dat het project TodoActivity is ingeschakeld en klik vervolgens op **Voltooien**. <p>Hiermee worden de projectbestanden in de huidige werkruimte geïmporteerd.</p>

        3. Klik in het menu **Uitvoeren** op **Uitvoeren** om het project in de Android-emulator te starten.

            ![][4]

        >[AZURE.NOTE]Als u het project in de Android-emulator wilt uitvoeren, moet u ten minste één virtueel Android-apparaat (AVD) definiëren. Gebruik AVD Manager om deze apparaten te maken en te beheren.


3. Nadat u de app in een van bovenstaande mobiele emulators hebt gestart, typt u wat tekst in het tekstvak. Klik vervolgens op **Toevoegen**.

    Er wordt nu een POST-aanvraag verzonden naar de nieuwe mobiele service die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de **takentabel**. De items die in de tabel zijn opgeslagen, worden geretourneerd door de mobiele service en de gegevens worden weergegeven in de lijst.

    > [AZURE.IMPORTANT] Wijzigingen in dit platformproject worden overschreven als het hoofdproject wordt herbouwd met de PhoneGap-hulpprogramma's. Breng in plaats daarvan wijzigingen aan in de www-hoofdmap van het project, zoals wordt beschreven in onderstaande sectie.

4. Klik in de [klassieke Azure Portal] op het tabblad **Gegevens** en vervolgens op de tabel **Taken**.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-tab.png)

    U kunt nu door de gegevens bladeren die door de app in de tabel zijn ingevoegd.

    ![](./media/mobile-services-javascript-backend-phonegap-get-started/mobile-data-browse.png)


## App-updates maken en projecten herbouwen voor elk platform

1. Breng wijzigingen aan in codebestanden in de map www (in dit geval todolist/www).

2. Controleer of alle hulpmiddelen voor de gewenste platforms toegankelijk zijn in het systeempad.

2. Open een opdrachtprompt in de hoofdmap van het project en voer een van de volgende platformspecifieke opdrachten uit:

    + **Windows Phone**

        Voer de volgende opdracht uit vanaf de opdrachtprompt van Visual Studio Developer:

            phonegap local build wp8

    + **iOS**

        Open de terminal en voer de volgende opdracht uit:

            phonegap local build ios

    + **Android**

        Open een opdrachtprompt of een terminalvenster en voer de volgende opdracht uit.

            phonegap local build android

4. Open elk project in de juiste ontwikkelomgeving, zoals wordt beschreven in de vorige sectie.

>[AZURE.NOTE]U kunt de code die toegang heeft tot uw mobiele service, bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het bestand je/index.js.

## Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u nagaan hoe u in Mobile Services ook diverse andere belangrijke taken uitvoert:

* **[Verificatie toevoegen aan uw app]**  
  Ontdek hoe u gebruikers van uw app verifieert met een id-provider.  

* **[Pushmeldingen toevoegen aan uw app](https://msdn.microsoft.com/magazine/dn879353.aspx)**  
  Ontdek hoe u zich registreert voor pushmeldingen en deze naar uw app verzendt.

* **[Mobile Services HTML/Instructies voor conceptuele verwijzing in JavaScript](mobile-services-html-how-to-use-client-library.md)**  
  Meer informatie over het gebruik van de JavaScript-clientbibliotheek voor toegang tot gegevens, het aanroepen van aangepaste API's en het uitvoeren van verificaties.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Images. -->
[0]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot1.png
[1]: ./media/mobile-services-javascript-backend-phonegap-get-started/portal-screenshot2.png
[2]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-wp8.png
[3]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-ios.png
[4]: ./media/mobile-services-javascript-backend-phonegap-get-started/mobile-portal-quickstart-android.png

<!-- URLs. -->
[Verificatie toevoegen aan uw app]: mobile-services-html-get-started-users.md
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[klassieke Azure Portal]: https://manage.windowsazure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio 2012 Express voor Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
 


<!--HONumber=Jun16_HO2-->



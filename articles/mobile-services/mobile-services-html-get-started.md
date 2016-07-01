<properties
    pageTitle="Aan de slag met Azure Mobile Services voor HTML/JavaScript-apps | Microsoft Azure"
    description="Volg deze zelfstudie om aan de slag te gaan met Azure Mobile Services voor HTML-ontwikkeling."
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html5"
    ms.devlang="javascript"
    ms.topic="get-started-article" 
    ms.date="11/30/2015"
    ms.author="glenga"/>


# <a name="getting-started"> </a>Aan de slag met Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Overzicht 

Deze zelfstudie laat zien hoe u een cloudgebaseerde back-endservice toevoegt aan een HTML-app met Azure Mobile Services. In deze zelfstudie maakt u een nieuwe mobiele service en een eenvoudige *takenlijst*-app die app-gegevens opslaat in de nieuwe mobiele service. U kunt de volgende videoversie van deze zelfstudie bekijken. 

> [AZURE.VIDEO mobile-get-started-html]
 
Hieronder ziet u een schermafbeelding van de voltooide app:

![][0]

Het voltooien van deze zelfstudie is een vereiste voor alle andere Mobile Services-zelfstudies voor HTML-apps. Zie voor een PhoneGap/Cordova-app de [PhoneGap/Cordova-versie](mobile-services-javascript-backend-phonegap-get-started.md) van deze zelfstudie.

##Vereisten

Hieronder wordt aangegeven wat de vereisten zijn om deze zelfstudie te voltooien:

+ Een van de volgende webservers moet op uw lokale computer worden uitgevoerd:

    +  **Op Windows**: IIS Express. IIS Express wordt geïnstalleerd door het [webplatforminstallatieprogramma voor Windows].
    +  **Op Mac OS X**: Python, die al is geïnstalleerd.
    +  **Op Linux**: Python. U moet de [meest recente versie van Python] installeren.

    U kunt elke willekeurige webserver gebruiken voor het hosten van de app, maar dit zijn de webservers die worden ondersteund door de gedownloade scripts.  

+ Een webbrowser met ondersteuning voor HTML5.
+ Een Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank) voor meer informatie. 


## <a name="create-new-service"> </a>Een nieuwe mobiele service maken

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Een nieuwe HTML-app maken

Nadat u uw mobiele service hebt gemaakt, kunt u in de klassieke Azure Portal een eenvoudige Quick Start volgen om een nieuwe app te maken of een bestaande app zodanig te wijzigen dat deze verbinding maakt met uw mobiele service.

In deze sectie maakt u een nieuwe HTML-app die is verbonden met uw mobiele service.

1.  Klik in de [klassieke Azure Portal] op **Mobile Services** en klik vervolgens op de mobiele service die u zojuist hebt gemaakt.


2. Klik op het tabblad Quick Start op **Windows** onder **Platform kiezen** en vouw **Een nieuwe HTML-app maken** uit.

    ![][6]

    U ziet nu de drie eenvoudige stappen om een HTML-app te maken en te hosten die is verbonden met uw mobiele service.

    ![][7]

3. Klik op **Takentabel maken** om een tabel te maken voor het opslaan van app-gegevens.

4. Klik onder **Uw app downloaden en uitvoeren** op **Downloaden**.

    Hiermee downloadt u de websitebestanden voor de _takenlijst_-voorbeeldtoepassing die is verbonden met uw mobiele service. Sla het gecomprimeerde bestand op uw lokale computer op en noteer de opslaglocatie.

5. Controleer op het tabblad **Configureren** of `localhost` al wordt vermeld in de lijst **Aanvragen van hostnamen toestaan** onder **Cross-origin-resource delen (CORS)**. Als dat niet het geval is, typt u `localhost` in het veld **Hostnaam** en klikt u vervolgens op **Opslaan**.

    ![][9]

    > [AZURE.IMPORTANT] Als u de Quick Start-app op een andere webserver dan localhost implementeert, moet u de hostnaam van de webserver toevoegen aan de lijst **Aanvragen van hostnamen toestaan**. Zie voor meer informatie [Cross-origin-resources delen](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx).

## Uw HTML-app hosten en uitvoeren

De laatste fase van deze zelfstudie is het hosten en uitvoeren van uw nieuwe app op uw lokale computer.

1. Blader naar de locatie waar u de gecomprimeerde projectbestanden hebt opgeslagen, vouw de bestanden op uw computer uit en start een van de volgende opdrachtbestanden vanuit de **server**-submap.

    + **launch-windows** (Windows-computers)
    + **launch-mac.command** (Mac OS X-computers)
    + **launch-linux.sh** (Linux-computers)

    > [AZURE.NOTE] Typ op een Windows-computer `R` wanneer PowerShell u vraagt te bevestigen dat u het script wilt uitvoeren. Mogelijk waarschuwt uw webbrowser u het script niet uit te voeren omdat het is gedownload van internet. Als dit gebeurt, moet u aangeven dat de browser moet verdergaan met het laden van het script.

    Hiermee start u een webserver op de lokale computer voor het hosten van de nieuwe app.

2. Open de URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> in een webbrowser om de app te starten.

3. Typ in de app zinvolle tekst, zoals _Voltooi de zelfstudie_ in **Nieuwe taak invoeren** en klik op **Toevoegen**.

    ![][10]

    Er wordt nu een POST-aanvraag verzonden naar de nieuwe mobiele service die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. De items die in de tabel zijn opgeslagen, worden door de mobiele service geretourneerd. Vervolgens worden de gegevens weergegeven in de tweede kolom in de app.

    > [AZURE.NOTE] U kunt de code die toegang heeft tot uw mobiele service bekijken om gegevens op te vragen en in te voegen. Deze code vindt u in het bestand page.js.

4. Klik in de [klassieke Azure Portal] op het tabblad **Gegevens** en klik vervolgens op de tabel **Taken**.

    ![][11]

    U kunt nu door de gegevens bladeren die door de app in de tabel zijn ingevoegd.

    ![][12]

## <a name="next-steps"> </a>Volgende stappen
Nu u de Quick Start hebt voltooid, kunt u nagaan hoe u in Mobile Services ook diverse andere belangrijke taken uitvoert:

* **[Verificatie toevoegen aan uw app]**  
  Ontdek hoe u gebruikers van uw app verifieert met een id-provider.

* **[Mobile Services HTML/Instructies voor conceptuele verwijzing in JavaScript]**  
  Meer informatie over het gebruik van Mobile Services met HTML/JavaScript


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Aan de slag met Mobile Services]:#getting-started
[Een nieuwe mobiele service maken]:#create-new-service
[Het mobiele service-exemplaar definiëren]:#define-mobile-service-instance
[Volgende stappen]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Verificatie toevoegen aan uw app]: mobile-services-html-get-started-users.md

[klassieke Azure Portal]: https://manage.windowsazure.com/
[webplatforminstallatieprogramma voor Windows]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[meest recente versie van Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Mobile Services HTML/Instructies voor conceptuele verwijzing in JavaScript]: mobile-services-html-how-to-use-client-library.md
[Cross-origin-resources delen]: http://msdn.microsoft.com/library/azure/dn155871.aspx
 



<!--HONumber=Jun16_HO2-->



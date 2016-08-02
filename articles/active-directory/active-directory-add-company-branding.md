<properties
    pageTitle="De huisstijl van uw bedrijf toevoegen aan de aanmeldingspagina en de toegangsvensterpagina’s"
    description="Ontdek hoe u uw huisstijl kunt toevoegen aan de Azure-aanmeldingspagina en aan de toegangsvensterpagina"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/16/2016" 
    ms.author="MarkVi"/>

# De huisstijl van uw bedrijf toevoegen aan de aanmeldingspagina en de toegangsvensterpagina’s


Om verwarring te voorkomen, willen veel bedrijven een consistente look gebruiken voor alle websites en services die ze beheren. Azure Active Directory biedt deze mogelijkheid. U kunt het uiterlijk van de volgende webpagina’s aanpassen en uw eigen bedrijfslogo en kleurenschema toepassen:

- **Aanmeldingspagina**: dit is de pagina die wordt weergegeven wanneer u zich aanmeldt bij Office 365 of andere toepassingen op internet die gebruikmaken van Azure AD als id-provider. U communiceert met deze pagina tijdens het detecteren van het thuisdomein of om uw referenties in te voeren. Tijdens de detectie van het thuisdomein kan het systeem federatieve gebruikers omleiden naar hun on-premises STS (zoals AD FS).

- **Pagina met toegangspaneel**: het toegangspaneel is een portal op internet waarmee u de cloudtoepassingen kunt bekijken en gebruiken waartoe uw Azure AD-beheerder u toegang heeft verleend. Als u het toegangsvenster wilt openen, gebruikt u de volgende URL: [https://myapps.microsoft.com](https://myapps.microsoft.com).

In dit onderwerp wordt uitgelegd hoe u de aanmeldingspagina en de pagina met het toegangsvenster kunt aanpassen.

> [AZURE.NOTE]
>
- De functie Huisstijl is alleen beschikbaar als u een upgrade hebt uitgevoerd naar de Premium- of Basic-editie van Azure Active Directory. Zie [Azure Active Directory-edities](active-directory-editions.md) voor meer informatie.
- De Azure Active Directory-edities Premium en Basic zijn beschikbaar voor klanten in China via het wereldwijde exemplaar van Azure Active Directory. De edities Azure Active Directory Premium en Basic worden momenteel niet ondersteund in de Microsoft Azure-service die wordt beheerd door 21Vianet in China. Neem voor meer informatie contact met ons op via het [Azure Active Directory-forum](https://feedback.azure.com/forums/169401-azure-active-directory/).



## De aanmeldingspagina aanpassen

Als u browsertoegang nodig hebt om de cloud-apps en -services te openen waar uw organisatie op is geabonneerd, gebruikt u de aanmeldingspagina.

Als u wijzigingen hebt aangebracht aan uw aanmeldingspagina, kan het een uur duren voordat de wijzigingen worden weergegeven.

Er wordt alleen een aanmeldingspagina met huisstijl weergegeven wanneer u een service opent met een tenantspecifieke URL, zoals https://outlook.com/**contoso**.com of https://mail.**contoso**.com.

Wanneer u een service gebruikt met niet-tenantspecifieke URL’s (bijvoorbeeld https://mail.office365.com), wordt er een aanmeldingspagina zonder huisstijl weergegeven. In dat geval wordt uw huisstijl weergegeven zodra u uw gebruikers-id hebt ingevoerd of een gebruikerstegel hebt geselecteerd.

> [AZURE.NOTE]
>

- Uw domeinnaam moet als Actief worden weergegeven in het gedeelte **Active Directory** > **Directory** > **Domeinen** van de klassieke Azure Portal waarin u de huisstijl hebt geconfigureerd.

- De huisstijl van de aanmeldingspagina wordt niet meegenomen naar de Microsoft-aanmeldingspagina voor klanten. Als u zich met een persoonlijk Microsoft-account aanmeldt, wordt er door Azure AD een reeks gebruikerstegels met huisstijl weergegeven, maar de huisstijl van uw organisatie wordt niet toegepast op de aanmeldingspagina voor Microsoft-accounts.


Als u op deze pagina uw bedrijfsmerk en -kleuren en andere aanpasbare elementen wilt weergeven, bekijkt u de volgende afbeeldingen om inzicht te krijgen in het verschil tussen de twee ervaringen.

In de volgende schermafbeelding ziet u een voorbeeld van de Office 365-aanmeldingspagina op een desktopcomputer **vóór** het aanpassen:

![De Office 365-aanmeldingspagina vóór het aanpassen][1]

In de volgende schermafbeelding ziet u een voorbeeld van de Office 365-aanmeldingspagina op een desktopcomputer **na** het aanpassen:

![De Office 365-aanmeldingspagina na het aanpassen][2]

In de volgende schermafbeelding ziet u een voorbeeld van de Office 365-aanmeldingspagina op een mobiel apparaat **vóór** het aanpassen:

![De Office 365-aanmeldingspagina vóór het aanpassen][3]


In de volgende schermafbeelding ziet u een voorbeeld van de Office 365-aanmeldingspagina op een mobiel apparaat **na** het aanpassen:

![De Office 365-aanmeldingspagina na het aanpassen][4]


Wanneer u het formaat van een browservenster wijzigt, wordt de grote afbeelding (zoals de afbeelding die eerder is weergegeven) vaak bijgesneden om ervoor te zorgen dat de verhoudingen blijven kloppen. U moet daarom proberen om de belangrijkste visuele elementen zodanig in de afbeelding te plaatsen dat ze altijd in de linkerbovenhoek worden weergegeven (rechtsboven bij talen waarin van rechts naar links wordt geschreven). Dit is belangrijk omdat met het wijzigen van de grootte meestal wordt begonnen in de rechterbenedenhoek, waarna er naar linksboven of van beneden naar boven wordt gewerkt.

In de volgende afbeelding ziet u hoe de afbeelding wordt bijgesneden wanneer de grootte van het browservenster van rechts naar links wordt aangepast:

![][6]

Hier ziet u hoe de afbeelding wordt weergegeven wanneer de grootte van het browservenster van beneden naar boven wordt aangepast:

![][7]

## Welke elementen op de pagina kan ik aanpassen?

U kunt de volgende elementen op de aanmeldingspagina aanpassen:

![][5]

 Pagina-element  | Locatie op de pagina
    ------------- | -------------
Logo in banner | Wordt in de rechterbovenhoek van de pagina weergegeven. Vervangt het logo dat wordt weergegeven op de doelsite waarop u zich aanmeldt (bijvoorbeeld Office 365 of Azure).
Grote afbeelding/achtergrondkleur | Wordt aan de linkerkant van de pagina weergegeven. Vervangt de afbeelding die wordt weergegeven op de doelsite waarop u zich aanmeldt. In plaats van de grote afbeelding kan de achtergrondkleur worden weergegeven als de verbinding een lage bandbreedte heeft of het scherm erg smal is.
Tekst van aanmeldingspagina | Wordt boven de paginavoettekst weergegeven als u handige informatie wilt verstrekken voordat iemand zich aanmeldt met een werk- of schoolaccount. U kunt bijvoorbeeld het telefoonnummer van de helpdesk weergeven of een juridische mededeling.

> [AZURE.NOTE]
Alle elementen zijn optioneel. Als u bijvoorbeeld een logo wilt weergeven in de banner, maar geen grote afbeelding wilt gebruiken, wordt uw logo weergegeven op de aanmeldingspagina en de afbeelding voor de doelsite (in dit geval de Office 365-afbeelding van een snelweg in Californië).

U kunt alle elementen op deze pagina lokaliseren. Wanneer u een standaardset aangepaste elementen hebt geconfigureerd, kunt u meer versies configureren voor verschillende talen. U kunt ook een combinatie van verschillende elementen gebruiken. U kunt bijvoorbeeld:

- Een grote standaardafbeelding maken die geschikt is voor alle culturen en afzonderlijke versies maken voor Engels- en Franstaligen. Wanneer een browser op een van die twee talen wordt ingesteld, wordt de aangepaste afbeelding weergegeven. Bij alle overige talen wordt de standaardafbeelding weergegeven.

- Voor uw organisatie verschillende logo's configureren (bijvoorbeeld een Japanse en een Hebreeuwse versie).



## De pagina met het toegangspaneel aanpassen

De pagina met het toegangspaneel is als het ware een portalpagina voor snelle toegang tot de cloud-apps waar de beheerder u toegang toe heeft verleend. Op deze pagina worden uw apps weergegeven als toepassingstegels waarop kan worden geklikt. 


In de volgende schermafbeelding ziet u een voorbeeld van een pagina met een toegangspaneel nadat deze is aangepast.

![][8]

## Uw directory configureren met de huisstijl van uw bedrijf

In de klassieke Azure Portal kunt u per directory één standaardset aanpasbare elementen configureren. Wanneer de standaardset is opgeslagen, kan een beheerder vertaalde versies van elk element toevoegen voor gebruik in andere taalgebieden. Alle aanpasbare elementen zijn optioneel.

Als u bijvoorbeeld een standaardlogo voor in de banner configureert, maar geen grote afbeelding, wordt in de rechterbovenhoek van de aanmeldingspagina uw logo weergegeven. Daarbij wordt de standaardafbeelding van de site weergegeven. 

Stel dat de volgende configuratie wordt gebruikt:

- Een standaardlogo voor in de banner en aanmeldingspaginatekst in het Engels 
- Een taalspecifieke aanmeldingspaginatekst in het Duits 

Als uw voorkeurstaal Duits is, krijgt u het standaardlogo in de banner te zien, maar de Duitse tekst. 

Hoewel u technisch gezien een afzonderlijke set zou kunnen configureren voor elke taal die door Azure AD wordt ondersteund, doet u er verstandig aan om het aantal variaties klein te houden. Dit vereenvoudigt het onderhoud en houdt de prestaties goed.

**Als u de huisstijl van uw bedrijf aan de directory wilt toevoegen, voert u de volgende stappen uit:**

1. Meld u bij de [klassieke Azure Portal](https://manage.windowsazure.com) aan als beheerder van de directory die u wilt aanpassen.
2. Selecteer de directory die u wilt aanpassen.
3. Klik in de werkbalk bovenaan op **Configureren**.
4. Klik op **Huisstijl aanpassen**.
4. Wijzig de elementen die u wilt aanpassen. Alle velden zijn optioneel.
5. Klik op **Opslaan**.

Het kan een uur duren voordat de wijzigingen die u aan de huisstijl van de aanmeldingspagina hebt aangebracht, worden weergegeven.

**Als u taalspecifieke huisstijlwijzigingen wilt doorvoeren, volgt u de volgende stappen:**

1. Meld u bij de [klassieke Azure Portal](https://manage.windowsazure.com) aan als beheerder van de directory die u wilt aanpassen.
2. Selecteer de directory die u wilt aanpassen.
3. Klik in de werkbalk bovenaan op **Configureren**.
4. Klik op **Huisstijl aanpassen**.
2. Klik op **Huisstijl voor een specifieke taal toevoegen**.
3. Selecteer de taal waarvoor u het logo wilt aanpassen en klik vervolgens op **Volgende**.
3. Bewerk alleen de elementen waarvoor u taalspecifieke overschrijvingen wilt configureren. Alle velden zijn optioneel. Als een veld leeg wordt gelaten, wordt de aangepaste standaardwaarde weergegeven (of de Microsoft-standaard als er geen aangepaste standaard is geconfigureerd).
4. Klik op **Opslaan**.

**Als u de huisstijl van uw bedrijf uit de directory wilt verwijderen, voert u de volgende stappen uit:**

1. Meld u bij de [klassieke Azure Portal](https://manage.windowsazure.com) aan als beheerder van de directory die u wilt aanpassen.
2. Selecteer de directory die u wilt aanpassen.
3. Klik in de werkbalk bovenaan op **Configureren**.
4. Klik op **Huisstijl aanpassen**.
5. Selecteer op de pagina Huisstijl aanpassen de optie **Bestaande huisstijlinstellingen bewerken** en ga naar de volgende pagina.
3. Afhankelijk van de elementen die u wilt verwijderen, voert u een of meer van de volgende acties uit:

    a. Selecteer onder **Logo in banner** de optie **Geüploade logo verwijderen**.

    b. Selecteer onder **Logo in tegel** de optie **Geüploade logo verwijderen**.

    c. Verwijder de tekst uit alle tekstvakken.

    d. Klik op **Volgende**.

    e. Verwijder de tekst uit alle tekstvakken.

4. Klik op **Opslaan** om de elementen te verwijderen.
5. Klik zo nodig opnieuw op **Huisstijl aanpassen** en herhaal deze stappen voor alle taalspecifieke huisstijlonderdelen die moeten worden verwijderd.
    Alle huisstijlinstellingen zijn verwijderd wanneer u op **Huisstijl aanpassen** klikt en er in het formulier **Standaardhuisstijl aanpassen** geen bestaande instellingen zijn geconfigureerd.

## Testen en voorbeelden

U doet er verstandig aan om te experimenteren met een testtenant voordat u wijzigingen aanbrengt aan uw productieomgeving.

**Controleren of uw huisstijl is toegepast:**

1. Open een InPrivate- of Incognito-browsersessie. 
2. Ga naar https://outlook.com/contoso.com en vervang contoso.com door het domein dat u hebt aangepast. 

Dit geldt ook voor domeinen met de volgende indeling: contoso.onmicrosoft.com.

De volgende twee fictieve aanmeldingspagina’s zijn aangepast. Op basis hiervan kunt u eenvoudiger effectieve aanpassingssets maken:

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Als u de taalspecifieke instellingen wilt testen, moet u de standaardtaalvoorkeuren in uw webbrowser wijzigen in een taal die u hebt ingesteld in de aanpassingen. In Internet Explorer kunt u dit configureren in het menu **Internetopties**.

## Aanpasbare elementen

Sommige aanpasbare elementen in Azure AD hebben meerdere gebruiksmogelijkheden. Zo hoeft u uw bedrijfslogo voor elke directory slechts één keer te configureren, maar wordt het logo voor zowel de aanmeldingspagina als de pagina’s met het toegangsvenster gebruikt. Sommige aanpasbare elementen worden alleen op de aanmeldingspagina gebruikt. In de volgende tabel vindt u meer informatie over de verschillende aanpasbare elementen.

Naam | Beschrijving | Beperkingen | Aanbevelingen
    ------------- | ------------- | ------------- | -------------
Logo in banner | Het logo in de banner wordt weergegeven op de aanmeldingspagina en in het toegangsvenster. | <p>JPG of PNG</p><p>60 x 280 pixels</p><p>10 kB</p> | <p>Gebruik het volledige logo van uw organisatie (inclusief pictogram en logotype)</p><p>Gebruik een hoogte van maximaal 30 pixels om te voorkomen dat er op mobiele apparaten schuifbalken worden weergegeven</p><p>Gebruik een grootte van maximaal 4 kB</p><p>Gebruik een transparant PNG-bestand (ga er niet van uit dat de aanmeldingspagina een witte achtergrond heeft)</p>
Logo in tegel | In de toekomst wordt deze tekst mogelijk gebruikt om op verschillende plaatsen binnen de omgeving het algemene pictogram voor een werk- of schoolaccount te vervangen (momenteel niet in gebruik op de aanmeldingspagina). | <p>JPG of PNG</p><p>120 x 120 pixels</p><p>10 kB</p> | <p>Houd het eenvoudig (geen kleine tekst), omdat deze afbeelding tot wel 50% kleiner kan worden gemaakt.
</p> |
Gebruikersnaamlabel op aanmeldingspagina | In de toekomst wordt deze tekst mogelijk gebruikt om op verschillende plaatsen binnen de omgeving de algemene tekenreeks voor een werk- of schoolaccount te vervangen (momenteel niet in gebruik op de aanmeldingspagina). U kunt deze tekenreeks instellen op zoiets als 'Contoso-account' of 'Contoso-id'. | <p>Unicodetekst, maximaal 50 tekens</p><p>Alleen tekst zonder opmaak (geen koppelingen of HTML-tags)</p> | <p>Houd het kort en eenvoudig</p><p>Vraag uw gebruikers hoe ze het werk- of schoolaccount waar u ze van voorziet, meestal noemen.</p>
Tekst van aanmeldingspagina | Deze standaardtekst wordt onder het formulier van de aanmeldingspagina weergegeven en kan worden gebruikt om aanvullende instructies te geven of om de gebruiker te laten weten waar hulp en ondersteuning te vinden zijn. | <p>Unicodetekst, maximaal 256 tekens</p><p>Alleen tekst zonder opmaak (geen koppelingen of HTML-tags)</p> | Houd het korter dan 250 tekens (ongeveer 3 regels tekst)
Afbeelding op aanmeldingspagina | Dit is een grote afbeelding die wordt weergegeven op de aanmeldingspagina, links van het aanmeldingsformulier. | <p>JPG of PNG</p><p>1420 x 1200</p><p>500 kB</p> | <p>1420 x 1200 pixels</p><p>Belangrijk: houd de afbeelding zo klein mogelijk, liefst kleiner dan 200 kB. Als deze afbeelding te groot is, kan dat de prestaties van de aanmeldingspagina beïnvloeden wanneer de afbeelding niet in het cachegeheugen is opgeslagen</p><p>De afbeelding wordt vaak bijgesneden, zodat deze er ook goed uitziet bij een andere beeldverhouding. Houd de primaire visuele elementen in de linkerbovenhoek (in de rechterbovenhoek voor talen die van rechts naar links worden geschreven), omdat de grootte altijd vanuit de rechterbenedenhoek wordt gewijzigd wanneer het browservenster groter of kleiner wordt gemaakt.</p>
Achtergrondkleur van de aanmeldingspagina | De achtergrondkleur van de aanmeldingspagina wordt in het gedeelte links van het aanmeldingsformulier gebruikt. | Dit moet een RGB-kleur zijn met een hexadecimale notatie (voorbeeld: #FFFFFF) | <p>De achtergrondkleur kan in plaats van de grote afbeelding worden weergegeven als er een verbinding is met een lage bandbreedte</p><p>Het is een goed idee om de primaire kleur van het logo in de banner te gebruiken</p>


## Volgende stappen

- [Aan de slag met Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Uw toegangs- en gebruiksrapporten weergeven](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png



<!--HONumber=Jun16_HO2-->



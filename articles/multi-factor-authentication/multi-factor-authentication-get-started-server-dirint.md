<properties 
    pageTitle="Adreslijstintegratie tussen Azure Multi-Factor Authentication en Active Directory" 
    description="Dit is de Azure Multi-Factor Authentication-pagina waarop wordt beschreven hoe u de Azure Multi-Factor Authentication-server kunt integreren met Active Directory zodat u de mappen kunt synchroniseren." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Adreslijstintegratie tussen Azure MFA-server en Active Directory

In de sectie Adreslijstintegratie kunt u de server configureren voor integratie met Active Directory of een andere LDAP-directory.  Hier kunt u kenmerken configureren zodat deze overeenkomen met het Active Directory-schema en de automatische synchronisatie van gebruikers instellen. 

## Instellingen
De Azure Multi-Factor Authentication-server is standaard geconfigureerd om gebruikers te importeren uit of te synchroniseren met Active Directory.  Op dit tabblad kunt u het standaardgedrag negeren en een binding maken met een andere LDAP-directory, een ADAM-directory of specifieke Active Directory-domeincontroller.  U kunt hier ook LDAP-verificatie gebruiken om een proxy op LDAP uit te voeren of LDAP-binding gebruiken als een RADIUS-doel, pre-authenticatie voor IIS-authenticatie gebruiken of primaire authenticatie voor de gebruikersportal gebruiken.  De volgende tabel beschrijft de afzonderlijke instellingen.

![Instellingen](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Functie | Beschrijving |
| ------- | ----------- |
| Active Directory gebruiken | Selecteer de optie Active Directory gebruiken om Active Directory te gebruiken voor het importeren en synchroniseren.  Dit is de standaardinstelling. <br>Opmerking: voor een goede werking van de Active Directory-integratie moet de computer aan een domein zijn toegevoegd en moet u aangemeld zijn met een domeinaccount. |
| Vertrouwde domeinen opnemen | Schakel het selectievakje Vertrouwde domeinen opnemen in om de agent verbindingspogingen te laten uitvoeren met domeinen die door het huidige domein worden vertrouwd, met een ander domein in het forest of met domeinen die betrokken zijn in een forestvertrouwensrelatie.  Wanneer u geen gebruikers uit een van de vertrouwde domeinen importeert of synchroniseert, schakelt u het selectievakje uit om de prestaties te verbeteren.  Standaard is dit selectievakje ingeschakeld. |
| Specifieke LDAP-configuratie gebruiken | Selecteer de optie Specifieke LDAP-configuratie gebruiken om de LDAP-instellingen te gebruiken die voor het importeren en synchroniseren zijn opgegeven. Opmerking: als Specifieke LDAP-configuratie gebruiken is geselecteerd, verandert de gebruikersinterface verwijzingen van Active Directory in LDAP. |
| Knop Bewerken | Met de knop Bewerken kunt de huidige LDAP-configuratie-instellingen wijzigen. |
| Query's voor kenmerkbereik gebruiken | Geeft aan of query's voor het kenmerkbereik moeten worden gebruikt.  Met query's voor het kenmerkbereik kan efficiënt in directory's worden gezocht naar in aanmerking komende records op basis van de vermeldingen in het kenmerk van een andere record.  De Azure Multi-Factor Authentication-server gebruikt query's voor het kenmerkbereik om een query uit te voeren naar de gebruikers die lid zijn van een beveiligingsgroep.   <br>Opmerking: er zijn enkele gevallen waarbij query's voor het kenmerkbereik worden ondersteund, maar niet mogen worden gebruikt.  Active Directory kan bijvoorbeeld problemen met query's voor het kenmerkbereik hebben wanneer een beveiligingsgroep leden uit meer dan één domein bevat.  In dit geval moet het selectievakje worden uitgeschakeld. |

De volgende tabel beschrijft de LDAP-configuratie-instellingen.

| Functie | Beschrijving |
| ------- | ----------- |
| Server | Geef de hostnaam of het IP-adres van de server op waarop de LDAP-directory wordt uitgevoerd.  U kunt ook een back-upserver opgeven, gescheiden door een puntkomma. <br>Opmerking: wanneer het bindingstype SSL is, is doorgaans een volledig gekwalificeerde hostnaam vereist. |
| Basis-DN | Geef de DN-naam op van het basisdirectory-object waaruit alle directoryquery's worden gestart.  Bijvoorbeeld: dc=abc, dc=com. |
| Bindingstype query | Selecteer het geschikte bindingstype voor gebruik bij het binden om de LDAP-directory te zoeken.  Dit wordt gebruikt voor import, synchronisatie en gebruikersnaamomzetting. <br><br>  Anoniem: Er zal een anonieme binding worden uitgevoerd.  Bindings-DN en bindingswachtwoord worden niet gebruikt.  Dit werkt alleen als de LDAP-directory anonieme binding toestaat en als machtigingen toestaan dat query's naar de betreffende records en kenmerken worden uitgevoerd.  <br><br> Eenvoudig: Bindings-DN en bindingswachtwoord worden doorgegeven als tekst zonder opmaak om een binding te maken met de LDAP-directory.  Dit moet alleen worden gebruikt voor testdoeleinden om te verifiëren of de server bereikbaar is en of het bindingsaccount de juiste toegang heeft.  Het verdient aanbeveling om in plaats hiervan SSL te gebruiken nadat het juiste certificaat is geïnstalleerd.  <br><br> SSL: Bindings-DN en bindingswachtwoord worden versleuteld met behulp van SSL om een binding te maken met de LDAP-directory.  Hiervoor moet lokaal een certificaat worden geïnstalleerd dat de LDAP-directory vertrouwt.  <br><br> Windows: Bind gebruikersnaam en Bindingswachtwoord worden gebruikt om een veilige verbinding te maken met een Active Directory-domeincontroller of ADAM-directory.  Als Bind gebruikersnaam niet wordt ingevuld, wordt voor de binding het aangemelde gebruikersaccount gebruikt. |
| Bindingstype - Verificaties | Selecteer het geschikte bindingstype voor gebruik bij het uitvoeren van de verificatie van LDAP-bindingen.  Zie de beschrijvingen van de bindingstypen onder Bindingstype query.  Hierdoor kan bijvoorbeeld een anonieme binding worden gebruikt voor query's terwijl voor het beveiligen van verificaties van LDAP-bindingen een SSL-binding wordt gebruikt. |
| Bindings-DN of Bind gebruikersnaam | Geef de DN-naam van de gebruikersrecord op voor het account dat moet worden gebruikt bij het maken van een binding met de LDAP-directory.<br><br>De Bindings-DN wordt alleen gebruikt wanneer het bindingstype Eenvoudig of SSL is.  <br><br>Geef de gebruikersnaam van het Windows-account op dat moet worden gebruikt bij het maken van een binding met de LDAP-directory wanneer het bindingstype Windows is.  Als dit veld niet wordt ingevuld, wordt het aangemelde gebruikersaccount gebruikt om de binding te maken. |
| Bindingswachtwoord | Voer het bindingswachtwoord in voor de Bindings-DN of gebruikersnaam die wordt gebruikt om een binding te maken met de LDAP-directory.  Om het wachtwoord voor de Multi-Factor Authentication Server-service AdSync te configureren, moet synchronisatie zijn ingeschakeld en moet de service op de lokale computer actief zijn.  Het wachtwoord wordt opgeslagen in de in Windows opgeslagen gebruikersnamen en wachtwoorden met het account waarmee de Multi-Factor Authentication Server-service AdSync wordt uitgevoerd.  Het wachtwoord wordt ook opgeslagen met het account waarmee de gebruikersinterface van Multi-Factor Authentication-server wordt uitgevoerd en met het account waarmee de service van de Multi-Factor Authentication-server wordt uitgevoerd.  <br><br> Opmerking: omdat het wachtwoord alleen wordt opgeslagen in de in Windows opgeslagen gebruikersnamen en wachtwoorden van de lokale server, moet deze stap worden uitgevoerd op elke Multi-Factor Authentication-server die toegang tot het wachtwoord nodig heeft. |
| Maximale grootte query | Geef de maximale grootte op voor het maximum aantal gebruikers dat bij een directory-zoekopdracht wordt geretourneerd.  Deze limiet moet overeenkomen met de configuratie in de LDAP-directory.  Voor grote zoekopdrachten waarbij wisselgeheugengebruik niet wordt ondersteund, zal bij import en synchronisatie worden geprobeerd gebruikers in batches op te halen.  Als de maximale grootte hier hoger is dan de limiet die is geconfigureerd in de LDAP-directory, is het mogelijk dat sommige gebruikers ontbreken. |
| Knop Testen | Klik op de knop Testen om de binding met de LDAP-server te testen.  <br><br> Opmerking: De optie Specifieke LDAP-configuratie gebruiken hoeft niet te worden geselecteerd om de binding te testen.  Hiermee kan de binding worden getest alvorens de LDAP-configuratie te gebruiken. |

## Filters
Met behulp van filters kunt u criteria instellen om records te kwalificeren bij het uitvoeren van een directoryzoekopdracht.  Door het filter in te stellen, kunt u het bereik bepalen van de objecten die u wilt synchroniseren.  

![Filters](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Azure Multi-Factor Authentication heeft de volgende drie opties:

- **Containerfilter** - Geef de filtercriteria op die worden gebruikt om containerrecords te kwalificeren bij het uitvoeren van een directoryzoekopdracht.  Voor Active Directory en ADAM wordt doorgaans (|(objectClass=organizationalUnit)(objectClass=container)) gebruikt.  Voor andere LDAP-directory's moeten filtercriteria worden gebruikt die elk type containerobject kwalificeren, afhankelijk van het Active Directory-schema.  <br>Opmerking: als dit veld leeg blijft, wordt standaard ((objectClass=organizationalUnit)(objectClass=container)) gebruikt.

- **Beveiligingsgroepfilter** - Geef de filtercriteria op die worden gebruikt om beveiligingsgroeprecords te kwalificeren bij het uitvoeren van een directoryzoekopdracht.  Voor Active Directory en ADAM wordt doorgaans (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) gebruikt.  Voor andere LDAP-directory's moeten filtercriteria worden gebruikt die elk type beveiligingsgroepsobject kwalificeren, afhankelijk van het directory-schema.  <br>Opmerking: als dit veld leeg blijft, wordt standaard (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) gebruikt.

- **Gebruikersfilter** - Geef de filtercriteria op die worden gebruikt om gebruikersrecords te kwalificeren bij het uitvoeren van een directoryzoekopdracht.  Voor Active Directory en ADAM wordt doorgaans (&(objectClass=user)(objectCategory=person)) gebruikt.  Voor andere LDAP-mappen moet (objectClass=inetOrgPerson) of iets soortgelijks worden gebruikt, afhankelijk van het Active Directory-schema. <br>Opmerking: als dit veld leeg blijft, wordt standaard (&(objectCategory=person)(objectClass=user)) gebruikt.

## Kenmerken
Kenmerken kunnen indien nodig worden aangepast voor een specifieke directory.  Hierdoor kunt u aangepaste kenmerken toevoegen en de synchronisatie afstemmen op alleen de kenmerken die u nodig hebt.  De waarde voor elk kenmerkveld moet de naam van het kenmerk zijn, zoals deze in het Active Directory-schema is gedefinieerd.  Gebruik de onderstaande tabel voor meer informatie.

![Kenmerken](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Functie | Beschrijving |
| ------- | ----------- |
| Unieke id | Voer de kenmerknaam van het kenmerk in die fungeert als de unieke id van de container, beveiligingsgroep en gebruikersrecords.  In Active Directory is dit doorgaans objectGUID.  In andere LDAP-implementaties kan dit entryUUID of iets soortgelijks zijn.  De standaardwaarde is objectGUID. |
| Knoppen  ... (Kenmerk selecteren) | Naast elk kenmerkveld bevindt zich een knop '...' die het dialoogvenster Kenmerk selecteren weergeeft, waarin een kenmerk uit een lijst kan worden geselecteerd. <br><br>Dialoogvenster Kenmerk selecteren.<br><br>Opmerking: kenmerken kunnen handmatig worden ingevoerd en hoeven niet overeen te komen met een kenmerk in de lijst met kenmerken. |
| Type unieke id | Selecteer het type van het kenmerk Unieke id.  In Active Directory heeft het kenmerk objectGUID het type GUID.  In andere LDAP-implementaties kan dit het type ASCII-bytematrix of Tekenreeks zijn.  De standaardwaarde is GUID. <br><br>Opmerking: Het is belangrijk dat u dit correct instelt omdat naar synchronisatie-items wordt verwezen met hun unieke id en het type unieke id wordt gebruikt om het object rechtstreeks in de map te vinden.  Wanneer dit op Tekenreeks wordt ingesteld terwijl de directory de waarde eigenlijk als een bytematrix van ASCII-tekens opslaat, zal de synchronisatie niet juist verlopen. |
| DN-naam | Voer de kenmerknaam in van het kenmerk dat de DN-naam voor elke record bevat.  In Active Directory is dit doorgaans distinguishedName.  In andere LDAP-implementaties kan het entryDN of iets soortgelijks zijn.  De standaardwaarde is distinguishedName. <br><br>Opmerking: Als er geen kenmerk bestaat dat alleen de DN-naam bevat, kan het kenmerk adspath worden gebruikt.  Het gedeelte LDAP://<server>/ van het pad wordt automatisch verwijderd, waardoor alleen de DN-naam van het object overblijft. |
| Containernaam | Voer de kenmerknaam in van het kenmerk dat de naam in een containerrecord bevat.  De waarde van dit kenmerk wordt weergegeven in de containerhiërarchie bij het importeren vanuit Active Directory of bij het toevoegen van synchronisatie-items.  De standaardwaarde is name. <br><br>Opmerking: Als verschillende containers andere kenmerken voor hun namen gebruiken, kunnen meerdere containernaamkenmerken worden opgegeven, gescheiden door puntkomma's.  Het eerste containernaamkenmerk dat in een containerobject wordt gevonden, wordt gebruikt om de naam ervan weer te geven. |
| Naam beveiligingsgroep | Voer de kenmerknaam in van het kenmerk dat de naam in een beveiligingsgroepsrecord bevat.  De waarde van dit kenmerk wordt weergegeven in de lijst Beveiligingsgroep bij het importeren vanuit Active Directory of het toevoegen van synchronisatie-items.  De standaardwaarde is name. |
| Gebruikers | De volgende kenmerken worden gebruikt voor het zoeken, weergeven, importeren en synchroniseren van gebruikersgegevens uit de directory. |
| Gebruikersnaam | Voer de kenmerknaam in van het kenmerk dat de gebruikersnaam in een gebruikersrecord bevat.  De waarde van dit kenmerk wordt gebruikt als de gebruikersnaam voor de Multi-Factor Authentication-server.  Een tweede kenmerk kan worden opgegeven als een back-up voor het eerste.  Het tweede kenmerk wordt alleen gebruikt als het eerste kenmerk geen waarde voor de gebruiker bevat.  De standaardwaarden zijn userPrincipalName en sAMAccountName. |
| Voornaam | Voer de kenmerknaam in van het kenmerk dat de voornaam in een gebruikersrecord bevat.  De standaardwaarde is givenName. |
| Achternaam | Voer de kenmerknaam in van het kenmerk dat de achternaam in een gebruikersrecord bevat.  De standaardwaarde is sn. |
| E-mailadres | Voer de kenmerknaam in van het kenmerk dat het e-mailadres in een gebruikersrecord bevat.  Het e-mailadres wordt gebruikt om via e-mail welkomstberichten en updateberichten naar de gebruiker te verzenden.  De standaardwaarde is mail. |
| Gebruikersgroep | Voer de kenmerknaam in van het kenmerk dat de gebruikersgroep in een gebruikersrecord bevat.  Gebruikersgroep kan worden gebruikt voor het filteren van gebruikers in de agent en in rapporten in de beheerportal van de Multi-Factor Authentication-server. |
| Beschrijving | Voer de kenmerknaam in van het kenmerk dat de beschrijving in een gebruikersrecord bevat.  Beschrijving wordt alleen gebruikt voor zoekopdrachten.  De standaardwaarde is description. |
| Taal telefoonoproep | Voer de kenmerknaam in van het kenmerk dat de korte naam bevat van de taal die moet worden gebruikt voor telefoonoproepen voor de gebruiker. |
| Taal sms-bericht | Voer de kenmerknaam in van het kenmerk dat de korte naam bevat van de taal die moet worden gebruikt voor sms-berichten voor de gebruiker. |
| Taal mobiele app | Voer de kenmerknaam in van het kenmerk dat de korte naam bevat van de taal die moet worden gebruikt voor tekstberichten van mobiele apps voor de gebruiker. |
| Taal OATH-token | Voer de kenmerknaam in van het kenmerk dat de korte naam bevat van de taal die moet worden gebruikt voor sms-berichten met een OATH-token voor de gebruiker. |
| Telefoons | De volgende kenmerken worden gebruikt om telefoonnummers van de gebruiker te importeren of te synchroniseren.  Als voor het telefoontype geen kenmerknaam wordt opgegeven, zal het telefoontype niet beschikbaar zijn bij het importeren vanuit Active Directory of het toevoegen van synchronisatie-items. |
| Telefoon (werk) | Voer de kenmerknaam in van het kenmerk dat het zakelijke telefoonnummer in een gebruikersrecord bevat.  De standaardwaarde is telephoneNumber. |
| Telefoon (thuis) | Voer de kenmerknaam in van het kenmerk dat het persoonlijke telefoonnummer thuis in een gebruikersrecord bevat.  De standaardwaarde is homePhone. |
| Pager | Voer de kenmerknaam in van het kenmerk dat het pagernummer in een gebruikersrecord bevat.  De standaardwaarde is pager. |
| Mobiele telefoon | Voer de kenmerknaam in van het kenmerk dat het mobiel telefoonnummer in een gebruikersrecord bevat.  De standaardwaarde is mobile. |
| Fax | Voer de kenmerknaam in van het kenmerk dat het faxnummer in een gebruikersrecord bevat.  De standaardwaarde is facsimileTelephoneNumber. |
| IP-telefoon | Voer de kenmerknaam in van het kenmerk dat het IP-telefoonnummer in een gebruikersrecord bevat.  De standaardwaarde is ipPhone. |
| Aangepast telefoonnummer | Voer de kenmerknaam in van het kenmerk dat een aangepast telefoonnummer in  |
|  | een gebruikersrecord bevat.  Standaard is dit veld leeg. |
| Toestelnummer | Voer de kenmerknaam in van het kenmerk dat het toestelnummer in een gebruikersrecord bevat.  De waarde van het veld Toestelnummer zal alleen worden gebruikt als het toestelnummer voor het primaire telefoonnummer.  Standaard is dit veld leeg. <br><br>Opmerking: Als het kenmerk Toestelnummer niet wordt opgegeven, kunnen toestelnummers worden opgenomen als onderdeel van het telefoonkenmerk.  Het toestelnummer moet worden voorafgegaan door een 'x', zodat het kan worden geparseerd.  Het nummer 020-123-4567 x890 resulteert bijvoorbeeld in 020-123-4567 als het telefoonnummer en 890 als het toestelnummer. |
| Knop Standaardwaarden herstellen | Klik op de knop Standaardwaarden herstellen om alle kenmerken opnieuw in te stellen op hun standaardwaarde.  De standaardinstellingen werken doorgaans juist met het normale Active Directory- of ADAM-schema. |

Als u kenmerken wilt bewerken, klikt u gewoon op de knop Bewerken op het tabblad Kenmerken.  In het venster dat wordt weergegeven, kunt u de kenmerken bewerken.

![Kenmerken bewerken](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## Synchronisatie
Synchronisatie houdt de Azure Multi-Factor-gebruikersdatabase gesynchroniseerd met de gebruikers in Active Directory of een andere Lightweight Directory Access Protocol (LDAP)-directory.  Het proces is vergelijkbaar met het handmatig importeren van gebruikers uit Active Directory, maar pollt periodiek naar te verwerken wijzigingen in Active Directory-gebruikers en -beveiligingsgroepen.  Het kan ook gebruikers uitschakelen of verwijderen die uit een container of beveiligingsgroep zijn verwijderd en gebruikers verwijderen uit Active Directory.

De Multi-Factor Authentication-service ADSync is een Windows-service die de periodieke polling van Active Directory uitvoert.  Verwar dit niet met Azure AD Sync of Azure AD Connect.  Hoewel de Multi-Factor Authentication-service ADSync is gebouwd op een vergelijkbare codebasis, is deze service specifiek voor de Azure Multi-Factor Authentication-server.  Deze service wordt geïnstalleerd met een status Gestopt en wordt gestart door de service van de Multi-Factor Authentication-server wanneer deze is geconfigureerd om te worden uitgevoerd.  Als u een Multi-Factor Authentication-serverconfiguratie met meerdere servers hebt, kan de Multi-Factor Authentication-service ADSync slechts op één server worden uitgevoerd.

De Multi-Factor Authentication-service ADSync maakt gebruik van de DirSync LDAP-serverextensie die door Microsoft wordt geleverd om efficiënt polls naar wijzigingen uit te voeren.  De aanroeper van dit DirSync-besturingselement moet beschikken over het recht 'directory get changes' (directorywijzigingen ophalen) en het toegangsrecht DS-Replication-Get-Changes voor uitgebreide controles.  Standaard worden deze rechten toegewezen aan de accounts Administrator en LocalSystem op domeincontrollers.  De Multi-Factor Authentication-service AdSync is geconfigureerd om standaard te worden uitgevoerd als LocalSystem.  Daarom kan de service het gemakkelijkst op een domeincontroller worden uitgevoerd.  De service kan worden uitgevoerd als een account met minder machtigingen als u deze configureert om altijd een volledige synchronisatie uit te voeren.  Dit is minder efficiënt, maar vereist minder accountmachtigingen.

Indien deze service geconfigureerd is om LDAP te gebruiken en de LDAP-directory ondersteunt het besturingselement DirSync, zal een poll naar wijzigingen in gebruikers en groepsbeveiligingen op dezelfde manier worden uitgevoerd als bij Active Directory.  Als de LDAP-directory geen ondersteuning biedt voor het besturingselement DirSync, wordt bij elke cyclus een volledige synchronisatie uitgevoerd.

![Synchronisatie](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Gebruik de onderstaande tabel voor meer informatie over elk van de afzonderlijke instellingen op het tabblad Synchronisatie.

| Functie | Beschrijving |
| ------- | ----------- |
| Synchronisatie met Active Directory inschakelen | Als deze optie wordt ingeschakeld, wordt de service van de Multi-Factor Authentication-server gestart om periodiek een poll naar wijzigingen in Active Directory uit te voeren. <br><br>Opmerking: ten minste één synchronisatie-item moet worden toegevoegd en Nu synchroniseren moet worden uitgevoerd voordat de service van de Multi-Factor Authentication-server wijzigingen begint te verwerken. |
| Synchronisatie-interval | Geef op hoelang de service van de Multi-Factor Authentication-server moet wachten tussen het uitvoeren van een poll en het verwerken van wijzigingen. <br><br> Opmerking: Het opgegeven interval is de tijd tussen het begin van elke cyclus.  Als de tijd voor het verwerken van wijzigingen groter is dan het interval, zal de service onmiddellijk opnieuw een poll uitvoeren. |
| Gebruikers verwijderen die niet meer in Active Directory zijn | Als deze optie wordt ingeschakeld, zal de service van de Multi-Factor Authentication-server verwijderde Active Directory-tombstones van gebruikers verwerken en de gerelateerde Multi-Factor Authentication-servergebruiker verwijderen. |
| Altijd een volledige synchronisatie uitvoeren | Als dit selectievakje wordt ingeschakeld, zal de service van de Multi-Factor Authentication-server altijd een volledige synchronisatie uitvoeren.  Als dit selectievakje wordt uitgeschakeld, zal de service van de Multi-Factor Authentication-server een incrementele synchronisatie uitvoeren door alleen een query naar gewijzigde gebruikers uit te voeren.  De standaardwaarde is uitgeschakeld. <br><br> Opmerking: Als dit selectievakje wordt uitgeschakeld, kan een incrementele synchronisatie alleen worden uitgevoerd wanneer de directory het besturingselement DirSync ondersteunt en wanneer het account dat wordt gebruikt om een directorybinding uit te voeren de juiste machtigingen heeft voor het uitvoeren van incrementele DirSync-query's.  Als het account niet de juiste machtigingen heeft of er meerdere domeinen bij de synchronisatie zijn betrokken, is het raadzaam om een volledige synchronisatie uit te voeren. |
| Goedkeuring door beheerder vereisen wanneer het aantal uitgeschakelde of verwijderde gebruikers de drempelwaarde overschrijdt | Synchronisatie-items kunnen worden geconfigureerd voor het uitschakelen of verwijderen van gebruikers die niet langer lid zijn van de container of de beveiligingsgroep van het item.  Als voorzorgsmaatregel kan de goedkeuring door een beheerder worden vereist wanneer het aantal uit te schakelen of te verwijderen gebruikers een drempelwaarde overschrijdt.  Als dit selectievakje wordt ingeschakeld, zal voor de opgegeven drempelwaarde goedkeuring zijn vereist.  De standaardwaarde is 5 en het bereik gaat van 1 tot 999. <br><br> Goedkeuring wordt vergemakkelijkt door eerst een e-mailmelding naar beheerders te verzenden. De e-mailmelding biedt instructies voor het controleren en goedkeuren van de uitschakeling en verwijdering van gebruikers.  Wanneer de gebruikersinterface van de Multi-Factor Authentication-server wordt gestart, wordt om goedkeuring gevraagd. |

Met de knop **Nu synchroniseren** kunt u een volledige synchronisatie uitvoeren voor de opgegeven synchronisatie-items.  Een volledige synchronisatie is vereist wanneer synchronisatie-items worden toegevoegd, gewijzigd, verwijderd of opnieuw gerangschikt.  Deze is ook vereist voordat de Multi-Factor Authentication-service AdSync operationeel zal zijn omdat bij deze synchronisatie het beginpunt wordt ingesteld op basis waarvan de service een poll naar incrementele wijzigingen zal uitvoeren.  Als er wijzigingen in synchronisatie-items zijn aangebracht en er geen volledige synchronisatie is uitgevoerd, wordt u gevraagd Nu synchroniseren te gebruiken wanneer u naar een andere sectie navigeert of de gebruikersinterface sluit.

Met de knop **Verwijderen** kan de beheerder een of meer synchronisatie-items uit de itemlijst van de Multi-Factor Authentication-server verwijderen.

>[AZURE.WARNING]Nadat een synchronisatie-itemrecord is verwijderd, kan het niet worden hersteld. Als u het per ongeluk hebt verwijderd, moet u het synchronisatie-itemrecord opnieuw toevoegen.

De synchronisatie-items worden verwijderd van de Multi-Factor Authentication-server.  De service van de Multi-Factor Authentication-server zal de synchronisatie-items niet langer verwerken. 

Met de knoppen Omhoog en Omlaag kan de beheerder de volgorde van de synchronisatie-items wijzigen.  De volgorde is belangrijk omdat dezelfde gebruiker lid kan zijn van meer dan één synchronisatie-item (bijvoorbeeld van een container en een beveiligingsgroep).  De instellingen die bij de synchronisatie op de gebruiker worden toegepast, zijn afkomstig van het eerste synchronisatie-item in de lijst waaraan de gebruiker is gekoppeld.  Daarom moeten de synchronisatie-items in volgorde van prioriteit worden geplaatst.

>[AZURE.TIP]Na het verwijderen van synchronisatie-items moet een volledige synchronisatie worden uitgevoerd.  Na het rangschikken van synchronisatie-items moet een volledige synchronisatie worden uitgevoerd.  Klik op de knop Nu synchroniseren om een volledige synchronisatie uit te voeren.

## Multi-Factor Auth-servers
Extra Multi-Factor Authentication-servers kunnen worden ingesteld om te fungeren als een back-up-RADIUS-proxy, LDAP-proxy of voor IIS-authenticatie. De configuratie van de synchronisatie wordt gedeeld tussen alle agents. De service van de Multi-Factor Authentication-server kan echter slechts op één van deze agents worden uitgevoerd. Op dit tabblad kunt u de Multi-Factor Authentication-server selecteren die voor synchronisatie moet worden ingeschakeld.

![Multi-Factor Auth-servers](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)


<!--HONumber=Jun16_HO2-->



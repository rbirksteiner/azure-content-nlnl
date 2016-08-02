<properties 
    pageTitle="Cloudresources en on-premises resources beveiligen met behulp van de Azure MFA-server met Windows Server 2012 R2 AD FS" 
    description="Dit is de pagina voor Azure Multi-Factor Authentication waarop wordt beschreven hoe u aan de slag gaat met Azure MFA en AD FS in Windows Server 2012 R2." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>


# Cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server met Windows Server 2012 R2 AD FS

Als uw organisatie is gefedereerd met Azure AD en u hebt resources die zich on-premises of in de cloud bevinden die u wilt beveiligen, kunt u dit doen door de Azure Multi-Factor Authentication-server te gebruiken en deze te configureren voor gebruik met AD FS, zodat meervoudige verificatie wordt geactiveerd voor waardevolle eindpunten.

In deze documentatie wordt beschreven hoe u de Azure Multi-Factor Authentication-server gebruikt met AD FS in Windows Server 2012 R2.  Zie [Cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server met AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md) voor meer informatie over het gebruik van Azure Multi-Factor Authentication met AD FS 2.0.

## Windows Server 2012 R2 AD FS beveiligen met Azure Multi-Factor Authentication-server

Bij de installatie van de Azure Multi-Factor Authentication-server hebt u de volgende twee opties:

- Installeer de Azure Multi-Factor Authentication-server lokaal op dezelfde server als AD FS 
- Installeer de Azure Multi-Factor Authentication-adapter lokaal op de AD FS-server en installeer de MFA-server op een andere computer

Houd rekening met de volgende informatie voordat u begint:

- Het is geen vereiste dat de Azure Multi-Factor Authentication-server op uw federatieve AD FS-server wordt geïnstalleerd. De Multi-Factor Authentication-Adapter voor AD FS moet echter worden geïnstalleerd in Windows Server 2012 R2 waarop AD FS wordt uitgevoerd. U kunt de server op een andere computer installeren als de versie hiervan wordt ondersteund en de AD FS-adapter afzonderlijk installeren op uw federatieve AD FS-server. Zie de procedure hieronder voor instructies voor de afzonderlijke installatie van de adapter.
- Toen de AD FS-adapter van de Multi-Factor Authentication-server werd ontworpen, werd ervan uitgegaan dat AD FS de naam van de relying party zou kunnen doorgeven aan de adapter die als een toepassingsnaam zou kunnen worden gebruikt.  Dit bleek echter niet het geval te zijn.  Bij het gebruik van verificatiemethoden voor sms-berichten of mobiele apps, bevatten de tekenreeksen die in Bedrijfsinstellingen zijn gedefinieerd een tijdelijke aanduiding <$application_name$>.  Deze tijdelijke aanduiding wordt niet vervangen bij het gebruik van de AD FS-adapter.  Hierdoor is het raadzaam om de tijdelijke aanduiding uit de desbetreffende tekenreeksen te verwijderen bij het beveiligen van AD FS.

- Het aangemelde account moet beschikken over bevoegdheden voor het maken van beveiligingsgroepen in Active Directory.

- Met de wizard AD FS-adapter installeren voor Multi-Factor Authentication wordt een beveiligingsgroep met de naam PhoneFactor Admins in uw Active Directory gemaakt en wordt vervolgens het AD FS-serviceaccount van uw federatieve service aan deze groep toegevoegd. Het is raadzaam op uw domeincontroller te controleren of de groep PhoneFactor Admins daadwerkelijk is gemaakt en of het AD FS-serviceaccount deel uitmaakt van deze groep. Voeg, indien nodig, het AD FS-serviceaccount handmatig toe aan de groep PhoneFactor Admins in uw domeincontroller.
- Zie [Deploying the user portal for the Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-portal.md) (De gebruikersportal voor de Azure Multi-Factor Authentication-server implementeren) voor meer informatie over het installeren van de webservice-SDK met de gebruikersportal.
  

### De Azure Multi-Factor Authentication-server lokaal installeren op dezelfde server als AD FS

1. Download en installeer de Azure Multi-Factor Authentication-server op uw federatieve AD FS-server. Zie [Getting started with the Azure Multi-Factor Authentication Server](multi-factor-authentication-get-started-server.md) (Aan de slag met de Azure Multi-Factor Authentication-server) voor meer informatie over het installeren van de Azure Multi-Factor Authentication-server.
2. Selecteer in de gebruikersinterface van de Azure Multi-Factor Authentication-server het AD FS-pictogram en selecteer daarna de opties Registreren van gebruikers toestaan en Toestaan dat gebruikers de methode selecteren.
3. Selecteer eventueel aanvullende opties.
4. Klik op AD FS-adapter installeren.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Als de computer lid is van een domein en de Active Directory-configuratie voor de beveiliging van de communicatie tussen de AD FS-adapter en de Multi-Factor Authentication-service niet is voltooid, wordt de Active Directory-stap weergegeven.  Klik op de knop Volgende om deze configuratie automatisch te voltooien of schakel het selectievakje Automatische Active Directory-configuratie overslaan en instellingen handmatig configureren in en klik op Volgende.
6. Als de computer nog niet lid is van een domein en de Lokale groep-configuratie voor de  beveiliging van de communicatie tussen de AD FS-adapter en de Multi-Factor Authentication-service niet is voltooid, wordt de Lokale groep-stap weergegeven.  Klik op de knop Volgende om deze configuratie automatisch te voltooien of schakel het selectievakje Automatische lokale groep-configuratie overslaan en instellingen handmatig configureren in en klik op Volgende.
7. Hierdoor wordt de installatiewizard weergegeven. Klik op Volgende om de Azure Multi-Factor Authentication-server toe te staan om de PhoneFactor Admins-groep te maken en de AD FS-serviceaccount toe te voegen aan de PhoneFactor Admins-groep.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Klik bij de stap Installatieprogramma uitvoeren op Volgende.
9. Klik in het installatieprogramma voor de Multi-Factor Authentication AD FS-adapter op Volgende.
10. Klik op Sluiten nadat de installatie is voltooid.
11. Nu dat de adapter is geïnstalleerd, moet deze bij AD FS worden geregistreerd. Open Windows PowerShell en voer het volgende uit: C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Nu moet Global Authentication Policy (Algemeen verificatiebeleid) in AD FS worden bewerkt om onze zojuist geregistreerde adapter te gebruiken. In de AD FS-beheerconsole gaat u naar het knooppunt Authentication Policies (Verificatiebeleid) en klikt u onder de sectie Multi-Factor Authentication op de koppeling Bewerken naast de subsectie Algemene instellingen. Selecteer in het venster Edit Global Authentication Policy (Algemeen verificatiebeleid bewerken) Multi-Factor Authentication als een aanvullende verificatiemethode en klik op OK. De adapter wordt geregistreerd als WindowsAzureMultiFactorAuthentication.  U moet de AD FS-service opnieuw starten voordat de registratie van kracht wordt.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

De Multi-Factor Authentication-server is nu ingesteld voor gebruik als een extra verificatieprovider voor gebruik met AD FS.

## De AD FS-adapter zelfstandig installeren met behulp van de webservice-SDK
1. Installeer de webservice-SDK op de server waarop Multi-Factor Authentication-server wordt uitgevoerd.
2. Kopieer de bestanden MultiFactorAuthenticationAdfsAdapterSetup64.msi, Register-MultiFactorAuthenticationAdfsAdapter.ps1, Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 en MultiFactorAuthenticationAdfsAdapter.config uit de map \Program Files\Multi-Factor Authentication Server naar de server waarop u de AD FS-adapter wilt installeren.
3. Voer het bestand MultiFactorAuthenticationAdfsAdapterSetup64.msi uit.
4. Klik in het installatieprogramma van Multi-Factor Authentication AD FS-adapter op Volgende om de installatie uit te voeren.
5. Klik op de knop Sluiten nadat de installatie is voltooid.
6. Bewerk het bestand MultiFactorAuthenticationAdfsAdapter.config als volgt:

Stap MultiFactorAuthenticationAdfsAdapter.config| Substap
:------------- | :------------- |
Stel het knooppunt UseWebServiceSdk in op true (waar).||
Stel WebServiceSdkUrl in op de URL van de webservice-SDK voor Multi-Factor Authentication.||
Optie 1 - Configureer de webservice-SDK met een gebruikersnaam en wachtwoord.|<ol><li>Stel WebServiceSdkUsername in op een account dat lid is van de veiligheidsgroep PhoneFactor Admins.  Gebruik de indeling <domain>\<username>.<li>Stel WebServiceSdkPassword in op het juiste accountwachtwoord.</li></ol>
Optie 2 - Configureer de webservice-SDK met een clientcertificaat.|<ol><li>Verkrijg een certificaat van een certificeringsinstantie voor de server waarop de webservice-SDK wordt uitgevoerd.  Zie [Obtain Client Certificate ](https://technet.microsoft.com/library/cc770328(v=ws.10).aspx) (Clientcertificaat verkrijgen) voor meer informatie over het verkrijgen van een certificaat.</li><li>Importeer het clientcertificaat in het persoonlijke certificaatarchief van de lokale computer op de server waarop de webservice-SDK wordt uitgevoerd.  Opmerking: zorg ervoor dat het openbaar certificaat van de certificeringsinstantie zich bevindt bij de vertrouwde basiscertificaten.</li><li>Exporteer de openbare en persoonlijke sleutels van het clientcertificaat naar een PFX-bestand.</li><li>Exporteer de openbare sleutel in base 64-indeling naar een CER-bestand.</li><li>Controleer in Serverbeheer of het onderdeel Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication (Verificatie van IIS-clientcertificaattoewijzing) is geïnstalleerd.</li><li>Als dit niet is geïnstalleerd, kiest u Functies en onderdelen toevoegen om dit onderdeel toe te voegen.</li><li>Dubbelklik in IIS-beheer op Configuratie-editor voor de website die de virtuele map van de webservice-SDK bevat.  Opmerking: het is heel belangrijk dat u dit doet op het niveau van de website en niet op dat van de virtuele map.</li><li>Navigeer naar de sectie system.webServer/security/authentication/iisClientCertificateMappingAuthentication.</li><li>Stel Ingeschakeld in op true (waar).</li><li>Stel oneToOneCertificateMappingsEnabled in op true (waar).</li><li>Klik op de knop ... naast oneToOneMappings.</li><li>Klik op de koppeling Toevoegen.</li><li>Open het CER-bestand dat eerder is geëxporteerd in base 64-indeling.  Verwijder -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- en alle regeleinden.  Kopieer de resulterende tekenreeks.</li><li>Stelt het certificaat in op de tekenreeks die u in de vorige stap hebt gekopieerd.</li><li>Stel Ingeschakeld in op true (waar).</li><li>Stel Gebruikersnaam in op een account dat lid is van de veiligheidsgroep PhoneFactor Admins.  Gebruik de indeling <domain>\<username>.</li><li>Stel het wachtwoord in op het geschikte accountwachtwoord.</li><li>Sluit de editor voor verzamelingen.</li><li>Klik op de koppeling Toepassen.</li><li>Navigeer naar de virtuele map van de webservice-SDK.</li><li>Dubbelklik op Verificatie.</li><li>Controleer of ASP.NET-imitatie en Basisverificatie zijn ingeschakeld en of alle overige items zijn uitgeschakeld.</li><li>Navigeer opnieuw naar de virtuele map van de webservice-SDK.</li><li>Dubbelklik op SSL-instellingen.</li><li>Stel Clientcertificaten in op Accepteren en klik op Toepassen.</li><li>Kopieer het PFX-bestand dat eerder is geëxporteerd naar de server waarop de AD FS-adapter wordt uitgevoerd.</li><li>Importeer het PFX-bestand in het persoonlijke certificaatarchief van de lokale computer.</li><li>Klik met de rechtermuisknop en kies Persoonlijke sleutels beheren en verleen leestoegang tot het account waarmee de Active Directory Federation Services-service is aangemeld.</li><li>Open het clientcertificaat en kopieer de vingerafdruk van het tabblad Details.</li><li>Stel in het bestand MultiFactorAuthenticationAdfsAdapter.config WebServiceSdkCertificateThumbprint in op de tekenreeks die u in de vorige stap hebt gekopieerd.</li></ol>
Bewerk het script Register-MultiFactorAuthenticationAdfsAdapter.ps1 door ConfigurationFilePath <path> toe te voegen aan het einde van de opdracht Register-AdfsAuthenticationProvider, waarbij <path> het volledige pad is naar het bestand MultiFactorAuthenticationAdfsAdapter.config.|


Voer nu het script \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 uit in PowerShell om de adapter te registreren.  De adapter wordt geregistreerd als WindowsAzureMultiFactorAuthentication.  U moet de AD FS-service opnieuw starten voordat de registratie van kracht wordt. 




























 

 


 

 


 





 


 

























































































 


 

 






 


<!--HONumber=Jun16_HO2-->



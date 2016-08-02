<!--author=alkohli last changed: 12/01/15-->


#### Het apparaat configureren en registreren

1. Open de Windows PowerShell-interface op de seriële console van het StorSimple-apparaat. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) voor instructies. **Volg de procedure exact, omdat u anders geen toegang hebt tot de console.**

2. Druk in de geopende sessie eenmaal op Enter om een opdrachtprompt weer te geven. 

3. U wordt gevraagd de taal te kiezen die u voor uw apparaat wilt instellen. Geef de taal op en druk op Enter. 

    ![Apparaat 1 configureren en registreren met StorSimple](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)

4. Kies in het weergegeven menu van de seriële console optie 1 om u aan te melden met volledige toegang. 

    ![Apparaat 2 registreren met StorSimple](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
  
     Voer de stappen 5 tot en met 12 uit om de minimale vereiste netwerkinstellingen voor uw apparaat te configureren. **Deze configuratiestappen moeten worden uitgevoerd op de actieve controller van het apparaat.** In het menu van de seriële console wordt de controllerstatus in het bannerbericht aangegeven. Als u niet met de actieve controller verbonden bent, verbreekt u de verbinding en maakt u vervolgens verbinding met de actieve controller.

5. Typ uw wachtwoord bij de opdrachtprompt. Het standaardapparaatwachtwoord is **Password1**.

6. Typ de volgende opdracht:

     `Invoke-HcsSetupWizard` 

7. Er wordt een instellingenwizard weergegeven om u te helpen bij het configureren van de netwerkinstellingen voor het apparaat. Geef de volgende informatie op: 
   - IP-adres voor de DATA 0-netwerkinterface
   - Subnetmasker
   - Gateway
   - IP-adres voor de primaire DNS-server
   - IP-adres voor de primaire NTP-server
   
      > [AZURE.NOTE] U moet misschien een paar minuten wachten tot het subnetmasker en de DNS-instellingen zijn toegepast. Als het foutbericht Het apparaat is niet gereed wordt weergegeven, controleert u de fysieke netwerkverbinding voor de DATA 0-netwerkinterface van uw actieve controller.

8. Configureer desgewenst uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar **houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd**. Zie [Configure web proxy for your device](../articles/storsimple/storsimple-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie. Als u problemen ondervindt tijdens deze stap, raadpleegt u de richtlijnen voor probleemoplossing voor [fouten tijdens de webproxyconfiguratie](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).
 

      > [AZURE.NOTE] U kunt op elk gewenst moment op Ctrl+C drukken om de instellingenwizard af te sluiten. Alle instellingen die u hebt toegepast voordat u deze opdracht gaf, blijven behouden.

9. Uit veiligheidsoverwegingen is het beheerderswachtwoord van het apparaat na de eerste sessie verlopen en moet u het voor volgende sessies wijzigen. Als daarom wordt gevraagd, geeft u een beheerderswachtwoord voor het apparaat op. Een geldig beheerderswachtwoord voor een apparaat moet 8 tot 15 tekens bevatten. Het wachtwoord moet een combinatie van kleine letters, hoofdletters, cijfers en speciale tekens zijn.

10. Het StorSimple Snapshot Manager-wachtwoord wordt hier ook ingesteld. U kunt dit wachtwoord gebruiken wanneer u een apparaat verifieert met uw Windows-host waarop StorSimple Snapshot Manager wordt uitgevoerd. Wanneer daarom wordt gevraagd, geeft u een wachtwoord van 14 of 15 tekens op. Het wachtwoord moet een combinatie van drie van de volgende elementen bevatten: kleine letters, hoofdletters, cijfers en speciale tekens. 

    ![Apparaat 4 registreren met StorSimple](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)

    U kunt het StorSimple Snapshot Manager-wachtwoord opnieuw instellen vanuit de interface van de StorSimple Manager-service. Ga voor gedetailleerde stappen naar [Change the StorSimple passwords using the StorSimple Manager service](../articles/storsimple/storsimple-change-passwords.md) (De StorSimple-wachtwoorden wijzigen met de StorSimple Manager-service).

    Als u problemen ondervindt tijdens deze stap, raadpleegt u de richtlijnen voor probleemoplossing voor [fouten met betrekking tot wachtwoorden](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).

11. Bij de laatste stap in de instellingenwizard wordt uw apparaat geregistreerd met de StorSimple Manager-service. Hiervoor hebt u de serviceregistratiesleutel nodig die u in stap 2 hebt verkregen. Nadat u de registratiesleutel hebt opgegeven, moet u mogelijk twee tot drie minuten wachten voordat het apparaat is geregistreerd.

    Voor fouten bij de registratie van apparaten raadpleegt u [Errors during device registration](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration) (Fouten tijdens apparaatregistratie). In [Step-by-step troubleshooting example](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example) (Voorbeeld van stapsgewijze probleemoplossing) vindt u gedetailleerde informatie voor het oplossen van problemen.

12. Nadat het apparaat is geregistreerd, wordt er een gegevensversleutelingssleutel van de service weergegeven. Kopieer deze sleutel en bewaar deze op een veilige plaats.
    
    > [AZURE.WARNING] Deze sleutel is vereist bij de serviceregistratiesleutel om extra apparaten te registreren met de StorSimple Manager-service. Zie [StorSimple security](../articles/storsimple/storsimple-security.md) (StorSimple-beveiliging) voor meer informatie over deze sleutel.

     ![Apparaat 6 registreren met StorSimple](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)

     Kopieer de tekst uit het venster van de seriële console gewoon door de tekst te selecteren. U kunt de tekst vervolgens op het klembord of in een teksteditor plakken. Kopieer de gegevensversleutelingssleutel van de service NIET met Ctrl+C. Als u op Ctrl+C drukt, wordt de instellingenwizard afgesloten. Als gevolg hiervan worden het beheerderswachtwoord van het apparaat en het StorSimple Snapshot Manager-wachtwoord niet gewijzigd en worden de standaardwachtwoorden opnieuw ingesteld.

13. Sluit de seriële console af.

14. Ga terug naar de klassieke Azure-portal en voer de volgende stappen uit:
  1. Dubbelklik op de StorSimple Manager-service om de pagina **Quick Start** te openen.
  2. Klik op **Verbonden apparaten weergeven**.
  3. Controleer op de pagina **Apparaten** aan de hand van de status of het apparaat verbinding heeft met de service. Het apparaat moet de status **Online** hebben. Als de status van het apparaat **Offline** is, moet u een paar minuten wachten totdat het apparaat online is.
   
    ![Pagina StorSimple-apparaten](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
  
      > [AZURE.IMPORTANT] Als het apparaat online is, sluit u de netwerkkabels aan die u in het begin van deze stap hebt ontkoppeld.

Nadat het apparaat is geregistreerd maar de status Offline blijft, kunt u `Test-HcsmConnection -Verbose` uitvoeren om te controleren of de netwerkconnectiviteit in orde is. Ga voor gedetailleerde informatie over het gebruik van deze cmdlet naar [cmdlet reference for Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) (cmdlet-naslaginformatie voor Test-HcsmConnection).

![Video beschikbaar](./media/storsimple-configure-and-register-device/Video_icon.png) **Video beschikbaar**

Als u een video wilt bekijken over het configureren en registreren van uw apparaat via Windows PowerShell voor StorSimple, klikt u [hier](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).


<!--HONumber=Jun16_HO2-->



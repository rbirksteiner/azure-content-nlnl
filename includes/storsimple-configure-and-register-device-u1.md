<!--author=alkohli last changed: 02/22/2016-->


### Het apparaat configureren en registreren

1. Open de Windows PowerShell-interface op de seriële console van het StorSimple-apparaat. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](#use-putty-to-connect-to-the-device-serial-console) voor instructies. **Volg de procedure exact, omdat u anders geen toegang hebt tot de console.**

2. Druk in de geopende sessie eenmaal op Enter om een opdrachtprompt weer te geven. 

3. U wordt gevraagd de taal te kiezen die u voor uw apparaat wilt instellen. Geef de taal op en druk op Enter. 

    ![Apparaat 1 configureren en registreren met StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice1-U1-include.png)

4. Kies in het weergegeven menu van de seriële console optie 1 om u aan te melden met volledige toegang. 

    ![Apparaat 2 registreren met StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice2_U1-include.png)
  
     Voer de stappen 5 tot en met 12 uit om de minimale vereiste netwerkinstellingen voor uw apparaat te configureren. **Deze configuratiestappen moeten worden uitgevoerd op de actieve controller van het apparaat.** In het menu van de seriële console wordt de controllerstatus in het bannerbericht aangegeven. Als u niet met de actieve controller verbonden bent, verbreekt u de verbinding en maakt u vervolgens verbinding met de actieve controller.

5. Typ uw wachtwoord bij de opdrachtprompt. Het standaardapparaatwachtwoord is **Password1**.

6. Typ de volgende opdracht: `Invoke-HcsSetupWizard`. 

7. Er wordt een instellingenwizard weergegeven om u te helpen bij het configureren van de netwerkinstellingen voor het apparaat. Geef de volgende informatie op: 
   - IP-adres voor de DATA 0-netwerkinterface
   - Subnetmasker
   - Gateway
   - IP-adres voor de primaire DNS-server
    
        De netwerkinstellingen worden na elke stap in het proces gevalideerd.
   
      > [AZURE.NOTE] U moet misschien een paar minuten wachten tot het subnetmasker en de DNS-instellingen zijn toegepast. Als het foutbericht Controleer de netwerkverbinding met Data 0 wordt weergegeven, controleert u de fysieke netwerkverbinding voor de DATA 0-netwerkinterface van uw actieve controller.

8. Configureer desgewenst uw webproxyserver. De configuratie van uw webproxy is weliswaar optioneel, maar **houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd**. Zie [Configure web proxy for your device](../articles/storsimple/storsimple-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie.

9. Configureer een primaire NTP-server voor uw apparaat. NTP-servers zijn vereist, omdat uw apparaat de tijd moet synchroniseren voor verificatie met uw cloudserviceproviders. Zorg ervoor dat in uw netwerk NTP-verkeer kan worden doorgegeven van uw datacenter naar internet. Als dit niet mogelijk is, geeft u een interne NTP-server op. 
 
10. Uit veiligheidsoverwegingen is het beheerderswachtwoord van het apparaat na de eerste sessie verlopen en moet u het nu wijzigen. Als daarom wordt gevraagd, geeft u een beheerderswachtwoord voor het apparaat op. Een geldig beheerderswachtwoord voor een apparaat moet 8 tot 15 tekens bevatten. Het wachtwoord moet drie van de volgende elementen bevatten: kleine letters, hoofdletters, cijfers en speciale tekens.

    <br/>![Apparaat 5 registreren met StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice5_U1-include.png)

11. Bij de laatste stap in de instellingenwizard wordt uw apparaat geregistreerd met de StorSimple Manager-service. Hiervoor hebt u de serviceregistratiesleutel nodig die u in stap 2 hebt verkregen. Nadat u de registratiesleutel hebt opgegeven, moet u mogelijk twee tot drie minuten wachten voordat het apparaat is geregistreerd.

      > [AZURE.NOTE] U kunt op elk gewenst moment op Ctrl+C drukken om de instellingenwizard af te sluiten. Als u alle netwerkinstellingen (IP-adres voor Data 0, subnetmasker en gateway) hebt ingevoerd, blijven uw gegevens behouden.

    ![Apparaat 6 registreren met StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice6_U1-include.png)

12. Nadat het apparaat is geregistreerd, wordt er een gegevensversleutelingssleutel van de service weergegeven. Kopieer deze sleutel en bewaar deze op een veilige plaats. **Deze sleutel is vereist bij de serviceregistratiesleutel om extra apparaten te registreren met de StorSimple Manager-service.** Zie [StorSimple security](../articles/storsimple/storsimple-security.md) (StorSimple-beveiliging) voor meer informatie over deze sleutel.
    
    ![Apparaat 7 registreren met StorSimple](./media/storsimple-configure-and-register-device-u1/HCS_RegisterYourDevice7_U1-include.png)    

      > [AZURE.NOTE] Kopieer de tekst uit het venster van de seriële console gewoon door de tekst te selecteren. U kunt de tekst vervolgens op het klembord of in een teksteditor plakken. Kopieer de gegevensversleutelingssleutel van de service NIET met Ctrl+C. Als u op Ctrl+C drukt, wordt de instellingenwizard afgesloten. Als gevolg hiervan wordt het beheerderswachtwoord van het apparaat niet gewijzigd en wordt het standaardwachtwoord opnieuw ingesteld.

13. Sluit de seriële console af.

14. Ga terug naar de klassieke Azure-portal en voer de volgende stappen uit:
  1. Dubbelklik op de StorSimple Manager-service om de pagina **Quick Start** te openen.
  2. Klik op **Verbonden apparaten weergeven**.
  3. Controleer op de pagina **Apparaten** aan de hand van de status of het apparaat verbinding heeft met de service. Het apparaat moet de status **Online** hebben.
   
        ![Pagina StorSimple-apparaten](./media/storsimple-configure-and-register-device-u1/HCS_DevicesPageM_U1-include.png) 
  
        Als de status van het apparaat **Offline** is, moet u een paar minuten wachten totdat het apparaat online is. 

        Als het apparaat na een paar minuten nog steeds offline is, moet u ervoor zorgen dat uw firewallnetwerk is geconfigureerd zoals wordt beschreven in [Netwerkvereisten voor uw StorSimple-apparaat](../articles/storsimple/storsimple-system-requirements.md). 

        Controleer of poort 9354 open is voor uitgaande communicatie, omdat de servicebus deze poort gebruikt voor de communicatie van de StorSimple Manager-service naar het apparaat.
     
       



<!--HONumber=Jun16_HO2-->



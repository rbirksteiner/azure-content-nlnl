<!--author=alkohli last changed: 9/17/15-->

#### De minimale StorSimple-apparaatinstelling voltooien

1. Selecteer het apparaat en klik op **Quick Start**. Klik op **Apparaatinstelling voltooien** om de wizard Apparaat configureren te starten.

2. Ga in het dialoogvenster **Basisinstellingen** van de wizard Apparaat configureren als volgt te werk:
  1. Geef een **beschrijvende naam** voor het apparaat op. De standaardapparaatnaam bevat informatie zoals het model en serienummer van het apparaat. U kunt een beschrijvende naam van maximaal 64 tekens toewijzen om uw apparaat te beheren.
  2. Stel de **tijdzone** in op basis van de geografische regio waarin het apparaat wordt geïmplementeerd. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.
  3. Geef onder **DNS-instellingen** het adres voor uw **secundaire DNS-server** op. Als u IPv6 gebruikt, wordt het veld ingevuld op basis van het IPv6-voorvoegsel dat is opgegeven in de Windows PowerShell-interface. 
  Als de secundaire DNS-server niet is geconfigureerd, kunt u de apparaatconfiguratie niet opslaan.
  4. Schakel onder de iSCSI-interfaces ten minste één netwerk voor iSCSI in. Ten minste één netwerkinterface moet worden ingeschakeld voor de cloud en één interface moet worden ingeschakeld voor iSCSI. DATA 0 is automatisch ingeschakeld voor de cloud.
 
      ![Basisinstellingen voor de minimale StorSimple-apparaatinstelling](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Klik op het pijlpictogram. ![StorSimple-pijlpictogram](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4. Geef in het dialoogvenster **Netwerkinterfaces** de vaste IP-adressen voor Controller 0 en Controller 1 op. **De vaste IP-adressen voor de controllers moeten beschikbare IP-adressen binnen het subnet zijn die toegankelijk zijn voor het IP-adres van het apparaat.** Als de DATA 0-interface is geconfigureerd voor IPv4, moeten de vaste IP-adressen worden opgegeven in de IPv4-indeling. Als u een voorvoegsel voor IPv6-configuratie hebt opgegeven, worden de vaste IP-adressen automatisch in deze velden ingevuld.


    ![Netwerkinterfaces voor de minimale StorSimple-apparaatinstelling](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    Omdat de vaste IP-adressen voor de controller worden gebruikt om de updates aan het apparaat te leveren, moeten de vaste IP-adressen routeerbaar zijn en verbinding kunnen maken met internet. U kunt controleren of uw vaste IP-adressen voor de controller routeerbaar zijn met de cmdlet [Test-HcsmConnection][Test]. In het volgende voorbeeld is te zien dat de vaste IP-adressen voor de controller worden doorgestuurd naar internet en dat deze toegang hebben tot de Microsoft Update-servers. 

     ![Test-HcsmConnection met routeerbare IP-adressen](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

5. Klik op het vinkje ![Vinkje voor StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
  U keert terug naar de pagina **Quick Start** van het apparaat.

 > [AZURE.NOTE] U kunt alle andere apparaatinstellingen op elk gewenst moment wijzigen door de pagina **Configureren** te openen.

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx


<!--HONumber=Jun16_HO2-->



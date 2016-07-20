<!--author=alkohli last changed: 9/17/15-->

#### De minimale StorSimple-apparaatinstelling voltooien

1. Selecteer het apparaat op de pagina **Apparaten** en klik op de pijl naast de naam van het apparaat om naar de specifieke apparaatpagina te gaan. 

    ![Pagina met apparaten terwijl het apparaat online is](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 

2. Klik op het pictogram van Quick Start ![Pictogram Quick Start](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png) om de pagina Quick Start voor het apparaat te openen. Klik op **Apparaatinstelling voltooien** om de wizard **Apparaat configureren** te starten.

    ![De pagina Quick Start voor het apparaat](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)

2. Ga op de pagina **Basisinstellingen** als volgt te werkt:
  1. Geef een **beschrijvende naam** voor het apparaat op. De standaardapparaatnaam bevat informatie zoals het model en serienummer van het apparaat. U kunt een beschrijvende naam van maximaal 64 tekens toewijzen om uw apparaat te beheren.
  2. Stel de **tijdzone** in op basis van de geografische regio waarin het apparaat wordt geïmplementeerd. Het apparaat zal deze tijdzone gebruiken voor alle geplande bewerkingen.
  3. Geef onder **DNS-instellingen** het adres voor uw **secundaire DNS-server** op. Als u IPv6 gebruikt, wordt het veld ingevuld op basis van het IPv6-voorvoegsel dat is opgegeven in de Windows PowerShell-interface. 
  Als de secundaire DNS-server niet is geconfigureerd, kunt u de apparaatconfiguratie niet opslaan.
  4. Schakel onder de iSCSI-interfaces ten minste één netwerk voor iSCSI in. Ten minste één netwerkinterface moet worden ingeschakeld voor de cloud en één interface moet worden ingeschakeld voor iSCSI. DATA 0 is automatisch ingeschakeld voor de cloud.
 
      ![Basisinstellingen voor de minimale StorSimple-apparaatinstelling](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)

3. Klik op het pijlpictogram. ![StorSimple-pijlpictogram](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)

4. Geef op de pagina **Netwerkinterfaces** de vaste IP-adressen voor Controller 0 en Controller 1 op. Als de DATA 0-interface is geconfigureerd voor IPv4, moeten de vaste IP-adressen worden opgegeven in de IPv4-indeling. Als u een voorvoegsel voor IPv6-configuratie hebt opgegeven, worden de vaste IP-adressen automatisch in deze velden ingevuld.


    > [AZURE.NOTE] 
    > 
    > - De vaste IP-adressen voor de controllers moeten beschikbare IP-adressen binnen het subnet zijn die toegankelijk zijn voor het IP-adres van het apparaat.
    > - Omdat de vaste IP-adressen voor de controller worden gebruikt om de updates aan het apparaat te leveren, moeten de vaste IP-adressen routeerbaar zijn en verbinding kunnen maken met internet.

    ![Netwerkinterfaces voor de minimale StorSimple-apparaatinstelling](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

5. Klik op het vinkje ![Vinkje voor StorSimple](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png).
  U keert terug naar de pagina **Quick Start** van het apparaat.

 > [AZURE.NOTE] U kunt alle andere apparaatinstellingen op elk gewenst moment wijzigen door de pagina **Configureren** te openen.

![Video beschikbaar](./media/storsimple-complete-minimum-device-setup/Video_icon.png) **Video beschikbaar**

Als u een video wilt bekijken over het uitvoeren van de minimale apparaatinstelling, klikt u [hier](https://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/).


<!--HONumber=Jun16_HO2-->



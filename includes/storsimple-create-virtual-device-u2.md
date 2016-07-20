#### Een virtueel apparaat maken

1.  Ga in Azure Portal naar de **StorSimple Manager**-service.

2. Ga naar de pagina **Apparaten**. Klik op **Virtueel apparaat maken** aan de onderkant van de pagina **Apparaten**.

3. Geef in het **dialoogvenster Virtueel apparaat maken** de volgende gegevens op.

     ![Virtueel StorSimple-apparaat maken](./media/storsimple-create-virtual-device-u2/CreatePremiumsva1.png)

    1. **Naam**: een unieke naam voor uw virtuele apparaat.


    2. **Model**: het model van het virtuele apparaat. Dit veld wordt alleen weergegeven als u Update 2 of hoger uitvoert. Een 8010-apparaatmodel biedt 30 TB Standard-opslag terwijl 8020 64 TB Premium-opslag heeft. Geef 8010 op
    3.  om ophaalscenario's op itemniveau vanuit back-ups te implementeren. Selecteer 8020 om workloads met hoge prestaties en lage latentie te implementeren of voor gebruik als een tweede apparaat voor herstel na noodgevallen.
     
    4. **Versie**: kies de versie van het virtuele apparaat. Als het 8020-apparaatmodel wordt geselecteerd, wordt dit versieveld niet weergegeven voor de gebruiker. Deze optie ontbreekt als op alle fysieke apparaten die zijn geregistreerd met deze service Update 1 (of hoger) wordt uitgevoerd. Dit veld wordt alleen weergegeven als u een combinatie van fysieke pre-Update 1- en Update 1-apparaten hebt geregistreerd met dezelfde service. Aangezien de versie van het virtuele apparaat bepaalt welk fysiek apparaat voor failover kan dienen of van welk fysiek apparaat u een kloon kunt maken, is het belangrijk dat u de juiste versie van het virtuele apparaat maakt. Selecteer:

       - Versie Update 0.3 voor failover of DR vanaf een fysiek apparaat met Update 0.3 of lager. 
       - Versie Update 1 voor failover of klonen vanaf een fysiek apparaat met Update 1 (of lager). 
       
    
    5. **Virtueel netwerk**: geef een virtueel netwerk op dat u wilt gebruiken met dit virtuele apparaat. Als u Premium-opslag (Update 2 of hoger) gebruikt, moet u een virtueel netwerk selecteren dat wordt ondersteund met het premium-opslagaccount. De niet-ondersteunde virtuele netwerken worden lichter gekleurd weergegeven in de vervolgkeuzelijst. U wordt gewaarschuwd als u een niet-ondersteund virtueel netwerk selecteert. 

    5. **Opslagaccount voor het maken van virtuele apparaten**: selecteer een opslagaccount voor de installatiekopie van het virtuele apparaat tijdens het inrichten. Dit opslagaccount moet zich in dezelfde regio bevinden als het virtuele apparaat en virtuele netwerk. Het mag niet worden gebruikt voor de opslag van gegevens door ofwel het fysieke of het virtuele apparaat. Voor dit doel wordt standaard een nieuw opslagaccount gemaakt. Als u echter al een opslagaccount hebt dat hiervoor geschikt is, kunt u dit in de lijst selecteren. Als u een Premium virtueel apparaat maakt, worden in de vervolgkeuzelijst alleen Premium-opslagaccounts weergegeven. 

        >[AZURE.NOTE] Het virtuele apparaat kan alleen met de Azure Storage-accounts werken. Andere cloudserviceproviders, zoals Amazon, HP en OpenStack (die worden ondersteund voor het fysieke apparaat), worden niet ondersteund voor het virtuele StorSimple-apparaat.
    
    1. Klik op het vinkje om aan te geven dat u begrijpt dat de gegevens die zijn opgeslagen op de virtuele apparaat, in een Microsoft-datacenter worden gehost. Wanneer u alleen een fysiek apparaat gebruikt, wordt de versleutelingssleutel op uw apparaat opgeslagen. Daarom kan Microsoft deze niet ontsleutelen. 
     
        Wanneer u een virtueel apparaat gebruikt, worden zowel de versleutelingssleutel als de ontsleutelingssleutel opgeslagen in Microsoft Azure. Zie de [beveiligingsoverwegingen voor het gebruik van een virtueel apparaat](storsimple-security/#storsimple-virtual-device-security) voor meer informatie.
    2. Klik op het vinkje om het virtuele apparaat te maken. Het kan ongeveer 30 minuten duren voordat het apparaat is ingericht.

    ![Aanmaakfase voor een virtueel StorSimple-apparaat](./media/storsimple-create-virtual-device-u2/StorSimple_VirtualDeviceCreating1M.png)

    



<!--HONumber=Jun16_HO2-->



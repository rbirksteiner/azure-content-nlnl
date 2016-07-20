<!--author=SharS last changed: 02/29/2016-->

#### Een volume maken

1. Klik op het apparaat op de pagina **Quick Start** en klik vervolgens op **Een volume toevoegen**. Hiermee start u de wizard Volume toevoegen.

2. Typ in de wizard onder **Basisinstellingen**:

    4. een **naam** voor het volume.
    5. Selecteer in de vervolgkeuzelijst het **gebruikstype** voor het volume. Voor workloads waarvoor lokale garanties, lage latenties en betere prestaties vereist zijn, selecteert u een **lokaal vastgemaakt** volume. Voor alle overige gegevens selecteert u een **gelaagd** volume. Als u dit volume gebruikt voor de archivering van gegevens, schakelt u **Dit volume gebruiken voor minder frequent gebruikte gearchiveerde gegevens** in. 
    
        Een lokaal vastgemaakt volume is compact ingericht en zorgt ervoor dat de primaire gegevens op het volume lokaal op het apparaat blijven en niet naar de cloud worden gelekt.  Als u een lokaal vastgemaakt volume maakt, controleert het apparaat de beschikbare ruimte op de lokale lagen om ervoor te zorgen dat het volume het juiste formaat heeft. Het maken van een lokaal vastgemaakt volume heeft als risico dat bestaande gegevens van het apparaat naar de cloud worden gelekt. Ook duurt het maken van het volume mogelijk lang. De totale tijd is afhankelijk van de grootte van het betreffende volume, de beschikbare netwerkbandbreedte en de gegevens op uw apparaat. 

        Een gelaagd volume is dun ingericht en kan zeer snel worden gemaakt. Als u het gelaagde volume gebruikt voor archivering van gegevens en u **Dit volume gebruiken voor minder vaak gebruikte gegevensarchivering** selecteert, verandert de chunkgrootte voor de ontdubbeling voor het volume tot 512 KB. Als dit veld niet is ingeschakeld, gebruikt het overeenkomstige gelaagde volume een chunkgrootte van 64 KB. Een grotere chunkgrootte voor de ontdubbeling zorgt ervoor dat het apparaat sneller grotere archiveringsgegevens naar de cloud kan overzetten.

    3. Geef de **ingerichte capaciteit** voor het volume op. Noteer de beschikbare capaciteit op basis van het volumetype dat is geselecteerd. De grootte van het opgegeven volume mag niet groter zijn dan de beschikbare ruimte.

        U kunt lokaal vastgemaakte volumes inrichten tot maximaal 8 TB, of gelaagde volumes op het 8100-apparaat tot 200 TB. Op een groter 8600-apparaat kunt u lokaal vastgemaakte volumes inrichten tot 20 TB en gelaagde volumes tot maximaal 500 TB. Aangezien lokale ruimte op het apparaat vereist is voor het hosten van de werkset van gelaagde volumes, heeft het maken van lokaal vastgemaakte volumes invloed op de beschikbare schijfruimte voor het inrichten van gelaagde volumes. Als u dus een lokaal vastgemaakt volume maakt, wordt de beschikbare schijfruimte voor het maken van gelaagde volumes verminderd. En andersom: als een gelaagd volume wordt gemaakt, wordt de beschikbare ruimte voor het maken van lokaal vastgemaakte volumes verminderd. 

        Als u een lokaal vastgemaakt volume van 8 TB (maximaal toegestane grootte) op uw 8100-apparaat inricht, hebt u de lokale ruimte die beschikbaar is op het apparaat volledig gebruikt. Vanaf dat moment kunt u geen gelaagd volume maken, omdat er geen lokale ruimte op het apparaat is voor het hosten van de werkset van het gelaagde volume. Bestaande gelaagde volumes zijn ook van invloed op de beschikbare ruimte. Als u bijvoorbeeld een 8100-apparaat hebt met reeds gelaagde volumes van 100 TB, is er nog maar 4 TB ruimte beschikbaar voor lokaal vastgemaakte volumes.

        De volgende afbeelding toont de **Basisinstellingen** in het dialoogvenster voor een lokaal vastgemaakt volume.

         ![Lokaal volume toevoegen](./media/storsimple-create-volume-u2/add-local-volume-include.png)

        De volgende afbeelding toont de **basisinstellingen** in het dialoogvenster voor een gelaagd volume.

         ![Lokaal volume toevoegen](./media/storsimple-create-volume-u2/add-tiered-volume-include.png)

   4. Klik op het pijlpictogram ![pijltje](./media/storsimple-create-volume-u2/HCS_ArrowIcon-include.png) om naar de volgende pagina te gaan.


3. Voeg in het dialoogvenster **Extra instellingen** een nieuwe record voor toegangscontrole (ACR) in:

    1. Geef een **naam** voor uw ACR op.
    2. Geef onder **iSCSI-initiatornaam** het IQN (iSCI Qualified Name) van uw Windows-host op. Als u het IQN niet hebt, gaat u naar [Het IQN van een Windows Server-host ophalen](#get-the-iqn-of-a-windows-server-host).
    3. Onder **Standaardback-up voor dit volume?** selecteert u het selectievakje **Inschakelen**. Met de standaardback-up wordt een beleid ingesteld waarmee elke dag om 22.30 uur (tijd op het apparaat) een back-up wordt gemaakt en een momentopname van dit volume in de cloud wordt geplaatst.
     
     > [AZURE.NOTE] Nadat de back-up hier is ingeschakeld, kan dit niet ongedaan worden gemaakt. Als u deze instelling wilt wijzigen, moet u het volume bewerken.

     ![Volume toevoegen](./media/storsimple-create-volume-u2/AddVolumeAdditionalSettings1.png)

4. Klik op het vinkje ![vinkje](./media/storsimple-create-volume-u2/HCS_CheckIcon-include.png). Er wordt een volume gemaakt met de opgegeven instellingen.





<!--HONumber=Jun16_HO2-->



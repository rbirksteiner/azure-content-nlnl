<!--author=SharS last changed: 02/04/2016-->

#### Een volume maken

1. Klik op het apparaat op de pagina **Quick Start** en klik vervolgens op **Een volume toevoegen**. Hiermee start u de wizard Volume toevoegen.

2. Voer in de wizard onder **Basisinstellingen** de volgende stappen uit:
   1. Geef een **naam** op voor het volume.
   2. Geef de **ingerichte capaciteit** voor het volume in GB of TB op. De volumecapaciteit moet voor een fysiek apparaat tussen 1 GB en 64 TB zijn.
   3. Selecteer in de vervolgkeuzelijst het **gebruikstype** voor het volume. 
   4. Als u dit volume gebruikt voor de archivering van gegevens, schakelt u het selectievakje **Dit volume gebruiken voor minder frequent gebruikte gearchiveerde gegevens** in. In alle andere gevallen schakelt u **Gelaagd volume** in. (Gelaagde volumes werden voorheen primaire volumes genoemd.)

        ![Volume toevoegen](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)

    4. Klik op het pijlpictogram ![pijltje](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) om naar de volgende pagina te gaan.

3. Voeg in het dialoogvenster **Extra instellingen** een nieuwe record voor toegangscontrole (ACR) in:
   1. Geef een **naam** voor uw ACR op.
   2. Geef onder **iSCSI-initiatornaam** het IQN (iSCI Qualified Name) van uw Windows-host op. Als u het IQN niet hebt, gaat u naar [Het IQN van een Windows Server-host ophalen](#get-the-iqn-of-a-windows-server-host).
   3. Het is verstandig standaard een back-up te maken door het selectievakje **Een standaardback-up voor dit volume in schakelen** in te schakelen. Met de standaardback-up wordt een beleid ingesteld waarmee elke dag om 22.30 uur (tijd op het apparaat) een back-up wordt gemaakt en een momentopname van dit volume in de cloud wordt geplaatst.

        > [AZURE.NOTE] Nadat de back-up hier is ingeschakeld, kan dit niet ongedaan worden gemaakt. Als u deze instelling wilt wijzigen, moet u het volume bewerken.

        ![Volume toevoegen](./media/storsimple-create-volume/AddVolume2-include.png)

4. Klik op het vinkje ![vinkje](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Er wordt een volume gemaakt met de opgegeven instellingen.

![Video beschikbaar](./media/storsimple-create-volume/Video_icon.png) **Video beschikbaar**

Als u een video wilt bekijken over het maken van een StorSimple-volume, klikt u [hier](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/).




<!--HONumber=Jun16_HO2-->



<!--author=SharS last changed: 9/17/15-->

#### Een volume koppelen, initialiseren en formatteren

1. Start de Microsoft iSCSI-initiator.

2. Klik in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Detectie** op **Portal detecteren**.

3. Geef in het dialoogvenster **Doelportaal detecteren** het IP-adres op van de netwerkinterface met iSCSI-functionaliteit en klik vervolgens op **OK**. 

4. Ga in het venster **Eigenschappen iSCSI-initiator** op het tabblad **Doelen** naar **Gedetecteerde doelen**. De status van het apparaat moet worden weergegeven als **Inactief**.

5. Selecteer het doelapparaat en klik vervolgens op **Verbinden**. Nadat het apparaat is verbonden, moet de status zijn gewijzigd in **Verbonden**. (Zie [Microsoft iSCSI-initiator installeren en configureren][1] voor meer informatie over het gebruik van de Microsoft iSCSI-initiator).

6. Druk op uw Windows-hots op de Windows-toets + X en klik vervolgens op **Uitvoeren**. 

7. Typ in het dialoogvenster **Uitvoeren** **Diskmgmt.msc**. Klik op **OK**. Het dialoogvenster **Schijfbeheer** wordt weergegeven. In het rechterdeelvenster worden de volumes op uw host weergeven.

8. In het venster **Schijfbeheer** worden de gekoppelde volumes weergegeven zoals in de volgende afbeelding. Klik met de rechtermuisknop op het gedetecteerde volume (klik op de naam van de schijf) en klik vervolgens op **Online**.

     ![Opmaakvolume initialiseren](./media/storsimple-mount-initialize-format-volume/HCS_InitializeFormatVolume-include.png) 

9. Klik met de rechtermuisknop op het volume (klik op de naam van de schijf) en klik vervolgens op **Initialiseren**.

10. Om een eenvoudig volume te formatteren, moet u de volgende stappen uitvoeren:
  1. Selecteer het volume, klik er met de rechtermuisknop op (klik op het juiste gebied) en klik vervolgens op **Nieuw eenvoudig volume**.
  2. Geef in de wizard Nieuw eenvoudig volume de volumegrootte en de stationsletter op en configureer het volume als een NTFS-bestandssysteem.
  3. Geef een clustergrootte van 64 kB op. Deze clustergrootte werkt goed samen met de ontdubbelingsalgoritmen die worden gebruikt in de StorSimple-oplossing.
  4. Voer een snelle formattering uit.

![Video beschikbaar](./media/storsimple-mount-initialize-format-volume/Video_icon.png) **Video beschikbaar**

Als u een video wilt bekijken over het koppelen, initialiseren en formatteren van een StorSimple-oplossing, klikt u [hier](https://azure.microsoft.com/documentation/videos/mount-initialize-and-format-a-storsimple-volume/).

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



<!--HONumber=Jun16_HO2-->



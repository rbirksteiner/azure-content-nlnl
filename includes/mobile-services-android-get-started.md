De laatste fase van deze zelfstudie is het bouwen en uitvoeren van uw nieuwe app.

### Project in Android Studio laden en Gradle synchroniseren

1. Blader naar de locatie waar u de gecomprimeerde projectbestanden hebt opgeslagen en vouw de bestanden op uw computer uit in de map met Android Studio-projecten.

2. Open Android Studio. Als u aan een project werkt en dit wordt weergegeven, sluit u het project (Bestand => Project sluiten).

3. Selecteer **Een bestaand Android Studio-project openen**, blader naar de projectlocatie en klik vervolgens op **OK**. Het project wordt nu geladen en met Gradle gesynchroniseerd.

    ![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. Wacht tot de synchronisatie met Gradle is voltooid. Als de fout 'Kan doel niet vinden' wordt weergegeven, komt dat omdat de versie die in Android Studio wordt gebruikt niet overeenkomt met die in het voorbeeld. De eenvoudigste manier om dit probleem op te lossen is door op de koppeling **Ontbrekende platform(s) installeren en project synchroniseren** in het foutbericht te klikken. U ziet mogelijk nog meer foutberichten over versies. Herhaal dan eenvoudig deze procedure totdat er geen foutberichten meer worden weergegeven.
    - Er is een andere manier om dit op te lossen als u de 'nieuwste en beste' versie van Android wilt gebruiken. U kunt **targetSdkVersion** in het bestand *build.gradle* in de map *app* bijwerken zodat deze overeenkomt met de versie die al op uw computer is geïnstalleerd. Welke versie dat is, kunt u zien als u op het pictogram **SDK Manager** klikt. Vervolgens drukt u op **Project met Gradle-bestanden synchroniseren**. Mogelijk wordt er een foutbericht weergegeven dat betrekking heeft op versies van opbouwhulpmiddelen. U kunt dit op dezelfde manier oplossen.

### De app uitvoeren

U kunt de app uitvoeren met behulp van de emulator of met een daadwerkelijk apparaat.

1. Als u de app op een apparaat wilt uitvoeren, moet u het apparaat via een USB-kabel met uw computer verbinden. U moet [het apparaat voorbereiden voor ontwikkelwerkzaamheden](https://developer.android.com/training/basics/firstapp/running-app.html). Als u op een Windows-computer ontwikkelt, moet u ook een USB-stuurprogramma downloaden en installeren.

2. Als u de app wilt uitvoeren met de Android-emulator, moet u minimaal één Android Virtual Device (AVD) definiëren. Klik op het pictogram AVD Manager om deze apparaten te maken en te beheren.

3. Klik in het menu **Uitvoeren** op **Uitvoeren** om het project te starten. En kies een apparaat of emulator in het dialoogvenster dat verschijnt.

4. Als de app wordt weergeven, typt u zinvolle tekst, zoals _Voltooi de zelfstudie_, en klikt u vervolgens op **Toevoegen**.

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

    Er wordt nu een POST-aanvraag verzonden naar de nieuwe mobiele service die wordt gehost in Azure. De gegevens van de aanvraag worden opgenomen in de takentabel. De items die in de tabel zijn opgeslagen, worden geretourneerd door de mobiele service en de gegevens worden weergegeven in de lijst.

    > [AZURE.NOTE] U kunt de code bekijken die toegang heeft tot uw mobiele service, om zo gegevens op te vragen en in te voegen. Deze code vindt u in het bestand ToDoActivity.java.

8. Klik in de klassieke Azure-portal op het tabblad **Gegevens** en klik vervolgens op de tabel **Taken**.

    ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

    U kunt nu door de gegevens bladeren die door de app in de tabel zijn ingevoegd.

    ![](./media/mobile-services-android-get-started/mobile-data-browse.png)



<!--HONumber=Jun16_HO2-->



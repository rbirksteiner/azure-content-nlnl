

##Het bestand met de aanvraag voor certificaatondertekening genereren

De Apple Push Notification Service (APNS) gebruikt certificaten om uw pushmeldingen te verifiëren. Volg deze instructies om het pushcertificaat te maken dat vereist is om meldingen te verzenden en te ontvangen. Zie de officiële documentatie van de [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) voor meer informatie over deze concepten.

Genereer het bestand met de aanvraag voor certificaatondertekening dat door Apple wordt gebruikt om een ondertekend pushcertificaat te genereren.

1. Voer op uw Mac het hulpprogramma Sleutelhangertoegang uit. Dit kan worden gestart vanuit de map **Hulpprogramma's** of de map **Overige** in Launchpad.

2. Klik op **Toegang tot sleutelhanger**, vouw **Certificaatassistent** uit, klik vervolgens op **Een certificaat bij een certificaatautoriteit aanvragen...**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Selecteer uw **gebruikers-e-mailadres** en **algemene naam**, controleer of **Opgeslagen op schijf** is geselecteerd en klik vervolgens op **Doorgaan**. Laat het veld **E-mailadres CA** leeg omdat dit niet is vereist.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Typ een naam voor het bestand met de aanvraag voor certificaatondertekening in **Opslaan als**, selecteer de locatie in **Waar**, klik vervolgens op **Opslaan**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Het bestand met de aanvraag voor certificaatondertekening wordt opgeslagen op de geselecteerde locatie; de standaardlocatie is op het Bureaublad. Onthoud de locatie die u voor dit bestand hebt gekozen.

Vervolgens gaat u uw app registreren bij Apple, pushmeldingen inschakelen en dit geëxporteerde bestand met de aanvraag voor certificaatondertekening uploaden om een pushcertificaat te maken.

##Uw app voor pushmeldingen registreren

U moet u app bij Apple registreren en u voor pushmeldingen registeren om pushmeldingen te kunnen verzenden naar een iOS-app.  

1. Als u uw app nog niet hebt geregistreerd, gaat u naar de <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS-inrichtingsportal</a>, meldt u zich aan bij het Apple Developer Center met uw Apple ID, klikt u op **Id's**, klikt u vervolgens op **App-id's** en ten slotte op het **+**-teken om een nieuwe app te registreren.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. Werk de volgende drie velden voor uw nieuwe app bij en klik vervolgens op **Doorgaan**:

    * **Naam**: typ een beschrijvende naam voor uw app in het veld **Naam** in de sectie **Beschrijving app-id**.
    
    * **Bundel-id**: onder de sectie **Expliciete app-id** typt u een **bundel-id** in het formulier `<Organization Identifier>.<Product Name>` zoals vermeld in de [App Distribution Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8) (Gids voor apps-distributie). De *organisatie-id* en *productnaam* die u gebruikt, moet overeenkomen met de organisatie-id en productnaam die u gebruikt als u een XCode-project gaat maken. In de onderstaande schermopname wordt *NotificationHubs* gebruikt als de organisatie-id en *GetStarted* als de productnaam. Door ervoor te zorgen dat deze overeenkomen met waarden die u gaat gebruiken in uw XCode-project, is het mogelijk om het juiste publicatieprofiel met XCode te gebruiken. 
    
    * ** Pushmeldingen**: selecteer de optie **Pushmeldingen** in de sectie **App Services**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

    Nu wordt uw app-id gegenereerd en wordt u gevraagd de gegevens te bevestigen. Klik op **Verzenden**


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


    Nadat u op **Verzenden** hebt gedrukt, verschijnt het scherm **Registratie is voltooid** zoals hieronder wordt weergegeven. Klik op **Gereed**.


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. Ga in het Developer Center onder de app-id’s naar de app-id die u zojuist hebt gemaakt en klik op de rij van die id.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Door op de app-id te klikken, worden de gegevens van de app weergegeven. Klik op de knop **Bewerken** onderaan.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Ga naar de onderkant van het scherm en klik op de knop **Certificaat maken** onder de sectie **Ontwikkeling push-SSL-certificaat**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Nu wordt de assistent IOS-certificaat toevoegen weergegeven.

    > [AZURE.NOTE] In deze zelfstudie wordt een ontwikkelingscertificaat gebruikt. Hetzelfde proces wordt gebruikt bij het registreren van een productiecertificaat. Zorg er wel voor dat u hetzelfde certificaattype gebruikt als u meldingen verzendt.

5. Klik op **Bestand kiezen**, blader naar de locatie waar u het bestand met de aanvraag voor certificaatondertekening hebt opgeslagen dat u in de eerste taak hebt gemaakt, en klik vervolgens op **Genereren**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Nadat het certificaat met de portal is gemaakt, klikt u op de knop **Downloaden** en klikt u vervolgens op **Gereed**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Het certificaat wordt nu gedownload en op uw computer in de map Downloads opgeslagen.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] Standaard krijgt het gedownloade bestand, een ontwikkelingscertificaat, de naam **aps_development.cer**.

7. Dubbelklik op het gedownloade pushcertificaat **aps_development.cer**.

    Nu wordt het nieuwe certificaat in de sleutelhanger geïnstalleerd, zoals hieronder wordt weergegeven:

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] De naam in uw certificaat kan afwijken, maar wordt voorafgegaan door **Apple Development iOS Push Services:**.

8. In Sleutelhangertoegang klikt u met de rechtermuisknop op het nieuwe pushcertificaat dat u hebt gemaakt in de categorie **Certificaten**. Klik op **Exporteer**, geef het bestand een naam, selecteer de **.p12**-indeling en klik vervolgens op **Bewaar**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Noteer de bestandsnaam en locatie van het geëxporteerde .p12-certificaat. Dit wordt gebruikt om verificatie met APNS mogelijk te maken.

    >[AZURE.NOTE] Tijdens deze zelfstudie wordt een QuickStart.p12-bestand gemaakt. De naam en locatie van uw bestand kunnen afwijken.


##Een inrichtingsprofiel voor de app maken

1. Terug in de <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS-inrichtingsportal</a> selecteert u **Inrichtingsprofielen**, selecteert u **Alle** en klikt u vervolgens op de knop met het **+**-teken om een nieuw profiel te maken. Nu wordt de wizard **iOS-inrichtingsprofielen toevoegen** gestart.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Selecteer **Ontwikkeling iOS-app** onder **Ontwikkeling** als het inrichtingsprofieltype en klik vervolgens op **Doorgaan**. 


3. Selecteer vervolgens in de vervolgkeuzelijst **App-id** de app-id die u zojuist hebt gemaakt en klik op **Doorgaan**

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. In het scherm **Certificaten selecteren** selecteert u het ontwikkelingscertificaat dat u doorgaans gebruikt voor het ondertekenen van programmacode en klikt u op **Doorgaan**. Dit is niet het pushcertificaat dat u zojuist hebt gemaakt.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. Selecteer vervolgens de **apparaten** die u voor de tests wilt gebruiken en klik op **Doorgaan**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Kies tot slot een naam voor het profiel in **Profielnaam** en klik op **Genereren**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


7. Als het nieuwe inrichtingsprofiel is gemaakt, klikt u erop om het te downloaden en op uw machine met de Xcode-ontwikkelcode te installeren. Klik vervolgens op **Gereed**.

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)





<!--HONumber=Jun16_HO2-->



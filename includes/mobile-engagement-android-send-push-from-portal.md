###Mobile Engagement toegang verlenen tot uw GCM-API-sleutel

Om Mobile Engagement namens u pushmeldingen te laten verzenden, moet u het toegang geven tot uw API-sleutel. Dit kunt u doen door uw sleutel te configureren en in te voeren in de Mobile Engagement-portal.

1. Controleer in de klassieke Azure-portal of u zich in de app bevindt die we voor dit project gebruiken en klik vervolgens op de knop **Engage** onderaan:

    ![](./media/mobile-engagement-android-send-push/engage-button.png)

2. Klik vervolgens in de sectie **Instellingen** -> **Native pushbericht** om uw GCM-sleutel in te voeren:

    ![](./media/mobile-engagement-android-send-push/engagement-portal.png)

3. Klik op het pictogram **Bewerken** vóór de **API-sleutel** in de sectie **GCM-instellingen** zoals hieronder weergegeven:

    ![](./media/mobile-engagement-android-send-push/native-push-settings.png)

4. Plak de GCM-serversleutel die u eerder hebt verkregen in het pop-upvenster en klik vervolgens op **OK**.

    ![](./media/mobile-engagement-android-send-push/api-key.png)

##<a id="send"></a>Een melding verzenden naar uw app

We gaan nu een eenvoudige pushmeldingcampagne maken waarbij een pushmelding wordt verzonden naar uw app.

1. Navigeer naar het tabblad **REACH** in uw Mobile Engagement-portal.

2. Klik op **Nieuwe aankondiging** om uw pushmeldingcampagne te maken.

    ![](./media/mobile-engagement-android-send-push/new-announcement.png)

3. Stel het eerste veld van uw campagne in via de volgende stappen:

    ![](./media/mobile-engagement-android-send-push/campaign-first-params.png)

    a. Geef uw campagne een naam.

    b. Selecteer het **Bezorgingstype** als *Systeemmelding -> Eenvoudig*: dit is het eenvoudige Android-pushmeldingentype met een titel en een korte tekstregel.

    c. Selecteer **Leveringstijd** als *Elk tijdstip* om de app toe te staan een melding te ontvangen ongeacht of de app is gestart of niet.

    d. Als teksttype van de melding typt u de **Titel** die in de pushmelding vet wordt weergegeven.

    e. Typ vervolgens uw **bericht**

4. Blader naar beneden en selecteer in de sectie **Inhoud** **Alleen melding**.

    ![](./media/mobile-engagement-android-send-push/campaign-content.png)

5. U hebt nu een campagne gemaakt die zo eenvoudig mogelijk is. Blader nogmaals naar beneden en klik op de knop **Maken** om uw campagne op te slaan.

6. Laatste stap: klik op **Activeren** om uw campagne om pushmeldingen te verzenden, te activeren.

    ![](./media/mobile-engagement-android-send-push/campaign-activate.png)


<!--HONumber=Jun16_HO2-->



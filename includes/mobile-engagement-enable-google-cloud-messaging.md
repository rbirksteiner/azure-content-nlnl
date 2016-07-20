
###Met API-sleutel een Google Cloud Messaging-project maken

>[AZURE.NOTE] Voor deze procedure moet u een Google-account hebben met een bijbehorend e-mailadres dat is geverifieerd. Als u een nieuw Google-account wilt maken, gaat u naar <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navigeer naar [Google Cloud Console](https://console.developers.google.com/project) en meld u aan met de referenties voor uw Google-account.

2. Klik op **Ga naar project** en klik vervolgens op **Project maken**.
<!--
    ![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

    ![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. Typ een naam van het project.

4. Noteer het projectnummer dat wordt weergegeven onder het tekstvak **Projectnaam**. U hebt dit verderop in de zelfstudie nodig om het in het manifestbestand van Android in te vullen.
    ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)   
5. Klik op **Maken**.

6. Controleer of in de linkerkolom **Overzicht** is geselecteerd en klik onder mobiele API's op **Google Cloud Messaging**. Klik op de volgende pagina op **Inschakelen**.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. Klik op de volgende pagina op **Ga naar referenties** en selecteer op de volgende pagina **Google Cloud Messaging** in de eerste vervolgkeuzelijst en **Webserver** in de tweede, klik vervolgens op **Welke referenties heb ik nodig?**

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. Op de pagina **Referenties aan uw project toevoegen** klikt u op **API-sleutel maken**.

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. Noteer de waarde van de **API-SLEUTEL**. Deze API-sleutelwaarde gaat u later gebruiken voor de configuratie in de sectie Native pushbericht. Klik nu op **Gereed**.



<!--HONumber=Jun16_HO2-->



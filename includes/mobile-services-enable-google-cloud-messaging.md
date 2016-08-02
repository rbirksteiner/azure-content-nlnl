
1. Navigeer naar [Google Cloud Console](https://console.developers.google.com/project) en meld u aan met de referenties voor uw Google-account. 
 
2. Klik op **Project maken**, typ een naam voor het project en klik vervolgens op **Maken**. Als dat wordt gevraagd, voert u de sms-verificatie uit en klikt u nogmaals op **Maken**.

    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   

     Typ uw nieuwe **projectnaam** en klik op **Project maken**.

3. Klik op de knop **Hulpprogramma's en meer** en klik vervolgens op **Projectgegevens**. Noteer het **projectnummer**. U moet deze waarde in de client-app als de `SenderId`-variabele instellen.

    ![](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)


4. In het projectdashboard onder **Mobiele API's** klikt u op **Google Cloud Messaging**, klikt u vervolgens op de volgende pagina op **API inschakelen** en accepteert u de servicevoorwaarden. 

    ![](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)

    ![](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 

5. Klik in het projectdashboard op**Referenties** > **Referentie maken** > **API-sleutel**. 

    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. In **Een nieuwe sleutel maken** klikt u op **Serversleutel**, typt u een naam voor de sleutel en klikt u vervolgens op **Maken**.

7. Noteer de waarde van de **API-SLEUTEL**.

    Deze API-sleutelwaarde wordt door Azure gebruikt om zich bij GCM te verifiëren en om namens uw app pushmeldingen te verzenden.




<!--HONumber=Jun16_HO2-->



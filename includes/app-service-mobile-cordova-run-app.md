
1. Ga naar [Azure Portal]. Klik op **Door alles bladeren** > **Mobile Apps** > de back-end die u zojuist hebt gemaakt. Klik in de instellingen van de mobiele app op **Snelstartgids** > **Cordova**. Selecteer onder **Uw clienttoepassing configureren** de optie **Een nieuwe app maken** en klik vervolgens op **Downloaden**. Er wordt dan een volledig Cordova-project voor een app gedownload, dat vooraf is geconfigureerd om verbinding te maken met uw back-end.

2. Pak het gedownloade zip-bestand uit in een map op de vaste schijf.

3. Open het project met **Visual Studio**.  Klik op **Openen** > **Project/oplossing...**.

4. Zoek naar het bestand _sitename_.sln en klik op **Openen**.

5. De standaardemulator is **Ripple - Nexus (Galaxy)**.  Klik op de pijl-omlaag naast de emulator en selecteer **Google Android-emulator**.

6. Klik op **Google Android-emulator**.  Het project wordt gemaakt en vervolgens uitgevoerd.  Mogelijk ziet u nu een beveiligingswaarschuwing van het netwerk omdat de Google Android-emulator toegang tot het netwerk aanvraagt.  Uiteindelijk wordt de Google Android-emulator weergegeven en wordt uw toepassing uitgevoerd.

7. Typ zinvolle tekst in de app, zoals _De zelfstudie voltooien_ en klik vervolgens op de knop Toevoegen. Er wordt nu een POST-aanvraag verzonden naar de back-end van Azure die u eerder hebt geïmplementeerd. De back-end voegt gegevens van de aanvraag toe aan de SQL-takentabel en stuurt informatie over de nieuw opgeslagen items terug naar de mobiele app. Deze gegevens worden in de lijst in de mobiele app weergegeven.

    ![](./media/app-service-mobile-cordova-quickstart/quickstart-startup.png)

[Azure Portal]: https://portal.azure.com/



<!--HONumber=Jun16_HO2-->



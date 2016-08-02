
Met het mobiele-serviceproject dat u hebt gedownload, kunt u uw nieuwe mobiele service gewoon op uw lokale computer of virtuele machine uitvoeren. Hierdoor kunt u gemakkelijk fouten in uw servicecode opsporen voordat u deze naar Azure publiceert.

In deze sectie gaat u uw nieuwe app testen in combinatie met de mobiele service die lokaal wordt uitgevoerd.

1. Blader naar de locatie waar u de gecomprimeerde projectbestanden hebt opgeslagen, vouw de bestanden op uw computer uit en open het oplossingsbestand in Visual Studio.

2. Klik in Solution Explorer in Visual Studio met de rechtermuisknop op **Instellen als opstartproject** en druk vervolgens op **F5** om het project te bouwen en de mobiele service lokaal te starten.

    ![](./media/mobile-services-dotnet-backend-test-local-service-dotnet/mobile-service-startup.png)

    Een webpagina wordt weergegeven nadat de mobiele service kon worden gestart.

3. Als u de Store-app wilt testen, klikt u met de rechtermuisknop op uw client-app-project, klikt u op **Instellen als opstartproject** en drukt u vervolgens op **F5** om het project opnieuw te bouwen en de app te starten.

    Hiermee start u de app, die vervolgens verbinding maakt met het lokale mobiele-service-exemplaar.   

4. Typ zinvolle tekst in de app, zoals _Voltooi de zelfstudie_ in het tekstvak **Nieuwe taak invoegen** en klik op **Opslaan**.

    Er wordt nu een POST-aanvraag verzonden naar de lokale mobiele service. De gegevens van de aanvraag worden opgenomen in de takentabel. De items die in de tabel zijn opgeslagen, worden door de mobiele service geretourneerd. Vervolgens worden de gegevens weergegeven in de tweede kolom in de app.


<!--HONumber=Jun16_HO2-->



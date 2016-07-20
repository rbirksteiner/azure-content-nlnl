## Een IoT Hub maken

Een IoT Hub maken waarmee uw gesimuleerde apparaat verbinding kan maken. De volgende stappen laten zien hoe u deze taak kunt uitvoeren met behulp van de Azure Portal.

1. Meld u aan bij [Azure Portal][lnk portal].

2. Klik in de snelbalk op **Nieuw** > **Internet van dingen** > **Azure IoT Hub**.

    ![Snelbalk Azure Portal][1]

3. In de blade **IoT Hub** kiest u de configuratie voor uw IoT Hub.

    ![Blade IoT Hub][2]

    * In het vak **Naam** typt u een naam voor uw IoT Hub. Als de **naam** geldig en beschikbaar is, verschijnt er een groen vinkje in het vak **Naam**.
    * Selecteer een [laag voor de prijzen en de schaal][lnk-pricing]. Voor deze zelfstudie is geen bepaalde laag vereist. Gebruik voor deze zelfstudie de gratis F1-laag.
    * Geef in **Resourcegroep** een nieuwe resourcegroep op of selecteer een bestaande. Zie [Resourcegroepen gebruiken om Azure-resources te beheren][lnk-resource-groups] voor meer informatie.
    * In **Locatie** selecteert u de locatie voor het hosten van uw IoT Hub. Voor deze zelfstudie kiest uw dichtstbijzijnde locatie.

4. Wanneer u de configuratieopties voor uw IoT Hub hebt gekozen, klikt u op **Maken**.  Het kan enkele minuten duren voordat Azure uw IoT Hub heeft gemaakt. Als u de status wilt zien, kunt u de voortgang bekijken via het startboard in het meldingenvenster.

    ![Status Nieuwe IoT Hub][3]

5. Wanneer de IoT Hub is gemaakt, klikt u op de nieuwe tegel voor uw IoT Hub in de portal. De blade voor de nieuwe IoT Hub wordt dan geopend. Noteer de **hostnaam** en klik vervolgens op het pictogram **Sleutels**.

    ![Blade Nieuwe IoT Hub][4]

6. In de blade **Gedeeld toegangsbeleid** klikt u op het beleid **iothubowner** en kopieert en noteert u de verbindingsreeks in de blade **iothubowner**. Zie voor meer informatie [Access control][lnk-access-control] (Toegangsbeheer Ink-toegangsbeheer) in de 'Azure IoT Hub developer guide' (Ontwikkelaarshandleiding Azure IoT Hub).

    ![Blade Gedeeld toegangsbeleid][5]


<!-- Images. -->
[1]: ./media/iot-hub-get-started-create-hub/create-iot-hub1.png
[2]: ./media/iot-hub-get-started-create-hub/create-iot-hub2.png
[3]: ./media/iot-hub-get-started-create-hub/create-iot-hub3.png
[4]: ./media/iot-hub-get-started-create-hub/create-iot-hub4.png
[5]: ./media/iot-hub-get-started-create-hub/create-iot-hub5.png

<!-- Links -->
[lnk-resource-groups]: ../articles/azure-portal/resource-group-portal.md
[lnk portal]: https://portal.azure.com/
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-access-control]: ../articles/iot-hub/iot-hub-devguide.md#accesscontrol



<!--HONumber=Jun16_HO2-->



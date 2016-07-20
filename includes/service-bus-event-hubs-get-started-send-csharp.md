## Berichten verzenden naar Event Hubs

In deze sectie schrijft u een Windows-consoletoepassing die gebeurtenissen naar uw Event Hub verzendt.

1. Maak in Visual Studio een nieuw Visual C# bureaublad-app-project met behulp van de projectsjabloon**Consoletoepassing**. Noem het project **Afzender**.

    ![][7]

2. Klik in Solution Explorer met de rechtermuisknop op de oplossing en kies **NuGet-pakketten beheren voor oplossing**. 

3. Klik op het tabblad **Bladeren** en zoek naar `Microsoft Azure Service Bus`. Controleer of de projectnaam (**Afzender**) is opgegeven in het vak **Versie(s)**. Klik op **Installeren** en accepteer de gebruiksvoorwaarden. 

    ![][8]

    Hiermee wordt een verwijzing naar het <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/"> NuGet-pakket Azure Service Bus-bibliotheek</a> gedownload, geïnstalleerd en toegevoegd.

4. Voeg aan het begin van het bestand **Program.cs** de volgende `using`-instructies toe:

    ```
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Voeg de volgende velden toe aan de klasse **Program**, waarbij u de waarden van de tijdelijke aanduiding vervangt door de naam van de Event Hub die u in de vorige sectie hebt gemaakt, en de verbindingsreeks op naamruimteniveau die u eerder hebt opgeslagen.

    ```
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```

6. Voeg de volgende methode toe aan de klasse **Program**:

    ```
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }

            Thread.Sleep(200);
        }
    }
    ```

    Deze methode verzendt continu gebeurtenissen naar uw Event Hub, met een interval van 200 ms.

7. Voeg tot slot de volgende regels toe aan de methode **Main**:

    ```
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png


<!--HONumber=Jun16_HO2-->



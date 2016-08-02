<properties 
    pageTitle="Service Bus Brokered Messaging .NET-zelfstudie | Microsoft Azure"
    description="Brokered Messaging .NET-zelfstudie"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/25/2016"
    ms.author="sethm" />

# Service Bus Brokered Messaging .NET-zelfstudie

Azure Service Bus biedt twee uitgebreide oplossingen voor berichtverzending. De eerste via een gecentraliseerde ‘relayservice’, die wordt uitgevoerd in de cloud en tal van verschillende transportprotocollen en standaarden voor webservices ondersteunt, met inbegrip van SOAP, WS-* en REST. De client heeft geen rechtstreekse verbinding met de on-premises service nodig en hoeft niet te weten waar de service zich bevindt. Voor de on-premises service is niet vereist dat poorten voor inkomend verkeer zijn geopend in de firewall.

De tweede oplossing voor berichtverzending geeft ‘brokered messaging’-mogelijkheden. Deze kunnen worden beschouwd als functies voor asynchrone of ontkoppelde berichtverzending, die ondersteuning bieden voor publiceren-abonneren, tijdelijke ontkoppeling en scenario's voor taakverdeling met behulp van de Service Bus-infrastructuur voor berichtverzending. Ontkoppelde communicatie heeft veel voordelen; clients en servers kunnen bijvoorbeeld naar behoefte verbinding maken en hun bewerkingen asynchroon uitvoeren.

Deze zelfstudie is bedoeld om u een overzicht van en praktische ervaring met wachtrijen te geven, een van de belangrijkste onderdelen van Service Bus Brokered Messaging. Wanneer u de onderwerpen in deze zelfstudie hebt doorlopen, hebt u een toepassing die een lijst met berichten vult, een wachtrij maakt en berichten naar die wachtrij verzendt. Tot slot ontvangt de toepassing de berichten uit de wachtrij en geeft deze weer, waarna hij de resources opschoont en zichzelf afsluit. Zie de [Zelfstudie over Service Bus Relayed Messaging](service-bus-relay-tutorial.md) voor een bijbehorende zelfstudie waarin wordt beschreven hoe u een toepassing bouwt die de Service Bus-mogelijkheden voor ‘Relayed Messaging’ gebruikt.

## Inleiding en vereisten

Wachtrijen maken gebruik van het FIFO-principe (first in, first out) voor de berichtbezorging naar een of meer concurrerende consumenten. FIFO wil zeggen dat doorgaans wordt verwacht dat berichten door de ontvangers worden ontvangen en verwerkt in de tijdsvolgorde waarin ze aan de wachtrij zijn toegevoegd, en dat elk bericht slechts door één berichtenconsument ontvangen en verwerkt wordt. Een belangrijk voordeel van het gebruik van wachtrijen is dat *tijdelijke ontkoppeling* van toepassingsonderdelen kan worden bereikt: met andere woorden, de producenten en consumenten hoeven de berichten niet op hetzelfde moment te verzenden en te ontvangen, omdat berichten blijvend worden opgeslagen in de wachtrij. Een bijkomend voordeel is *load leveling*, waardoor producenten en consumenten berichten met verschillende snelheden kunnen verzenden en ontvangen.

Hieronder volgen enkele administratieve en vereiste stappen die u moet uitvoeren voordat u met de zelfstudie begint. De eerste stap is het maken van een servicenaamruimte en het ophalen van een SAS-sleutel (Shared Access Signature). Een naamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via Service Bus. Een SAS-sleutel wordt automatisch door het systeem gegenereerd wanneer een servicenaamruimte wordt gemaakt. De combinatie van servicenaamruimte en SAS-sleutel biedt Service Bus een referentie voor het verifiëren van toegang tot een toepassing.

### Een servicenaamruimte maken en een SAS-sleutel ophalen

1. Voor het maken van een servicenaamruimte gaat u naar de [klassieke Azure-portal][]. Klik op **Service Bus** aan de linkerkant en klik vervolgens op **Maken**. Voer een naam voor de naamruimte in en klik vervolgens op het vinkje.

1. Klik in het hoofdvenster van de portal op de naam van de naamruimte die u in de vorige stap hebt gemaakt.

1. Klik op **Configureren**

1. Noteer de primaire sleutel voor het beleid **RootManagerSharedAccessKey** of kopieer deze naar het klembord. U gebruikt deze waarde verderop in deze zelfstudie.

De volgende stap is het maken van een Visual Studio-project en het schrijven van twee hulpfuncties die een door komma's gescheiden lijst met berichten in een sterk getypeerd [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) .NET [lijst](https://msdn.microsoft.com/library/6sh2ey19.aspx)-object laden.

### Een Visual Studio-project maken

1. Open Visual Studio als beheerder door met de rechtermuisknop op het programma in het menu Start te klikken en vervolgens op **Als administrator uitvoeren**.

1. Maak een nieuw consoletoepassingsproject aan. Klik op het menu **Bestand**, selecteer **Nieuw** en selecteer vervolgens **Project**. Klik in het dialoogvenster **Nieuw project** op **Visual C#** (kijk bij **Andere talen** als **Visual C#** niet wordt weergegeven), klik op het sjabloon **Consoletoepassing** en geef het de naam **QueueSample**. Gebruik de standaardwaarde voor **Locatie**. Klik op **OK** om het project te maken.

1. Gebruik de NuGet package manager om de Service Bus-bibliotheken aan het project toe te voegen:
    1. Klik in Solution Explorer met de rechtermuisknop op het project **QueueSample** en klik vervolgens op **NuGet-pakketten beheren**.
    2. Klik in het dialoogvenster **NuGet-pakketten beheren** op het tabblad **Bladeren**, zoek naar **Azure Service Bus** en klik vervolgens op **Installeren**.
<br />
1. Dubbelklik in Solution Explorer op het bestand Program.cs om dit te openen in de Visual Studio-editor. Verander de standaardnaam `QueueSample` van de naamruimte in `Microsoft.ServiceBus.Samples`.

    ```
    Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Wijzig de `using`-instructies zoals weergegeven in de volgende code.

    ```
    using System;
    using System.Collections.Generic;
    using System.Data;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;
    ```

1. Maak een tekstbestand met de naam Data.csv en kopieer de volgende door komma's gescheiden tekst naar het bestand.

    ```
    IssueID,IssueTitle,CustomerID,CategoryID,SupportPackage,Priority,Severity,Resolved
    1,Package lost,1,1,Basic,5,1,FALSE
    2,Package damaged,1,1,Basic,5,1,FALSE
    3,Product defective,1,2,Premium,5,2,FALSE
    4,Product damaged,2,2,Premium,5,2,FALSE
    5,Package lost,2,2,Basic,5,2,TRUE
    6,Package lost,3,2,Basic,5,2,FALSE
    7,Package damaged,3,7,Premium,5,3,FALSE
    8,Product defective,3,2,Premium,5,3,FALSE
    9,Product damaged,4,6,Premium,5,3,TRUE
    10,Package lost,4,8,Basic,5,3,FALSE
    11,Package damaged,5,4,Basic,5,4,FALSE
    12,Product defective,5,4,Basic,5,4,FALSE
    13,Package lost,6,8,Basic,5,4,FALSE
    14,Package damaged,6,7,Premium,5,5,FALSE
    15,Product defective,6,2,Premium,5,5,FALSE
    ```

    Sla het Data.csv-bestand op en sluit het. Onthoud de locatie waar u het bestand hebt opgeslagen.

1. Klik met de rechtermuisknop op de naam van uw project in Solution Explorer (in dit voorbeeld **QueueSample**), klik op **Toevoegen** en vervolgens op **Bestaand item**.

1. Blader naar het Data.csv-bestand dat u hebt gemaakt in stap 6. Klik op het bestand en klik vervolgens op **Toevoegen**. Zorg ervoor dat **Alle bestanden (*.*)** in de lijst met bestandstypen is geselecteerd.

### Een methode maken die een lijst met berichten parseert

1. Declareer twee variabelen in de `Program`-klasse voor de `Main()`-methode. De een van het type **DataTable**, die de lijst met berichten in Data.csv bevat. De ander van het type lijstobject, sterk getypeerd naar [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx). Deze laatste is de lijst met brokered berichten die in de volgende stappen van de zelfstudie wordt gebruikt.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList;
    ```

1. Definieer buiten `Main()` een `ParseCSV()`-methode die de lijst met berichten in Data.csv parseert en de berichten in een [DataTable](https://msdn.microsoft.com/library/azure/system.data.datatable.aspx)-tabel laadt, zoals hier wordt weergegeven. De methode retourneert een **DataTable**-object.

    ```
    static DataTable ParseCSVFile()
    {
        DataTable tableIssues = new DataTable("Issues");
        string path = @"..\..\data.csv";
        try
        {
            using (StreamReader readFile = new StreamReader(path))
            {
                string line;
                string[] row;
    
                // create the columns
                line = readFile.ReadLine();
                foreach (string columnTitle in line.Split(','))
                {
                    tableIssues.Columns.Add(columnTitle);
                }
    
                while ((line = readFile.ReadLine()) != null)
                {
                    row = line.Split(',');
                    tableIssues.Rows.Add(row);
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Error:" + e.ToString());
        }
    
        return tableIssues;
    }
    ```

1. Voeg in de `Main()` -methode een instructie toe die de `ParseCSVFile()`-methode aanroept:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
    
    }
    ```

### Een methode maken die de lijst met berichten laadt

1. Definieer buiten `Main()` een `GenerateMessages()`-methode die het door `ParseCSVFile()` geretourneerde **DataTable**-object in een sterk getypeerde tabel met brokered berichten laadt. De methode retourneert vervolgens het **Lijst**-object, zoals in het volgende voorbeeld. 

    ```
    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
        // Instantiate the brokered list object
        List<BrokeredMessage> result = new List<BrokeredMessage>();
    
        // Iterate through the table and create a brokered message for each row
        foreach (DataRow item in issues.Rows)
        {
            BrokeredMessage message = new BrokeredMessage();
            foreach (DataColumn property in issues.Columns)
            {
                message.Properties.Add(property.ColumnName, item[property]);
            }
            result.Add(message);
        }
        return result;
    }
    ```

1. Voeg in `Main()`, direct na de aanroep van `ParseCSVFile()`, een instructie toe die de `GenerateMessages()`-methode aanroept met de geretourneerde waarde van `ParseCSVFile()` als argument:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
    }
    ```

### Gebruikersreferenties verkrijgen

1. Maak eerst drie globale tekenreeksvariabelen die deze waarden bevatten. Declareer deze variabelen direct na de vorige declaraties voor variabelen, bijvoorbeeld:

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        public class Program
        {
    
            private static DataTable issues;
            private static List<BrokeredMessage> MessageList; 

            // Add these variables
            private static string ServiceNamespace;
            private static string sasKeyName = "RootManageSharedAccessKey";
            private static string sasKeyValue;
            …
    ```

1. Maak vervolgens een functie die de servicenaamruimte en de SAS-sleutel accepteert en opslaat. Voeg deze methode buiten `Main()` toe. Bijvoorbeeld: 

    ```
    static void CollectUserInput()
    {
        // User service namespace
        Console.Write("Please enter the namespace to use: ");
        ServiceNamespace = Console.ReadLine();
    
        // Issuer key
        Console.Write("Enter the SAS key to use: ");
        sasKeyValue = Console.ReadLine();
    }
    ```

1. Voeg in `Main()`, direct na het aanroepen van `GenerateMessages()`, een instructie toe die de `CollectUserInput()`-methode aanroept:

    ```
    public static void Main(string[] args)
    {
    
        // Populate test data
        issues = ParseCSVFile();
        MessageList = GenerateMessages(issues);
        
        // Collect user input
        CollectUserInput();
    }
    ```

### De oplossing bouwen

Klik in het menu **Ontwikkelen** in Visual Studio op **Oplossing ontwikkelen** of druk op **Ctrl + Shift + B** om de juistheid van uw werk tot nu toe te controleren.

## Beheerreferenties maken

In deze stap definieert u de beheerbewerkingen die u gebruikt om SAS-referenties (Shared Access Signature) te maken waarmee uw toepassing wordt toegestaan.

1. Ter verduidelijking, deze zelfstudie plaatst alle bewerkingen van de wachtrij in een afzonderlijke methode. Maak na de `Main()`-methode een asynchrone `Queue()`-methode in de `Program`-klasse. Bijvoorbeeld:
 
    ```
    public static void Main(string[] args)
    {
    …
    }
    static async Task Queue()
    {
    }
    ```

1. De volgende stap is het maken van een SAS-referentie met een [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx)-object. De methode voor het maken neemt de naam en waarde van de SAS-sleutel die u in de `CollectUserInput()`-methode hebt verkregen. Voeg de volgende code aan de `Queue()` methode toe:

    ```
    static async Task Queue()
    {
        // Create management credentials
        TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
    }
    ```

2. Maak een nieuw beheerobject voor de naamruimte met als argumenten een URI met de naam van de naamruimte en de beheerreferenties die u in de vorige stap hebt verkregen. Voeg deze code toe direct na de code die u in de vorige stap hebt toegevoegd. Vervang `<yourNamespace>` door de naam van de servicenaamruimte:
    
    ```
    NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

### Voorbeeld

Uw code zou er nu als volgt uit moeten zien:

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
  public class Program
  {
    private static DataTable issues;
    private static List<BrokeredMessage> MessageList;
    private static string ServiceNamespace;
    private static string sasKeyName = "RootManageSharedAccessKey";
    private static string sasKeyValue;

    static void Main(string[] args)
    {
      // Populate test data
      issues = ParseCSVFile();
      MessageList = GenerateMessages(issues);

      // Collect user input
      CollectUserInput();

      // Add this call
      Task.WaitAll(Queue());
    }

    static async Task Queue()
    {
      // Create management credentials
      TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
      NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    }

    static DataTable ParseCSVFile()
    {
      DataTable tableIssues = new DataTable("Issues");
      string path = @"..\..\data.csv";
      try
      {
        using (StreamReader readFile = new StreamReader(path))
        {
          string line;
          string[] row;

          // create the columns
          line = readFile.ReadLine();
          foreach (string columnTitle in line.Split(','))
          {
            tableIssues.Columns.Add(columnTitle);
          }

          while ((line = readFile.ReadLine()) != null)
          {
            row = line.Split(',');
            tableIssues.Rows.Add(row);
          }
        }
      }
      catch (Exception e)
      {
        Console.WriteLine("Error:" + e.ToString());
      }

      return tableIssues;
    }

    static List<BrokeredMessage> GenerateMessages(DataTable issues)
    {
      // Instantiate the brokered list object
      List<BrokeredMessage> result = new List<BrokeredMessage>();

      // Iterate through the table and create a brokered message for each row
      foreach (DataRow item in issues.Rows)
      {
        BrokeredMessage message = new BrokeredMessage();
        foreach (DataColumn property in issues.Columns)
        {
          message.Properties.Add(property.ColumnName, item[property]);
        }
        result.Add(message);
      }
      return result;
    }

    static void CollectUserInput()
    {
      // User service namespace
      Console.Write("Please enter the service namespace to use: ");
      ServiceNamespace = Console.ReadLine();

      // Issuer key
      Console.Write("Please enter the issuer key to use: ");
      sasKeyValue = Console.ReadLine();
    }
  }
}
```

## Berichten naar de wachtrij verzenden

In deze stap maakt u een wachtrij en verzendt u vervolgens de berichten in de lijst met brokered berichten naar die wachtrij.

### Een wachtrij maken en berichten naar de wachtrij verzenden

1. Maak eerst de wachtrij aan. Noem deze bijvoorbeeld `myQueue` en declareer hem direct na de beheerbewerkingen die u in de vorige stap in de `Queue()`-methode hebt toegevoegd:

    ```
    QueueDescription myQueue;

    if (namespaceClient.QueueExists("IssueTrackingQueue"))
    {
        namespaceClient.DeleteQueue("IssueTrackingQueue");
    }

    myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
    ```

1. Maak in de `Queue()`-methode een MessagingFactory-object met een nieuw aangemaakte Service Bus-URI als argument. Voeg de volgende code toe direct na de beheerbewerkingen die u in de vorige stap hebt toegevoegd. Vervang `<yourNamespace>` door de naam van de servicenaamruimte:

    ```
    MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);
    ```

1. Maak vervolgens het wachtrij-object met behulp van de klasse [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx). Voeg de volgende code toe direct na de code die u in de vorige stap hebt toegevoegd:

    ```
    QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");
    ```

1. Voeg vervolgens de code toe die door de lijst met brokered berichten gaat die u eerder hebt gemaakt, en die elk bericht naar de wachtrij verzendt. Voeg de volgende code toe direct na de `CreateQueueClient()`-instructie uit de vorige stap:
    
    ```
    // Send messages
    Console.WriteLine("Now sending messages to the queue.");
    for (int count = 0; count < 6; count++)
    {
        var issue = MessageList[count];
        issue.Label = issue.Properties["IssueTitle"].ToString();
        await myQueueClient.SendAsync(issue);
        Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
    }
    ```

## Berichten ontvangen uit de wachtrij

In deze stap verkrijgt u de lijst met berichten uit de wachtrij die u in de vorige stap hebt gemaakt.

### Een ontvanger maken en berichten uit de wachtrij ontvangen

Ga in de `Queue()`-methode weer door de wachtrij en ontvang de berichten met behulp van de methode [QueueClient.ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130423.aspx), waarbij elk bericht naar de console wordt afgedrukt. Voeg de volgende code toe direct na de code die u in de vorige stap hebt toegevoegd:

```
Console.WriteLine("Now receiving messages from Queue.");
BrokeredMessage message;
while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();
    
        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

Let op: de `Thread.Sleep` wordt alleen gebruikt om de berichtverwerking te simuleren en u hebt deze in een echte berichverzendingstoepassing waarschijnlijk niet nodig.

### De wachtrijmethode beëindigen en resources opschonen

Voeg direct na de vorige code de volgende code toe om de MessageFactory en wachtrijresources op te schonen:

```
factory.Close();
myQueueClient.Close();
namespaceClient.DeleteQueue("IssueTrackingQueue");
```

### De wachtrijmethode aanroepen

De laatste stap is het toevoegen van een instructie die de `Queue()`-methode vanuit `Main()` aanroept. Voeg de volgende gemarkeerde coderegel toe aan het einde van Main():
    
```
public static void Main(string[] args)
{
    // Collect user input
    CollectUserInput();
    
    // Populate test data
    issues = ParseCSVFile();
    MessageList = GenerateMessages(issues);
    
    // Add this call
    Queue();
}
```

### Voorbeeld

De volgende code bevat de volledige **QueueSample**-toepassing.

```
using System;
using System.Collections.Generic;
using System.Data;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.ServiceBus.Samples
{
    public class Program
    {
        private static DataTable issues;
        private static List<BrokeredMessage> MessageList;

        // Add these variables
        private static string ServiceNamespace;
        private static string sasKeyName = "RootManageSharedAccessKey";
        private static string sasKeyValue;

        static void Main(string[] args)
        {

            // Populate test data
            issues = ParseCSVFile();
            MessageList = GenerateMessages(issues);

            // Collect user input
            CollectUserInput();

            Queue();

        }

        static async Task Queue()
        {
            // Create management credentials
            TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName, sasKeyValue);
            NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueDescription myQueue;

            if (namespaceClient.QueueExists("IssueTrackingQueue"))
            {
                namespaceClient.DeleteQueue("IssueTrackingQueue");
            }
            
            myQueue = namespaceClient.CreateQueue("IssueTrackingQueue");
            
            MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", "<yourNamespace>", string.Empty), credentials);

            QueueClient myQueueClient = factory.CreateQueueClient("IssueTrackingQueue");

            // Send messages
            Console.WriteLine("Now sending messages to the queue.");
            for (int count = 0; count < 6; count++)
            {
                var issue = MessageList[count];
                issue.Label = issue.Properties["IssueTitle"].ToString();
                await myQueueClient.SendAsync(issue);
                Console.WriteLine(string.Format("Message sent: {0}, {1}", issue.Label, issue.MessageId));
            }

            Console.WriteLine("Now receiving messages from Queue.");
            BrokeredMessage message;
            while ((message = await myQueueClient.ReceiveAsync(new TimeSpan(hours: 0, minutes: 1, seconds: 5))) != null)
            {
                Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
                message.Complete();

                Console.WriteLine("Processing message (sleeping...)");
                Thread.Sleep(1000);
            }

            factory.Close();
            myQueueClient.Close();
            namespaceClient.DeleteQueue("IssueTrackingQueue");


        }

        static void CollectUserInput()
        {
            // User service namespace
            Console.Write("Please enter the namespace to use: ");
            ServiceNamespace = Console.ReadLine();

            // Issuer key
            Console.Write("Enter the SAS key to use: ");
            sasKeyValue = Console.ReadLine();
        }

        static List<BrokeredMessage> GenerateMessages(DataTable issues)
        {
            // Instantiate the brokered list object
            List<BrokeredMessage> result = new List<BrokeredMessage>();

            // Iterate through the table and create a brokered message for each row
            foreach (DataRow item in issues.Rows)
            {
                BrokeredMessage message = new BrokeredMessage();
                foreach (DataColumn property in issues.Columns)
                {
                    message.Properties.Add(property.ColumnName, item[property]);
                }
                result.Add(message);
            }
            return result;
        }

        static DataTable ParseCSVFile()
        {
            DataTable tableIssues = new DataTable("Issues");
            string path = @"..\..\data.csv";
            try
            {
                using (StreamReader readFile = new StreamReader(path))
                {
                    string line;
                    string[] row;

                    // create the columns
                    line = readFile.ReadLine();
                    foreach (string columnTitle in line.Split(','))
                    {
                        tableIssues.Columns.Add(columnTitle);
                    }

                    while ((line = readFile.ReadLine()) != null)
                    {
                        row = line.Split(',');
                        tableIssues.Rows.Add(row);
                    }
                }
            }
            catch (Exception e)
            {
                Console.WriteLine("Error:" + e.ToString());
            }

            return tableIssues;
        }
    }
}
```

## De  QueueSample-toepassing bouwen en uitvoeren

Nu u de voorgaande stappen hebt voltooid, kunt u de **QueueSample**-toepassing bouwen en uitvoeren.

### De  QueueSample-toepassing bouwen

Klik in het menu **Ontwikkelen** in Visual Studio op **Oplossing ontwikkelen** of druk op **Ctrl + Shift + B**. Als er fouten optreden, controleer dan of uw code klopt met het complete voorbeeld dat aan het einde van de vorige stap wordt weergegeven.

## Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Service Bus-clienttoepassing en -service maakt met behulp van de mogelijkheden voor Service Bus Brokered Messaging. Zie [Zelfstudie over Service Bus Relayed Messaging](service-bus-relay-tutorial.md) voor een vergelijkbare zelfstudie waarin gebruik wordt gemaakt van Service Bus [Relayed Messaging](service-bus-messaging-overview.md#Relayed-messaging).

Zie de volgende onderwerpen voor meer informatie over [Service Bus](https://azure.microsoft.com/services/service-bus/).

- [Overzicht van Service Bus-berichten](service-bus-messaging-overview.md)
- [Grondbeginselen van Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus-architectuur](service-bus-architecture.md)

[klassieke Azure-portal]: http://manage.windowsazure.com



<!--HONumber=Jun16_HO2-->



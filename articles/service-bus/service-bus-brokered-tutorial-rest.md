<properties 
    pageTitle="Service Bus Brokered Messaging REST-zelfstudie | Microsoft Azure"
    description="Brokered Messaging REST-zelfstudie"
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
    ms.date="06/03/2016"
    ms.author="sethm" />

# Service Bus Brokered Messaging REST-zelfstudie

In deze zelfstudie kunt u leren hoe u een eenvoudige op REST gebaseerde Azure Service Bus-wachtrij en onderwerp/abonnement maakt.

## Stap 1: Een naamruimte maken

De eerste stap is het maken van een servicenaamruimte en het ophalen van een SAS-sleutel ( [Shared Access Signature](service-bus-sas-overview.md)). Een servicenaamruimte biedt een toepassingsbegrenzing voor elke toepassing die toegankelijk is via Service Bus. Een SAS-sleutel wordt automatisch door het systeem gegenereerd wanneer een servicenaamruimte wordt gemaakt. De combinatie van servicenaamruimte en SAS-sleutel biedt Service Bus een referentie voor het verifiëren van toegang tot een toepassing.

### Een naamruimte maken en een SAS-sleutel ophalen

1. Voor het maken van een servicenaamruimte gaat u naar de [klassieke Azure-portal][]. Klik op **Service Bus** aan de linkerkant en klik vervolgens op **Maken**. Voer een naam voor de naamruimte in en klik vervolgens op het vinkje.

1. Klik in het hoofdvenster van de [klassieke Azure-portal][] op de naam van de naamruimte die u in de vorige stap hebt gemaakt.

1. Klik op het tabblad **Configureren**.

1. Noteer in het gedeelte **sleutel genereren voor gedeelde toegang** de **Primaire sleutel** die is gekoppeld aan het beleid **RootManageSharedAccessKey**, of kopieer deze sleutel naar het klembord. U gebruikt deze waarde verderop in deze zelfstudie.

## Een consoleclient maken

Met Service Bus-wachtrijen kunt u berichten in een ‘first-in, first-out’-wachtrij opslaan. Onderwerpen en abonnementen implementeren een patroon van publiceren/abonneren: u maakt een onderwerp aan en vervolgens een of meer abonnementen die aan dit onderwerp gekoppeld zijn. Wanneer berichten naar het onderwerp worden verzonden, worden ze onmiddellijk naar de abonnees van dit onderwerp verzonden.

De code in deze zelfstudie doet het volgende:

- Hij gebruikt uw servicenaamruimte en SAS-sleutel ([Shared Access Signature](service-bus-sas-overview.md)) om toegang te krijgen tot uw resources voor de Service Bus-naamruimte.

- Hij maakt een wachtrij, verzendt een bericht naar de wachtrij en leest het bericht uit de wachtrij.

- Hij maakt een onderwerp, een abonnement op dat onderwerp en verzendt en leest het bericht van het abonnement.

- Hij haalt alle gegevens over de wachtrij, het onderwerp en het abonnement uit de Service Bus op, met inbegrip van abonnementsregels.

- Hij verwijdert de resources van de wachtrij, het onderwerp en het abonnement.

Omdat de service een REST-stijl-webservice is, zijn er geen speciale typen betrokken, want de gehele uitwisseling bestaat uit tekenreeksen. Dit betekent dat het Visual Studio-project niet naar Service Bus-bibliotheken mag verwijzen.

Nadat u in de eerste stap de servicenaamruimte en referenties heeft verkregen, maakt u vervolgens een eenvoudige Visual Studio-consoletoepassing aan.

### Een consoletoepassing maken

1. Start Visual Studio als beheerder door met de rechtermuisknop op het programma in het menu **Start** te klikken en vervolgens op **Als administrator uitvoeren**.

1. Maak een nieuw consoletoepassingsproject aan. Klik in het menu **Bestand** op **Nieuw** en klik vervolgens op **Project**. Klik in het dialoogvenster **Nieuw Project** op **Visual C#** (kijk bij **Andere talen** als **Visual C#** niet wordt weergegeven), selecteer het sjabloon **Consoletoepassing** en geef het de naam **Microsoft.ServiceBus.Samples**. Gebruik de standaardlocatie. Klik op **OK** om het project te maken.

1. Zorg ervoor dat uw `using`-instructies in Program.cs er als volgt uitzien:

    ```
    using System;
    using System.Globalization;
    using System.IO;
    using System.Net;
    using System.Security.Cryptography;
    using System.Text;
    using System.Xml;
    ```

1. Wijzig indien nodig de standaard Visual Studio-naam van de naamruimte voor het programma in `Microsoft.ServiceBus.Samples`.

1. Voeg in de `Program`-klasse de volgende globale variabelen toe:
    
    ```
    static string serviceNamespace;
    static string baseAddress;
    static string token;
    const string sbHostName = "servicebus.windows.net";
    ```

1. Plak de volgende code in `Main()`:

    ```
    Console.Write("Enter your service namespace: ");
    serviceNamespace = Console.ReadLine();
    
    Console.Write("Enter your SAS key: ");
    string SASKey = Console.ReadLine();
    
    baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
    try
    {
        token = GetSASToken("RootManageSharedAccessKey", SASKey);
    
        string queueName = "Queue" + Guid.NewGuid().ToString();
    
        // Create and put a message in the queue
        CreateQueue(queueName, token);
        SendMessage(queueName, "msg1");
        string msg = ReceiveAndDeleteMessage(queueName);
    
        string topicName = "Topic" + Guid.NewGuid().ToString();
        string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
        CreateTopic(topicName);
        CreateSubscription(topicName, subscriptionName);
        SendMessage(topicName, "msg2");
    
        Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));
    
        // Get an Atom feed with all the queues in the namespace
        Console.WriteLine(GetResources("$Resources/Queues"));
    
        // Get an Atom feed with all the topics in the namespace
        Console.WriteLine(GetResources("$Resources/Topics"));
    
        // Get an Atom feed with all the subscriptions for the topic we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions"));
    
        // Get an Atom feed with all the rules for the topic and subscription we just created
        Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));
    
        // Delete the queue we created
        DeleteResource(queueName);
    
        // Delete the topic we created
        DeleteResource(topicName);
    
        // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Topics"));
    
        // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
        Console.WriteLine(GetResources("$Resources/Queues"));
    }
    catch (WebException we)
    {
        using (HttpWebResponse response = we.Response as HttpWebResponse)
        {
            if (response != null)
            {
                Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
            }
            else
            {
                Console.WriteLine(we.ToString());
            }
        }
    }
    
    Console.WriteLine("\nPress ENTER to exit.");
    Console.ReadLine();
    ```

## Beheerreferenties maken

De volgende stap is het schrijven van een methode die de naamruimte en SAS-sleutel verwerkt die u in de vorige stap heeft opgegeven, en die een SAS-token retourneert. In dit voorbeeld wordt een SAS-token aangemaakt dat een uur geldig is.

### Een GetSASToken()-methode maken

Plak de volgende code in de `Program`-klasse na de `Main()`-methode:

```
private static string GetSASToken(string SASKeyName, string SASKeyValue)
{
  TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
  string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
  string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
  HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

  string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
  string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
      WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
  return sasToken;
}
```
## De wachtrij maken

De volgende stap is het schrijven van een methode die een wachtrij maakt met de REST-stijl-opdracht HTTP PUT.

Plak de volgende code direct na de `GetSASToken()`-code die u in de vorige stap hebt toegevoegd:

```
// Uses HTTP PUT to create the queue
private static string CreateQueue(string queueName, string token)
{
    // Create the URI of the new queue, note that this uses the HTTPS scheme
    string queueAddress = baseAddress + queueName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating queue {0}", queueAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                          <title type=""text"">" + queueName + @"</title>
                          <content type=""application/xml"">
                            <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                          </content>
                        </entry>";

    byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## Een bericht naar de wachtrij verzenden

In deze stap voegt u een methode toe die met de REST-stijl-opdracht HTTP POST een bericht verzendt naar de wachtrij die u in de vorige stap hebt gemaakt.

1. Plak de volgende code direct na de `CreateQueue()`-code die u in de vorige stap hebt toegevoegd:

    ```
    // Sends a message to the "queueName" queue, given the name and the value to enqueue
    // Uses an HTTP POST request.
    private static void SendMessage(string queueName, string body)
    {
        string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
        Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
        WebClient webClient = new WebClient();
        webClient.Headers[HttpRequestHeader.Authorization] = token;
    
        webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));
    }
    ```

1. Standaard brokered berichten worden in een `BrokerProperties`-HTTP-header gezet. De eigenschappen van de broker moeten worden geserialiseerd in JSON-indeling. Om een **TimeToLive**-waarde van 30 seconden op te geven en een berichtlabel ‘M1’ aan het bericht toe te voegen, voegt u de volgende code toe direct voor de `webClient.UploadData()`-aanroep die in het vorige voorbeeld is weergegeven:

    ```
    // Add brokered message properties "TimeToLive" and "Label"
    webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
    ```

    Houd er rekening mee dat brokered-berichteigenschappen zijn en worden toegevoegd. De verzendaanvraag moet daarom een API-versie opgeven die ondersteuning biedt voor alle brokered-berichteigenschappen die deel uitmaken van de aanvraag. Als de opgegeven API-versie een brokered-berichteigenschap niet ondersteunt, wordt die eigenschap genegeerd.

1. Aangepaste berichteigenschappen worden gedefinieerd als een set van sleutel-/waardeparen. Elke aangepaste eigenschap wordt in een eigen TPPT-header opgeslagen. Om de aangepaste eigenschappen ‘Prioriteit’ en ‘Klant’ toe te voegen, voegt u de volgende code toe direct voor de `webClient.UploadData()`-aanroep die in het vorige voorbeeld is weergegeven:

    ```
    // Add custom properties "Priority" and "Customer".
    webClient.Headers.Add("Priority", "High");
    webClient.Headers.Add("Customer", "12345");
    ```

## Een bericht uit de wachtrij ontvangen en verwijderen

De volgende stap is het toevoegen van een methode die een bericht uit de wachtrij ontvangt en verwijdert met de REST-stijl-opdracht HTTP DELETE.

Plak de volgende code direct na de `SendMessage()`-code die u in de vorige stap hebt toegevoegd:

```
// Receives and deletes the next message from the given resource (queue, topic, or subscription)
// using the resourceName and an HTTP DELETE request
private static string ReceiveAndDeleteMessage(string resourceName)
{
    string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
    Console.WriteLine("\nRetrieving message from {0}", fullAddress);
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
    string responseStr = Encoding.UTF8.GetString(response);

    Console.WriteLine(responseStr);
    return responseStr;
}
```

## Een onderwerp en een abonnement maken

De volgende stap is het schrijven van een methode die een onderwerp maakt met de REST-stijl-opdracht HTTP PUT, waarna u een methode kunt schrijven die een abonnement op dat onderwerp maakt.

### Een onderwerp maken

Plak de volgende code direct na de `ReceiveAndDeleteMessage()`-code die u in de vorige stap hebt toegevoegd:

```
// Using an HTTP PUT request.
private static string CreateTopic(string topicName)
{
    var topicAddress = baseAddress + topicName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating topic {0}", topicAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

### Een abonnement maken

De volgende code maakt een abonnement aan op het onderwerp dat u in de vorige stap hebt gemaakt. Voeg de volgende code direct na de `CreateTopic()`-definitie toe:

```
private static string CreateSubscription(string topicName, string subscriptionName)
{
    var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
    // Prepare the body of the create queue request
    var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

    byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
    return Encoding.UTF8.GetString(response);
}
```

## Berichtresources ophalen

In deze stap voegt u de code toe die de berichteigenschappen ophaalt en vervolgens de berichtverzendingsresources verwijdert die u in de vorige stappen hebt gemaakt.

### Een Atom-feed ophalen met de opgegeven resources

Voeg de volgende code toe direct na de `CreateSubscription()`-methode die u in de vorige stap hebt toegevoegd:

```
private static string GetResources(string resourceAddress)
{
    string fullAddress = baseAddress + resourceAddress;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;
    Console.WriteLine("\nGetting resources from {0}", fullAddress);
    return FormatXml(webClient.DownloadString(fullAddress));
}
```

### Berichtverzendingsentiteiten verwijderen

Voeg de volgende code toe direct na de code die u in de vorige stap hebt toegevoegd:

```
private static string DeleteResource(string resourceName)
{
    string fullAddress = baseAddress + resourceName;
    WebClient webClient = new WebClient();
    webClient.Headers[HttpRequestHeader.Authorization] = token;

    Console.WriteLine("\nDeleting resource at {0}", fullAddress);
    byte[] response = webClient.UploadData(fullAddress, "DELETE", newbyte[0]);
    return Encoding.UTF8.GetString(response);
}
```

### De Atom-feed indelen

De `GetResources()`-methode bevat een aanroep voor een `FormatXml()`-methode die de opgehaalde Atom-feed opnieuw indeelt, zodat deze beter leesbaar is. Het volgende is de definitie van `FormatXml()`. Voeg deze code toe direct na de `DeleteResource()`-code die u in de vorige stap hebt toegevoegd:

```
// Formats the XML string to be more human-readable; intended for display purposes
private static string FormatXml(string inputXml)
{
    XmlDocument document = new XmlDocument();
    document.Load(new StringReader(inputXml));

    StringBuilder builder = new StringBuilder();
    using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
    {
        writer.Formatting = Formatting.Indented;
        document.Save(writer);
    }

    return builder.ToString();
}
```

## De toepassing bouwen en uitvoeren.

U kunt de toepassing nu bouwen en uitvoeren. Klik in het menu **Opbouwen** in Visual Studio op **Oplossing bouwen** of druk op F6.

### De toepassing uitvoeren

Druk op F5 om de toepassing uit te voeren als er geen fouten zijn. Voer, wanneer u daarom wordt gevraagd, uw naamruimte, SAS-sleutelnaam en SAS-sleutelwaarde in die u in de eerste stap hebt verkregen.

### Voorbeeld

Het volgende voorbeeld is de volledige code zoals die eruit zou moet zien nadat u alle stappen in deze zelfstudie hebt gevolgd.

```
using System;
using System.Globalization;
using System.IO;
using System.Net;
using System.Security.Cryptography;
using System.Text;
using System.Xml;

namespace Microsoft.ServiceBus.Samples
{
    class Program
    {
        static string serviceNamespace;
        static string baseAddress;
        static string token;
        const string sbHostName = "servicebus.windows.net";

        static void Main(string[] args)
        {
            Console.Write("Enter your service namespace: ");
            serviceNamespace = Console.ReadLine();

            Console.Write("Enter your SAS key: ");
            string SASKey = Console.ReadLine();

            baseAddress = "https://" + serviceNamespace + "." + sbHostName + "/";
            try
            {
                token = GetSASToken("RootManageSharedAccessKey", SASKey);

                string queueName = "Queue" + Guid.NewGuid().ToString();

                // Create and put a message in the queue
                CreateQueue(queueName, token);
                SendMessage(queueName, "msg1");
                string msg = ReceiveAndDeleteMessage(queueName);

                string topicName = "Topic" + Guid.NewGuid().ToString();
                string subscriptionName = "Subscription" + Guid.NewGuid().ToString();
                CreateTopic(topicName);
                CreateSubscription(topicName, subscriptionName);
                SendMessage(topicName, "msg2");

                Console.WriteLine(ReceiveAndDeleteMessage(topicName + "/Subscriptions/" + subscriptionName));

                // Get an Atom feed with all the queues in the namespace
                Console.WriteLine(GetResources("$Resources/Queues"));

                // Get an Atom feed with all the topics in the namespace
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the subscriptions for the topic we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions"));

                // Get an Atom feed with all the rules for the topic and subscription we just created
                Console.WriteLine(GetResources(topicName + "/Subscriptions/" + subscriptionName + "/Rules"));

                // Delete the queue we created
                DeleteResource(queueName);

                // Delete the topic we created
                DeleteResource(topicName);

                // Get an Atom feed with all the topics in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Topics"));

                // Get an Atom feed with all the queues in the namespace, it shouldn't have the one we created now
                Console.WriteLine(GetResources("$Resources/Queues"));
            }
            catch (WebException we)
            {
                using (HttpWebResponse response = we.Response as HttpWebResponse)
                {
                    if (response != null)
                    {
                        Console.WriteLine(new StreamReader(response.GetResponseStream()).ReadToEnd());
                    }
                    else
                    {
                        Console.WriteLine(we.ToString());
                    }
                }
            }

            Console.WriteLine("\nPress ENTER to exit.");
            Console.ReadLine();
        }

        private static string GetSASToken(string SASKeyName, string SASKeyValue)
        {
            TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
            string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + 3600);
            string stringToSign = WebUtility.UrlEncode(baseAddress) + "\n" + expiry;
            HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(SASKeyValue));

            string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
            string sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}",
                WebUtility.UrlEncode(baseAddress), WebUtility.UrlEncode(signature), expiry, SASKeyName);
            return sasToken;
        }

        // Uses HTTP PUT to create the queue
        private static string CreateQueue(string queueName, string token)
        {
            // Create the URI of the new queue, note that this uses the HTTPS scheme
            string queueAddress = baseAddress + queueName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating queue {0}", queueAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + queueName + @"</title>
                                  <content type=""application/xml"">
                                    <QueueDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(queueAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        // Sends a message to the "queueName" queue, given the name and the value to enqueue
        // Uses an HTTP POST request.
        private static void SendMessage(string queueName, string body)
        {
            string fullAddress = baseAddress + queueName + "/messages" + "?timeout=60&api-version=2013-08 ";
            Console.WriteLine("\nSending message {0} - to address {1}", body, fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            // Add brokered message properties “TimeToLive” and “Label”.
            webClient.Headers.Add("BrokerProperties", "{ \"TimeToLive\":30, \"Label\":\"M1\"}");
            // Add custom properties “Priority” and “Customer”.
            webClient.Headers.Add("Priority", "High");
            webClient.Headers.Add("Customer", "12345");
            webClient.UploadData(fullAddress, "POST", Encoding.UTF8.GetBytes(body));

        }

        // Receives and deletes the next message from the given resource (queue, topic, or subscription)
        // using the resourceName and an HTTP DELETE request.
        private static string ReceiveAndDeleteMessage(string resourceName)
        {
            string fullAddress = baseAddress + resourceName + "/messages/head" + "?timeout=60";
            Console.WriteLine("\nRetrieving message from {0}", fullAddress);
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            string responseStr = Encoding.UTF8.GetString(response);

            Console.WriteLine(responseStr);
            return responseStr;
        }

        // Using an HTTP PUT request.
        private static string CreateTopic(string topicName)
        {
            var topicAddress = baseAddress + topicName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating topic {0}", topicAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + topicName + @"</title>
                                  <content type=""application/xml"">
                                    <TopicDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(topicAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string CreateSubscription(string topicName, string subscriptionName)
        {
            var subscriptionAddress = baseAddress + topicName + "/Subscriptions/" + subscriptionName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nCreating subscription {0}", subscriptionAddress);
            // Prepare the body of the create queue request
            var putData = @"<entry xmlns=""http://www.w3.org/2005/Atom"">
                                  <title type=""text"">" + subscriptionName + @"</title>
                                  <content type=""application/xml"">
                                    <SubscriptionDescription xmlns:i=""http://www.w3.org/2001/XMLSchema-instance"" xmlns=""http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"" />
                                  </content>
                                </entry>";

            byte[] response = webClient.UploadData(subscriptionAddress, "PUT", Encoding.UTF8.GetBytes(putData));
            return Encoding.UTF8.GetString(response);
        }

        private static string GetResources(string resourceAddress)
        {
            string fullAddress = baseAddress + resourceAddress;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;
            Console.WriteLine("\nGetting resources from {0}", fullAddress);
            return FormatXml(webClient.DownloadString(fullAddress));
        }

        private static string DeleteResource(string resourceName)
        {
            string fullAddress = baseAddress + resourceName;
            WebClient webClient = new WebClient();
            webClient.Headers[HttpRequestHeader.Authorization] = token;

            Console.WriteLine("\nDeleting resource at {0}", fullAddress);
            byte[] response = webClient.UploadData(fullAddress, "DELETE", new byte[0]);
            return Encoding.UTF8.GetString(response);
        }

        // Formats the XML string to be more human-readable; intended for display purposes
        private static string FormatXml(string inputXml)
        {
            XmlDocument document = new XmlDocument();
            document.Load(new StringReader(inputXml));

            StringBuilder builder = new StringBuilder();
            using (XmlTextWriter writer = new XmlTextWriter(new StringWriter(builder)))
            {
                writer.Formatting = Formatting.Indented;
                document.Save(writer);
            }

            return builder.ToString();
        }
    }
}
```

## Volgende stappen

Raadpleeg de volgende onderwerpen voor meer informatie:

- [Overzicht van Service Bus-berichten](service-bus-messaging-overview.md)
- [Grondbeginselen van Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Service Bus Relay REST-zelfstudie](service-bus-relay-rest-tutorial.md)

[klassieke Azure-portal]: http://manage.windowsazure.com



<!--HONumber=Jun16_HO2-->



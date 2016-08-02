<properties
   pageTitle="Containerbeheer in Azure Container Service via de webgebruikersinterface | Microsoft Azure"
   description="Implementeer containers naar een Azure Container Service-cluster met behulp van de webgebruikersinterface van Marathon."
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>

# Containerbeheer via de webgebruikersinterface

DC/OS biedt een omgeving voor het implementeren en schalen van geclusterde werkbelastingen terwijl de onderliggende hardware wordt onttrokken. Op de DC/OS ligt een framework dat de planning en uitvoering van rekenwerkbelastingen regelt.

Er zijn frameworks beschikbaar voor veel populaire werkbelastingen, maar in dit document wordt specifiek gekeken naar het maken en schalen van containerimplementaties met Marathon. Voer het uitvoeren van deze voorbeelden hebt u een DC/OS-cluster nodig dat is geconfigureerd in Azure Container Service. U hebt ook een externe verbinding met dit cluster nodig. Zie de volgende artikelen voor meer informatie over deze items:

- [Een Azure Container Service-cluster implementeren](container-service-deployment.md)
- [Verbinding maken met een Azure Container Service-cluster](container-service-connect.md)

## De DC/OS-gebruikersinterface verkennen

Wanneer een SSH-tunnel (Secure Shell) is ingesteld, gaat u naar http://localhost/. Hierdoor wordt de DC/OS-webgebruikersinterface geladen met informatie over het cluster, zoals gebruikte resources, actieve agents en actieve services.

![DC/OS-webgebruikersinterface](media/dcos/dcos2.png)

## De Marathon-gebruikersinterface verkennen

Ga naar http://localhost/Marathon om de Marathon-gebruikersinterface weer te geven. In dit scherm kunt u een nieuwe container of een andere toepassing starten op het DC/OS-cluster in Azure Container Service. U ziet ook informatie over actieve containers en toepassingen.  

![Marathon-gebruikersinterface](media/dcos/dcos3.png)

## Een met Docker ingedeelde container implementeren

Voor het implementeren van een nieuwe container via Marathon, klikt u op de knop **Toepassing maken** en voert u de volgende gegevens op het formulier in:

Veld           | Waarde
----------------|-----------
Id              | nginx
Installatiekopie           | nginx
Netwerk         | Overbrugd
Hostpoort       | 80
Protocol        | TCP

![Nieuwe toepassingsgebruikersinterface: algemeen](media/dcos/dcos4.png)

![Nieuwe toepassingsgebruikersinterface: Docker-container](media/dcos/dcos5.png)

![Nieuwe toepassingsgebruikersinterface: poort- en servicedetectie](media/dcos/dcos6.png)

Als u de containerpoort statisch wilt toewijzen aan een poort op de agent, gebruikt u de JSON-modus. U doet dit door de wizard Nieuwe toepassing over te schakelen naar de **JSON-modus** met behulp van de wisselknop. Voer dan het volgende in onder de sectie `portMappings` van de definitie van de toepassing. In dit voorbeeld wordt poort 80 van de container gebonden aan poort 80 van de DC/OS-agent. Nadat u deze wijziging hebt aangebracht, kunt u deze wizard uit JSON-modus halen.

```none
“hostPort”: 80,
```

![Nieuwe toepassingsgebruikersinterface: voorbeeld van poort 80](media/dcos/dcos13.png)

Het DC/OS-cluster wordt geïmplementeerd met een set persoonlijke en openbare agents. Om te zorgen dat het cluster toegang heeft tot toepassingen vanaf internet, moet u de toepassingen implementeren naar een openbare agent. Hiertoe selecteert u het tabblad **Optioneel** van de wizard Nieuwe toepassing en voert u **slave_public** in bij de **Geaccepteerde resourcerollen**.

![Nieuwe toepassingsgebruikersinterface: instellingen openbare agent](media/dcos/dcos14.png)

Op de hoofdpagina van Marathon ziet u de implementatiestatus voor de container.

![Hoofdpagina van Marathon: implementatiestatus van container](media/dcos/dcos7.png)

Als u terugschakelt naar de DC/OS-webgebruikersinterface (http://localhost/), ziet u dat een taak (in dit geval een container in Docker-indeling) wordt uitgevoerd op het DC/OS-cluster.

![DC/OS-webgebruikersinterface: taak die wordt uitgevoerd op het cluster](media/dcos/dcos8.png)

U ziet ook het clusterknooppunt waarop de taak wordt uitgevoerd.

![DC/OS-webgebruikersinterface: clusterknooppunt van taak](media/dcos/dcos9.png)

## Uw containers schalen

U kunt de Marathon-gebruikersinterface gebruiken om het aantal exemplaren van een container te schalen. Wanneer u dit wilt doen, gaat u naar de pagina **Marathon**, selecteert u de container die u wilt schalen en klikt u op de knop **Schalen**. In het dialoogvenster **Toepassing schalen** voert u het aantal gewenste containerexemplaren in en selecteert u **Toepassing schalen**.

![Marathon-gebruikersinterface: dialoogvenster Toepassing schalen](media/dcos/dcos10.png)

Nadat de bewerking voor het aanpassen van de schaal is voltooid, ziet u meerdere exemplaren van dezelfde taak verdeeld over DC/OS-agents.

![Dashboard in DC/OS-webgebruikersinterface: taak verspreid over agents](media/dcos/dcos11.png)

![DC/OS-webgebruikersinterface: knooppunten](media/dcos/dcos12.png)

## Volgende stappen

- [Werken met DC/OS en de Marathon API](container-service-mesos-marathon-rest.md)



<!--HONumber=Jun16_HO2-->



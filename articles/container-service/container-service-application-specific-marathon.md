<properties
   pageTitle="Toepassings- of gebruikersspecifieke Marathon-service | Microsoft Azure"
   description="Een toepassings- of gebruikersspecifieke Marathon-service maken"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Marathon, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# Een toepassings- of gebruikersspecifieke Marathon-service maken

Azure Container Service voorziet in een set masterservers waarop we Apache Mesos en Marathon vooraf configureren. Deze kunnen worden gebruikt voor het indelen van uw toepassingen op het cluster, maar het is raadzaam om niet de masters voor dit doel te gebruiken. Voor het aanpassen van de configuratie van Marathon moet bijvoorbeeld bij de masters zelf worden aangemeld en moeten wijzigingen worden aangebracht. Hierdoor worden gebruikers aangemoedigd unieke masterservers te maken die enigszins verschillen van de norm en die onafhankelijk moeten worden onderhouden en beheerd. Daarnaast is de configuratie die het ene team nodig heeft, mogelijk niet  de optimale configuratie voor een ander team. In dit artikel leggen we uit hoe u een gebruikers- of toepassingsspecifieke Marathon-service kunt toevoegen.

Aangezien deze service tot een enkele gebruiker of een enkel team behoort, kunnen gebruikers en teams de service helemaal naar eigen wens configureren. Bovendien zorgt Azure Container Service dat de service blijft draaien: mocht de service stoppen, dan start  Azure Container Service de service weer op. In de meeste gevallen zult u zelfs niets merken van eventuele uitval.

## Vereisten

[Implementeer een exemplaar van Azure Container Service](container-service-deployment.md) met orchestrator-type DCOS, [zorg dat de client verbinding kan maken met uw cluster](container-service-connect.md), en [AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)].

## Maak een toepassings- of gebruikersspecifieke Marathon-service.

Begin door een JSON-configuratiebestand te maken dat de naam bepaalt van de toepassingsservice die u wilt maken. Hier gebruiken we `marathon-alice` als de naam van het framework. Sla het bestand op naar iets als `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Gebruik vervolgens de CLI DC/OS om het Marathon-exemplaar te installeren met de opties die in uw configuratiebestand zijn ingesteld:

```bash
dcos package install --options=marathon-alice.json marathon
```

U ziet nu dat uw `marathon-alice`-service wordt uitgevoerd op het tabblad Services van uw DC/OS-gebruikersinterface. De gebruikersinterface is `http://<hostname>/service/marathon-alice/` als u rechtstreeks toegang wilt.

## De DC/OS CLI instellen voor toegang tot de service

Eventueel kunt u de DC/OS CLI configureren voor toegang tot deze nieuwe service door de eigenschap van `marathon.url` zo in te stellen dat deze als volgt verwijst naar het `marathon-alice`-exemplaar:

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

U kunt controleren met welk exemplaar van Marathon uw CLI werkt met de `dcos config show`-opdracht, en u kunt teruggaan naar het gebruik van de Marathon-masterservice met de opdracht `dcos config unset marathon.url`.


<!--HONumber=Jun16_HO2-->



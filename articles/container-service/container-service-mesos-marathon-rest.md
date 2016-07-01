<properties
   pageTitle="Containerbeheer in Azure Container Service met de REST API | Microsoft Azure"
   description="Implementeer containers naar een Mesos-cluster in Azure Container Service met behulp van de Marathon REST API."
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

# Containerbeheer met de REST API

DC/OS biedt een omgeving voor het implementeren en schalen van geclusterde workloads terwijl de onderliggende hardware wordt onttrokken. Op de DC/OS ligt een framework dat de planning en uitvoering van rekenworkloads regelt.

Er zijn frameworks beschikbaar voor veel populaire workloads, maar in dit document wordt specifiek gekeken naar het maken en schalen van containerimplementaties met Marathon. Voer het uitvoeren van deze voorbeelden hebt u een DC/OS-cluster nodig dat is geconfigureerd in Azure Container Service. U hebt ook een externe verbinding met dit cluster nodig. Zie de volgende artikelen voor meer informatie over deze items:

- [Een Azure Container Service-cluster implementeren](container-service-deployment.md)
- [Verbinding maken met een Azure Container Service-cluster](container-service-connect.md)

Nadat u met het Azure Container Service-cluster bent verbonden, hebt u via http://localhost:local-port toegang tot DC/OS en gerelateerde REST API's. In de voorbeelden in dit document wordt ervan uitgegaan dat u poort 80 gebruikt. Het Marathon-eindpunt kan bijvoorbeeld worden bereikt op `http://localhost/marathon/v2/`. Zie voor meer informatie over de verschillende API's de Mesosphere-documentatie voor de [Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html) en de [Chronos API](https://mesos.github.io/chronos/docs/api.html), en de Apache-documentatie voor de [Mesos Scheduler API](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## Informatie verzamelen van DC/OS en Marathon

Voordat u containers naar het DC/OS-cluster kun implementeren, verzamelt u wat informatie over het DC/OS-cluster, zoals de namen en de huidige status van de DC/OS-agents. U doet dit door een query uit te voeren op het `master/slaves`-eindpunt van de DC/OS REST API. Als alles goed gaat, ziet u een lijst van DC/OS-agents en de verschillende eigenschappen voor elke agent.

```bash
curl http://localhost/mesos/master/slaves
```

Gebruik nu het Marathon `/apps`-eindpunt om te controleren op huidige implementaties naar het DC/OS-cluster. Als dit een nieuw cluster is, ziet u een lege matrix voor apps.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## Een met Docker ingedeelde container implementeren

U implementeert met Docker ingedeelde containers via Marathon met behulp van een JSON-bestand waarin het doel van de implementatie wordt beschreven. In het volgende voorbeeld wordt de Nginx-container geïmplementeerd, met een binding van poort 80 van de DC/OS-agent naar poort 80 van de container. We willen u er ook op wijzen dat de eigenschap 'acceptedResourceRoles' is ingesteld op 'slave_public'. Hierdoor wordt de container geïmplementeerd naar een agent in de schaalset met openbare agents.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Wanneer u een met Docker ingedeelde agent wilt implementeren, maakt u uw eigen JSON-bestand of gebruikt u het voorbeeld dat is opgegeven bij de [Azure Container Service-demo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Sla dit op een toegankelijke locatie op. Voer vervolgens de volgende opdracht uit om de container te implementeren Geef de naam op van het JSON-bestand.

```
# deploy container

curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

De uitvoer lijkt op de volgende:

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

Als u in Marathon toepassingen opvraagt via een query, wordt deze nieuwe toepassing in de uitvoer weergegeven.

```
curl localhost/marathon/v2/apps
```

## Uw containers schalen

U kunt ook de Marathon API gebruiken om in implementaties van toepassingen uit of in te schalen. In het vorige voorbeeld hebt u één exemplaar van een toepassing geïmplementeerd. Laten we dit uitschalen naar drie exemplaren van een toepassing. Dit doet u door een JSON-bestand te maken met behulp van de volgende JSON-tekst en dit op een toegankelijke locatie op te slaan.

```json
{ "instances": 3 }
```

Voer de volgende opdracht uit om de toepassing uit te schalen.

>[AZURE.NOTE] De URI is http://localhost/marathon/v2/apps/ gevolgd door de id van de toepassing die u wilt schalen. Als u het Nginx-voorbeeld gebruikt dat hier wordt besproken, zou de URI http://localhost/marathon/v2/apps/nginx zijn.

```json
# scale container

curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Voer tenslotte een query voor toepassingen uit op het Marathon-eindpunt. U zult zien dat er nu drie Nginx-containers zijn.

```
curl localhost/marathon/v2/apps
```

## Gebruik PowerShell voor deze oefening: interactie van Marathon REST API met PowerShell

U kunt dezelfde acties uitvoeren met behulp van PowerShell-opdrachten in een Windows-systeem.

Voer de volgende opdracht uit om informatie te verzamelen over het DC/OS-cluster, zoals agentnamen en agentstatus.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

U implementeert met Docker ingedeelde containers via Marathon met behulp van een JSON-bestand waarin het doel van de implementatie wordt beschreven. In het volgende voorbeeld wordt de Nginx-container geïmplementeerd, met een binding van poort 80 van de DC/OS-agent naar poort 80 van de container.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Maak uw eigen JSON-bestand of gebruik het voorbeeld dat is opgegeven bij de [Azure Container Service-demo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Sla dit op een toegankelijke locatie op. Voer vervolgens de volgende opdracht uit om de container te implementeren Geef de naam op van het JSON-bestand.

```powershell
# deploy container

Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

U kunt ook de Marathon API gebruiken om in implementaties van toepassingen uit of in te schalen. In het vorige voorbeeld hebt u één exemplaar van een toepassing geïmplementeerd. Laten we dit uitschalen naar drie exemplaren van een toepassing. Dit doet u door een JSON-bestand te maken met behulp van de volgende JSON-tekst en dit op een toegankelijke locatie op te slaan.

```json
{ "instances": 3 }
```

Voer de volgende opdracht uit om de toepassing uit te schalen.

> [AZURE.NOTE] De URI is http://localhost/marathon/v2/apps/ gevolgd door de id van de toepassing die u wilt schalen. Als u het Nginx-voorbeeld gebruikt dat hier wordt besproken, zou de URI http://localhost/marathon/v2/apps/nginx zijn.

```powershell
# scale container

Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## Volgende stappen

[Meer informatie over de Mesos HTTP-eindpunten]( http://mesos.apache.org/documentation/latest/endpoints/).
[Meer informatie over de Marathon REST API]( https://mesosphere.github.io/marathon/docs/rest-api.html).






<!--HONumber=Jun16_HO2-->



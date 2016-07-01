<properties
   pageTitle="Taakverdeling maken voor een Azure Container Service-cluster | Microsoft Azure"
   description="Maak een taakverdelingen voor een Azure Container Service-cluster."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Containers, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="rogardle"/>

# Taakverdelingen maken voor een Azure Container Service-cluster

In dit artikel wordt er een web-front-end ingesteld die omhoog kan worden geschaald om services te bieden achter Azure LB.


## Vereisten

[Implementeer een exemplaar van Azure Container Service](container-service-deployment.md) met orchestrator-type DCOS, [zorg dat de client verbinding kan maken met uw cluster](container-service-connect.md), en [AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)].


## Taakverdeling

Een Container Service-cluster bevat twee taakverdelingslagen: Azure LB voor openbare invoerpunten (die door de gebruikers wordt gebruikt) en de onderliggende Marathon-taakverdeling die inkomende aanvragen doorstuurt naar containerexemplaren die aanvragen onderhouden. De Marathon-taakverdeling wordt dynamisch aangepast naarmate de containers die de service bieden worden geschaald.

## Marathon-taakverdeling 

De Marathon-taakverdeling wordt dynamisch opnieuw geconfigureerd op basis van de containers die zijn geïmplementeerd. De Marathon-taakverdeling past zich ook aan het verlies van een container of agent aan. Als dit gebeurt, wordt de container door Mesos elders opnieuw gestart en wordt de Marathon-taakverdeling opnieuw geconfigureerd. 

Voer de volgende opdracht vanaf de clientcomputer uit om de Marathon-taakverdeling te installeren.

```bash
dcos package install marathon-lb 
``` 

Met het Marathon-taakverdelingspakket kan er een eenvoudige webserver worden geïmplementeerd volgens deze configuratie:


```json
{ 
  "id": "web", 
  "container": { 
    "type": "DOCKER", 
    "docker": { 
      "image": "tutum/hello-world", 
      "network": "BRIDGE", 
      "portMappings": [ 
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 } 
      ], 
      "forcePullImage":true 
    } 
  }, 
  "instances": 3, 
  "cpus": 0.1, 
  "mem": 65, 
  "healthChecks": [{ 
      "protocol": "HTTP", 
      "path": "/", 
      "portIndex": 0, 
      "timeoutSeconds": 10, 
      "gracePeriodSeconds": 10, 
      "intervalSeconds": 2, 
      "maxConsecutiveFailures": 10 
  }], 
  "labels":{ 
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http" 
  } 
}

```

De belangrijkste onderdelen hiervan zijn: 
  * Stel de waarde voor HAProxy_0_VHOST in op de FQDN van de taakverdeling voor uw agenten. Deze heeft de vorm `<acsName>agents.<region>.cloudapp.azure.com`. Als ik bijvoorbeeld een Container Service-cluster maak met naam `myacs` in regio `West US`, dan krijgt de FQDN de waarde `myacsagents.westus.cloudapp.azure.com`. U kunt deze ook vinden door te zoeken naar de taakverdeling met 'agent' in de naam in de resources in de resourcegroep die u hebt gemaakt voor uw containerservice in de [Azure Portal](https://portal.azure.com).
  * Stel servicePort in voor een poort >= 10.000. Hiermee wordt aangegeven dat de service in deze container wordt uitgevoerd. De marathontaakverdeling gebruikt dit om services te identificeren die moeten worden verdeeld.
  * Stel het label HAPROXY_GROUP in op extern.
  * Stel hostPort in op 0. Hierdoor wordt een willekeurige poort toegewezen.

Kopieer deze JSON naar bestand `hello-web.json` en gebruik het om een container te implementeren: 

```bash
dcos marathon app add hello-web.json 
``` 

## Azure Load Balancer 

Azure Load Balancer heeft standaard de beschikking over poorten 80, 8080 en 443. Als u een van deze drie poorten gebruikt (zoals in het bovenstaande voorbeeld), dan hoeft u niets te doen. U zou de FQDN van de taakverdeling van uw agent moeten bereiken en bij elke vernieuwing bereikt u een van de drie webservers op een round-robinmanier. Als u echter een andere poort gebruikt, dient u een round-robinregel en een probe aan de Azure LB toe te voegen. Dit kunt u doen vanaf [Azure XPLAT CLI](../xplat-cli-azure-resource-manager.md) met de opdrachten `azure lb rule create` en `azure lb probe create`.


## Overige scenario's

U hebt een scenario nodig waarbij u gebruikmaakt van verschillende domeinen om over verschillende services te kunnen beschikken. Bijvoorbeeld: 

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321 

Kijk hier voor op de Engelstalige website [Virtual Hosts](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/). Hier vindt u een methode waarmee u domeinen aan specifieke Marathon-taakverdelingspaden kunt koppelen.

U kunt ook verschillende poorten ter beschikking stellen en deze opnieuw toewijzen aan de juiste service achter de Marathon-taakverdeling. Bijvoorbeeld:

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432 
 

## Volgende stappen

Kijk op [dit blogbericht](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/) voor meer informatie over Marathon-taakverdeling.



<!--HONumber=Jun16_HO2-->



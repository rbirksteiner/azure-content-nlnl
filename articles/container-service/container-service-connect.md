<properties
   pageTitle="Verbinding maken met een Azure Container Service-cluster | Microsoft Azure"
   description="Verbinding maken met een Azure Container Service-cluster met een SSH-tunnel"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Containers, Micro-services, DC/OS, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>


# Verbinding maken met een Azure Container Service-cluster

DC/OS- en Swarm-clusters die zijn geïmplementeerd met Azure Container Service stellen REST-eindpunten beschikbaar. Deze eindpunten zijn echter niet openbaar beschikbaar Voor het beheren van deze eindpunten moet u een SSH-tunnel (Secure Shell) maken. Wanneer een SSH-tunnel eenmaal is ingesteld, kunt u opdrachten uitvoeren op de eindpunten van het cluster en de gebruikersinterface van het cluster weergeven via een browser op uw eigen systeem. In dit document vindt u instructies voor het maken van een SSH-tunnel in Linux, OS X en Windows.

>[AZURE.NOTE] U kunt een SSH-sessie maken met een clusterbeheersysteem. Dit wordt echter niet aangeraden. Wanneer u rechtstreeks in een beheersysteem werkt, bestaat het risico op onbedoelde configuratiewijzigingen.   

## Een SSH-tunnel maken in Linux of OS X

Het eerste wat u doet wanneer u een SSH-tunnel in Linux of OS X maakt, is het lokaliseren van de openbare DNS-naam van masters met gelijke taakverdeling. Hiervoor vouwt u de resourcegroep uit zodat elke resource wordt weergegeven. Zoek en selecteer het openbare IP-adres van de master. Hiermee opent u een blade die informatie bevat over het openbare IP-adres, dat de DNS-naam omvat. Bewaar deze naam voor later gebruik. <br />


![Openbare DNS-naam](media/pubdns.png)

Open nu een shell en voer de volgende opdracht uit, waarbij:

**PORT** de naam is van het eindpunt dat u beschikbaar wilt maken. Voor Swarm is dit 2375. Gebruik poort 80 voor DC/OS.  
**USERNAME** de naam is van de gebruiker die is opgegeven tijdens de implementatie van het cluster.  
**DNSPREFIX** het DNS-voorvoegsel is dat is opgegeven tijdens de implementatie van het cluster.  
**REGION** de regio is waarin uw resourcegroep zich bevindt.  
**PATH_TO_PRIVATE_KEY** [OPTIONEEL] het pad is naar de persoonlijke sleutel die overeenkomt met de openbare sleutel die u hebt opgegeven bij het maken van het containerservicecluster. Gebruik deze optie met de vlag -i.

```bash
# ssh sample

ssh -L PORT:localhost:PORT -f -N [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com -p 2200
```
> De poort voor de SSH-verbinding is 2200 en niet 22 (de standaardpoort).

## DC/OS-tunnel

Wanneer u een tunnel wilt openen naar de DC/OS-gerelateerde eindpunten, voert u een opdracht uit die vergelijkbaar is met de volgende:

```bash
# ssh sample

sudo ssh -L 80:localhost:80 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

U hebt nu toegang tot de DC/OS-gerelateerde eindpunten op:

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

U kunt ook de REST API's voor elke toepassing via deze tunnel bereiken.

## Swarm-tunnel

Wanneer u een tunnel wilt openen naar het Swarm-eindpunt, voert u een opdracht uit die vergelijkbaar is met de volgende:

```bash
# ssh sample

ssh -L 2375:localhost:2375 -f -N azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com -p 2200
```

U kunt nu uw omgevingsvariabele DOCKER_HOST als volgt instellen en de Docker-opdrachtregelinterface (CLI) op dezelfde wijze blijven gebruiken.

```bash
export DOCKER_HOST=:2375
```

## Een SSH-tunnel maken in Windows

Er zijn meerdere opties voor het maken van SSH-tunnels in Windows. In dit document wordt beschreven hoe u dit met PuTTY kunt doen.

Download PuTTY naar uw Windows-systeem en voer de toepassing uit.

Voer een hostnaam in die bestaat uit de naam van de cluserbeheerdergebruiker en de openbare DNS-naam van de eerste master in het cluster. De **hostnaam** ziet er ongeveer als volgt uit: `adminuser@PublicDNS`. Voer 2200 in bij **Poort**.

![PuTTY-configuratie 1](media/putty1.png)

Selecteer `SSH` en `Authentication`. Voeg uw persoonlijke sleutelbestand toe voor verificatiedoeleinden.

![PuTTY-configuratie 2](media/putty2.png)

Selecteer `Tunnels` en configureer de volgende doorgestuurde poorten:
- **Bronpoort:** uw voorkeur, gebruik 80 voor DC/OS of 2375 voor Swarm.
- **Doelpoort:** gebruik localhost:80 voor DC/OS of localhost:2375 voor Swarm.

Het volgende voorbeeld is geconfigureerd voor DC/OS, maar ziet er ongeveer hetzelfde uit voor Docker Swarm.

>[AZURE.NOTE] Poort 80 mag niet in gebruik zijn wanneer u deze tunnel maakt.

![PuTTY-configuratie 3](media/putty3.png)

Wanneer u klaar bent, slaat u de verbindingsconfiguratie op en verbindt u de PuTTY-sessie. Wanneer u verbinding maakt, kunt u de poortconfiguratie in het PuTTY-gebeurtenislogboek zien.

![PuTTY-gebeurtenislogboek](media/putty4.png)

Wanneer u de tunnel voor DC/OS hebt geconfigureerd, hebt u toegang tot het gerelateerde eindpunt op:

- DC/OS: `http://localhost/`
- Marathon: `http://localhost/marathon`
- Mesos: `http://localhost/mesos`

Wanneer u de tunnel voor Docker Swarm hebt geconfigureerd, hebt u toegang tot het Swarm-cluster via de Docker CLI. U moet eerst een Windows-omgevingsvariabele configureren genaamd `DOCKER_HOST` met een waarde van ` :2375`.

## Volgende stappen

Implementeer en beheer containers met DC/OS of Swarm.

[Werken met Azure Container Service en DC/OS](container-service-mesos-marathon-rest.md)
[Werken met de Azure Container Service en Docker Swarm](container-service-docker-swarm.md)



<!--HONumber=Jun16_HO2-->



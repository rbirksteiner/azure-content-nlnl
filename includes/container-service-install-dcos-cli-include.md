<properties
   pageTitle="De DC/OS CLI installeren| Microsoft Azure"
   description="Installeer de DC/OS CLI."
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
   ms.date="05/10/2016"
   ms.author="rogardle"/>

>[AZURE.NOTE] Dit is voor als u met op DC/OS gebaseerde ACS-clusters werkt. U hoeft dit niet te doen voor op Swarm gebaseerde ACS-clusters.

Eerst [maakt u verbinding met uw op DC/OS gebaseerde ACS-cluster](../articles/container-service/container-service-connect.md). Als u dit hebt gedaan, kunt u met de onderstaande opdrachten de DC/OS CLI installeren op de clientcomputer:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Als u een oude versie van Python gebruikt, is het mogelijk dat u een aantal 'InsecurePlatformWarnings'-waarschuwingen krijgt te zien. U kunt deze gewoon negeren.

Als u aan de slag wilt zonder dat u de shell opnieuw moet opstarten, voert u deze opdracht uit:

```bash
source ~/.bashrc
```

De stap is niet nodig als u nieuwe shells start.

U kun nu bevestigen dat de CLI is geïnstalleerd:

```bash
dcos --help
```


<!--HONumber=Jun16_HO2-->



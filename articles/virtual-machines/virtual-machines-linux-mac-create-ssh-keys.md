<properties
    pageTitle="SSH-sleutels maken in Linux en Mac | Microsoft Azure"
    description="SSH-sleutels genereren en gebruiken in Linux en Mac voor het Resource Manager- en het klassieke implementatiemodel in Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/16/2016"
    ms.author="v-livech"/>

# SSH-sleutels maken in Linux en Mac voor virtuele Linux-machines in Azure

Als u een met een wachtwoord beveiligde openbare en persoonlijke SSH-sleutel wilt maken, moet er op uw werkstation een terminal zijn geopend.  Wanneer u SSH-sleutels hebt, kunt u standaard nieuwe virtuele machines maken met die sleutel of de openbare sleutel toevoegen aan bestaande virtuele machines met zowel Azure CLI- als Azure-sjablonen.  Dit biedt de mogelijkheid tot wachtwoordloze aanmelding via SSH met de in vergelijking met wachtwoorden veel veiligere verificatiemethode via sleutels.

## Lijst met snelle opdrachten

Vervang in de volgende opdrachtvoorbeelden de waarden tussen &lt; en &gt; door de waarden van uw eigen omgeving.

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

Voer de naam in van het bestand dat in de `~/.ssh/`-map wordt opgeslagen:

```bash
<azure_fedora_id_rsa>
```

Voer de wachtwoordzin in voor azure_fedora_id_rsa:

```bash
<correct horse battery staple>
```

Voeg de nieuwe sleutel toe aan `ssh-agent` in Linux en Mac (ook toegevoegd aan OSX-sleutelketen):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Kopieer de openbare SSH-sleutel naar de Linux-server:

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

Test de aanmelding via sleutels in plaats van een wachtwoord:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## Inleiding

Het gebruik van openbare en persoonlijke SSH-sleutels is de eenvoudigste manier om u bij uw Linux-servers aan te melden. De [cryptografie van openbare sleutels](https://en.wikipedia.org/wiki/Public-key_cryptography) biedt u bovendien een veel veiligere manier van aanmelding bij uw virtuele Linux- of BSD-machines in Azure dan wachtwoorden, die aanzienlijk eenvoudiger met brute kracht kunnen worden aangevallen. Uw openbare sleutel kan worden gedeeld met iedereen, maar alleen u (of uw lokale beveiligingsinfrastructuur) beschikt over uw persoonlijke sleutel.  De persoonlijke SSH-sleutel heeft ter bescherming een [beveiligd wachtwoord](https://www.xkcd.com/936/). Dit wachtwoord dient alleen voor toegang tot de persoonlijke SSH-sleutel en is **niet** het wachtwoord voor het gebruikersaccount.  Wanneer u een wachtwoord aan uw SSH-sleutel toevoegt, wordt de persoonlijke sleutel gecodeerd, zodat deze niet kan worden gebruikt zonder het wachtwoord om deze te ontgrendelen.  Als een aanvaller erin zou slagen om uw persoonlijke sleutel te stelen en deze geen wachtwoord zou hebben, zou de aanvaller in staat zijn om de persoonlijke sleutel te gebruiken voor aanmelding bij de servers waarop de bijbehorende openbare sleutel is geïnstalleerd.  Als de persoonlijke sleutel is beveiligd met een wachtwoord, kan deze niet worden gebruikt door de aanvaller. Zo beschikt u over een extra beveiligingslaag voor uw infrastructuur in Azure.


In dit artikel worden sleutelbestanden in *ssh-rsa*-indeling gemaakt, aangezien deze worden aanbevolen voor implementaties in de Resource Manager en in de [portal](https://portal.azure.com) vereist zijn voor zowel klassieke implementaties als Resource Manager-implementaties.


## SSH-sleutels maken

Azure vereist openbare en persoonlijke sleutels van ten minste 2048 bits in ssh-rsa-indeling. Voor het maken van het paar gebruiken we `ssh-keygen`, die een reeks vragen stelt en vervolgens een persoonlijke sleutel en een overeenkomende openbare sleutel schrijft. Wanneer u uw virtuele machine in Azure maakt, geeft u de inhoud van de openbare sleutel door. Deze wordt gekopieerd naar de virtuele Linux-machine en vervolgens samen met uw lokale en veilig opgeslagen persoonlijke sleutel gebruikt om u te verifiëren wanneer u zich aanmeldt.

## ssh-keygen gebruiken

Met deze opdracht wordt een met een wachtwoord beveiligd (versleuteld) SSH-sleutelpaar gemaakt met 2048-bits RSA waaraan opmerkingen worden toegevoegd om het gemakkelijk te kunnen identificeren.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_Uitleg van de opdracht_

`ssh-keygen` = het programma dat wordt gebruikt voor het maken van de sleutels

`-t rsa` = type sleutel dat moet worden gemaakt, namelijk de [RSA-indeling](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = aantal bits van de sleutel

`-C "ahmet@fedoraVMAzure"` = een opmerking die wordt toegevoegd aan het einde van het openbare sleutelbestand om het gemakkelijk te identificeren.  Normaal gesproken bestaat de opmerking uit een e-mailadres, maar u kunt ook iets anders gebruiken als dat beter aansluit bij uw infrastructuur.

## Overzicht van ssh-keygen

Elke stap in detail uitgelegd.  Voer eerst `ssh-keygen` uit.

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Opgeslagen sleutelbestanden:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

De naam van het sleutelpaar voor dit artikel.  De naam **id_rsa** is de standaardwaarde voor een sleutelpaar. Sommige programma’s verwachten mogelijk **id_rsa** als naam voor het persoonlijke sleutelbestand, dus is het een goed idee als u er een hebt. (`~/.ssh/` is de gebruikelijke standaardlocatie voor al uw SSH-sleutelparen en het SSH-configuratiebestand.)

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
Hier ziet u uw nieuwe sleutelparen en de bijbehorende machtigingen. `ssh-keygen` maakt de `~/.ssh`-map als deze niet aanwezig is en stelt ook het juiste eigendom en de juiste bestandsmodi in.

Sleutelwachtwoord:

`Enter passphrase (empty for no passphrase):`

Het wordt sterk aanbevolen een wachtwoord (`ssh-keygen` noemt dit een 'wachtwoordzin') toe te voegen aan uw sleutelparen. Zonder een wachtwoord dat het sleutelpaar beveiligt, kan iedereen met een exemplaar van het persoonlijke sleutelbestand zich aanmelden bij een server (uw server) die beschikt over de bijbehorende openbare sleutel. Het toevoegen van een wachtwoord biedt daarom veel meer bescherming in het geval iemand in staat is toegang te krijgen tot uw persoonlijke sleutelbestand. In de extra tijd die dit oplevert, kunt u de sleutels wijzigen waarmee u wordt geverifieerd.

## ssh-agent gebruiken voor het opslaan van het wachtwoord van uw persoonlijke sleutel

Om te voorkomen dat u het wachtwoord van uw persoonlijke sleutelbestand bij elke SSH-aanmelding moet opgeven, kunt u `ssh-agent` gebruiken om het wachtwoord van uw persoonlijke sleutelbestand in de cache te plaatsen. Zo kunt u zich snel en veilig bij de virtuele Linux-machine aanmelden.  Als u OSX gebruikt, worden de wachtwoorden van uw persoonlijke sleutels veilig opgeslagen wanneer u `ssh-agent` aanroept.

Controleer eerst of `ssh-agent` wordt uitgevoerd.

```bash
eval "$(ssh-agent -s)"
```

Voeg nu de persoonlijke sleutel aan `ssh-agent` toe met de opdracht `ssh-add`. Opnieuw wordt hiermee in OSX de sleutelketen gestart waarin de referenties worden opgeslagen.

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

Het wachtwoord voor de persoonlijke sleutel wordt nu opgeslagen, zodat u dit niet bij elke SSH-aanmelding hoeft op te geven.

## Een SSH-configuratiebestand maken en configureren

Hoewel dit niet absoluut noodzakelijk is om uw virtuele Linux-machine in bedrijf te stellen, wordt het aanbevolen om een `~/.ssh/config`-bestand te maken en te configureren. Zo voorkomt u dat u per ongeluk wachtwoorden gebruikt om u aan te melden bij uw virtuele machines, het gebruik van verschillende sleutelparen voor verschillende virtuele machines in Azure automatiseert en andere programma’s configureert, zoals **git** om meerdere servers te benaderen.

In het volgende voorbeeld wordt een standaardconfiguratie getoond.

### Het bestand maken

```bash
touch ~/.ssh/config
```

### Bewerk het bestand om de nieuwe SSH-configuratie toe te voegen:

```bash
vim ~/.ssh/config
```

### Voorbeeld van `~/.ssh/config`-bestand:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

Deze SSH-configuratie biedt u afzonderlijke secties voor de diverse services, zodat elke service kan beschikken over een eigen toegewezen sleutelpaar. De standaardinstellingen zijn voor alle hosts waarbij u bent aangemeld die niet overeenkomen met een van de hierboven genoemde groepen.


### Uitleg van configuratiebestand

`Host` = de naam van de host die wordt aangeroepen op de terminal.  `ssh fedora22` vertelt `SSH` dat de waarden moeten worden gebruikt in het instellingenblok met het label `Host fedora22` Opmerking: dit kan elk label zijn dat logisch is voor uw gebruik. Het label vertegenwoordigt niet de werkelijke hostnaam van een server.

`Hostname 102.160.203.241` = het IP-adres of de DNS-naam van de server waarbij wordt aangemeld. Dit wordt gebruikt om de server te routeren en mag een extern IP-adres zijn dat is toegewezen aan een intern IP-adres.

`User git` = het account van de externe gebruiker dat moet worden gebruikt bij het aanmelden bij de virtuele machine in Azure.

`PubKeyAuthentication yes` = dit vertelt SSH dat u gebruik wilt maken van een SSH-sleutel voor aanmelding.

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = dit vertelt SSH welk sleutelpaar aan de server moet worden gepresenteerd voor het verifiëren van de aanmelding.


## SSH in Linux zonder een wachtwoord

Nu u een SSH-sleutelpaar en een geconfigureerd SSH-configuratiebestand hebt, kunt u zich snel en veilig aanmelden bij uw virtuele Linux-machine. De eerste keer dat u zich met een SSH-sleutel bij een server aanmeldt, wordt u gevraagd naar de wachtwoordzin voor dat sleutelbestand.

```bash
ssh fedora22
```

### Uitleg van de opdracht

Wanneer `ssh fedora22` wordt uitgevoerd, zoekt en laadt SSH eerst instellingen uit het `Host fedora22`-blok en laadt het vervolgens alle overige instellingen uit het laatste blok, `Host *`.

## Volgende stappen

De volgende stap bestaat uit het maken van virtuele Linux-machines in Azure met de nieuwe openbare SSH-sleutel.  Virtuele machines in Azure die zijn gemaakt met een openbare SSH-sleutel voor aanmelding, zijn beter beveiligd dan virtuele machines die zijn gemaakt met een wachtwoord als standaardaanmeldingsmethode.  Virtuele machines in Azure die SSH-sleutels gebruiken voor aanmelding, worden standaard zo geconfigureerd dat aanmelding met een wachtwoord niet mogelijk is. Dit voorkomt aanvallen met brute kracht.

- [Een beveiligde virtuele Linux-machine maken met een Azure-sjabloon](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Een beveiligde virtuele Linux-machine maken met de Azure Portal](virtual-machines-linux-quick-create-portal.md)
- [Een beveiligde virtuele Linux-machine maken met de Azure CLI](virtual-machines-linux-quick-create-cli.md)



<!--HONumber=Jun16_HO2-->



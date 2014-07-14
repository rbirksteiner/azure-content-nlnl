<properties  linkid="manage-scenarios-choose-web-app-service" urlDisplayName="Web Options for Azure" pageTitle="Azure Web Sites, Cloud Services and Virtual Machines comparison" metaKeywords="Cloud Services, Virtual Machines, Web Sites" description="Learn when to use Azure Web Sites, Cloud Services, and Virtual Machines for hosting web applications. Review a feature comparison." metaCanonical="" services="web-sites,virtual-machines,cloud-services" documentationCenter="" title=" Cloud Services" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

# Vergelijking van Azure Web Sites, Cloud Services en Virtual Machines

Azure biedt verscheidene manieren om uw webtoepassingen te hosten, zoals [Azure Web Sites][1], [Cloud Services][2] en [Virtual Machines][3]. Na het bekijken van deze opties weet u misschien niet meer zeker welke het beste aansluit bij uw behoeften, of misschien kent u begrippen als IaaS vs PaaS niet zo goed. Dit artikel geeft uitleg over uw opties en helpt u bij het maken van de juiste keuze voor uw webscenario. Hoewel u met alle drie de opties zeer schaalbare webtoepassingen kunt uitvoeren in Azure, zijn er ook verschillen die de doorslag kunnen geven.

Voor de meeste situaties is Azure Web Sites de beste optie. U beschikt over eenvoudige, flexibele opties voor implementatie en beheer en u kunt websites met grote volumes hosten. U kunt snel een nieuwe website maken met populaire software, zoals WordPress, uit de Web Application Gallery, of u kunt een bestaande website verplaatsen naar Azure Web Sites. Met de [Azure WebJobs SDK][4] (momenteel als preview) kunt u ook taken op de achtergrond uitvoeren.

U hebt ook de optie om webtoepassingen te hosten op Azure Cloud Services of Azure Virtual Machines. Dit zijn goede opties als uw websitelaag een hoog niveau vereist van beheer- en aanpassingsmogelijkheden. Dit hogere beheerniveau brengt echter wel toegenomen complexiteit met zich mee bij het creëren, beheren en implementeren van toepassingen. In het volgende diagram worden de voor- en nadelen van de drie opties vergeleken.

![ChoicesDiagram](./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_2.png)

Het opzetten, beheren en bewaken van websites is gemakkelijker, maar u beschikt over minder configuratieopties. Het belangrijkste is dat u het beste Azure Web Sites kunt gebruiken als u geen dwingende reden hebt om uw front-end op Cloud Services of Virtual Machines te plaatsen. In de rest van dit document wordt de informatie gegeven die u nodig hebt om een goede keuze te kunnen maken. Dit omvat:

* [Scenario's](#scenarios)
* [Serviceoverzicht](#services)
* [Vergelijking van functies](#features)

## <a name="scenarios"></a>Scenario's

### Ik heb een klein bedrijf. Ik zoek een goedkope manier om mijn website te hosten, maar dan wel met het oog op groei in de toekomst.

Azure Web Sites (WAWS) is in dit geval een fantastische oplossing, want u kunt deze service in het begin gratis gebruiken en later meer mogelijkheden toevoegen als u deze nodig hebt. Zo krijgt iedere gratis website een domein van Azure (*uw_bedrijf*.azurewebsites.net). Als u eraan toe bent uw eigen domein te gebruiken, kunt u dit toevoegen vanaf $9,80 (vanaf 1/2014). Er zijn nog vele andere services en schaalopties waarmee de site kan meegroeien met de toenemende eisen. Met **Azure Web Sites** kunt u:

* Beginnen met de gratis laag en breid uit wanneer dat nodig is.
* Gebruik de Application Gallery om snel populaire webtoepassingen zoals
  WordPress op te zetten.
* Voeg naar behoefte aanvullende services en functies van Azure toe aan
  uw toepassing.
* Stel uw website veilig met HTTPS door middel van het certificaat dat
  geleverd is bij uw domeinnaam *uw_bedrijf*.azurewebsites.net.

### Ik ben webdesigner of grafisch designer en wil websites ontwerpen en bouwen voor mijn klanten

Voor webontwikkelaars biedt Azure Web Sites alles wat nodig is om geavanceerde webtoepassingen te kunnen maken. Web Sites biedt naadloze integratie met hulpprogramma's als Visual Studio en SQL database. Met **Web Sites** kunnen ontwikkelaars:

* Opdrachtregelprogramma's gebruiken voor [geautomatiseerde taken][5].
* Werken met populaire talen als [.Net][6], [PHP][7], [Node.js][8]en
  [Python][9].
* Drie verschillende schaalniveaus kiezen voor de opschaling naar zeer
  hoge capaciteiten.
* Integreren met andere services van Azure, zoals [SQL Database][10],
  [Service Bus][11] en [Storage][12], of door partners aangeboden
  services uit de [Azure Store][13], zoals MySQL en MongoDB.
* Integratie met hulpprogramma's zoals Visual Studio, Git, WebMatrix,
  WebDeploy, TFS en FTP.

### Ik migreer mijn toepassing met meerdere lagen met een web-front-end naar de Cloud

Als u een toepassing met meerdere lagen uitvoert, zoals een webserver die communiceert met een databaseserver om websitegegevens op te slaan en op te halen, beschikt u in Azure over meerdere opties. Tot deze architectuuropties behoren Web Sites, Cloud Services en Virtual Machines. Allereerst is **Web Sites** een goede optie voor de weblaag van uw oplossing en kan worden gebruikt met Azure SQL Database voor het maken van een architectuur met twee lagen. Via Web Sites kunt u achtergrondprocessen of langlopende processen uitvoeren met behulp van de preview-versie van Azure WebJobs SDK. Als u een ingewikkeldere architectuur of flexibelere schaalopties nodig hebt, kunt u beter kiezen voor Cloud Services of Virtual Machines.

Met **Cloud Services** kunt u het volgende doen:

* Services hosten voor het web, de middelste laag en de back-end op
  schaalbare web- en worker-rollen
* Alleen de services voor de middelste laag en de back-end hosten op
  worker-rollen, terwijl de front-end op Azure Web Sites blijft.
* Services voor front-end en back-end afzonderlijk schalen.

Met **Virtual Machines** kunt u het volgende doen:

* Gemakkelijker sterk aangepaste omgevingen migreren als
  installatiekopie van een virtuele machine.
* Software of services uitvoeren die niet geconfigureerd kunnen worden
  op Web Sites of Cloud Services.

### Mijn toepassing is afhankelijk van sterk aangepaste Windows- of Linux-omgevingen

Als voor uw toepassing complexe installatie en configuratie van software en het besturingssysteem nodig zijn, is Virtual Machines de beste oplossing. Met **Virtual Machines** kunt u het volgende doen:

* De Virtual Machine-galerie gebruiken om te beginnen met een
  besturingssysteem, zoals Windows of Linux, en dit vervolgens aan te
  passen aan uw toepassingsvereisten.
* Een aangepaste installatiekopie van een bestaande lokale server maken
  en uploaden om uit te voeren op een virtuele machine in Azure.

### Mijn site maakt gebruik van opensourcesoftware en ik wil deze hosten in Azure

Alle drie de opties maken het mogelijk om opensourcetalen en opensourceframeworks te hosten. **Cloud Services** vereist het gebruik van opstarttaken voor het installeren en configureren van eventueel vereiste opensourcesoftware die wordt uitgevoerd op Windows. Met **Virtual Machines** installeert en configureert u de software op de installatiekopie van de machine, welke gebaseerd kan zijn op Windows of Linux. Als uw opensourceframework wordt ondersteund op Web Sites, biedt dit een eenvoudigere manier om dit soort toepassingen te hosten. Web Sites kan namelijk automatisch worden geconfigureerd met de talen en frameworks die uw toepassing nodig heeft. Met **Web Sites** kunt u het volgende doen:

* Veel populaire opensourcetalen gebruiken, zoals [.Net][6], [PHP][7],
  [Node.js][8]en [Python][9].
* WordPress, Drupal, Umbraco, DNN en vele andere
  niet-Microsoft-webtoepassingen installeren.
* Een bestaande toepassing migreren of een nieuwe maken uit de
  Application Gallery.

### Ik heb een Line-Of-Business-toepassing die verbonden moet zijn met het bedrijfsnetwerk

Als u een Line-Of-Business-toepassing wilt maken, is voor uw website mogelijk directe toegang tot services of gegevens op het bedrijfsnetwerk vereist. Dit is mogelijk op **Web Sites**, **Cloud Services** en **Virtual Machines**. Er zijn verschillen in de benadering die u volgt, waaronder de volgende:

* Web Sites kan een veilige verbinding maken met lokale bedrijfsbronnen
  door middel van Service Bus Relay. Hiermee kunnen services op het
  bedrijfsnetwerk taken uitvoeren namens de webtoepassing, zonder alles
  naar de Cloud te verplaatsen of een virtueel netwerk te maken.
* Cloud Services en Virtual Machines kunnen gebruik maken van Virtual
  Network. Met Virtual Network kunnen machines die worden uitgevoerd in
  Azure in feite een verbinding maken met een lokaal netwerk. Azure
  wordt dan een uitbreiding van het datacenter van uw bedrijf.

### Ik wil een REST-API of webservice voor mobiele clients hosten

Met op HTTP-gebaseerde webservices is het mogelijk een grote verscheidenheid aan clients te ondersteunen, inclusief mobiele clients. Frameworks zoals de ASP.NET Web API integreren met Visual Studio zodat het eenvoudiger is om REST-services aan te maken en te gebruiken. Deze services worden weergegeven vanaf een webeindpunt, dus het is mogelijk om verschillende soorten webhostingtechnieken op Azure te gebruiken om dit scenario te ondersteunen. Toch is **Web Sites** een goede keuze voor het hosten van REST-API's. Met Web Sites kunt u het volgende doen:

* Snel een website aanmaken om een HTTP-webservice te hosten in een van
  de wereldwijd verspreide datacenters van Azure™.
* Bestaande services migreren of nieuwe maken, waarbij u mogelijk baat
  heeft bij de ASP.NET Web API in Visual Studio.
* SLA bereiken voor beschikbaarheid met één exemplaar, of uitschalen
  naar meerdere toegewezen machines.
* De gepubliceerde site gebruiken om REST-API's te verschaffen aan
  HTTP-clients, inclusief mobiele clients.

## <a name="services"></a>Serviceoverzicht

Met[Azure Web Sites][1] kunt u snel zeer schaalbare websites bouwen op Azure. U kunt de Azure Portal of de opdrachtregelprogramma's gebruiken om een website op te stellen met populaire talen, zoals .NET, PHP, Node.js, en Python. Ondersteunde frameworks zijn reeds geïmplementeerd en vereisen geen verdere installatiestappen. De galerie van Azure Web Sites bevat een groot aantal niet-Microsoft-toepassingen zoals Drupal en WordPress alsmede ontwikkelingsframeworks zoals Django en CakePHP. Nadat u een site heeft gemaakt, kunt u ofwel een bestaande website migreren ofwel een geheel nieuwe website bouwen. Web Sites maakt het beheren van de fysieke hardware overbodig en biedt tevens diverse schaalopties. U kunt overgaan van een gedeeld multitenantmodel naar een standaardmodus, waarbij toegewezen machines binnenkomend verkeer afhandelen. Met Web Sites kunt u ook andere Azure-services integreren, zoals SQL Database, Service Bus en Storage. Door gebruik te maken van de preview van [Azure WebJobs SDK][4], kunt u achtergrondverwerking toevoegen. Samengevat maakt Azure Web Sites het eenvoudiger om u te richten op toepassingsontwikkeling door de ondersteuning van een breed scala aan talen, opensourcetoepassingen en implementatiemethoden (FTP, Git, Web Deploy of TFS). Indien u geen speciale vereisten hebt voor de noodzaak van Cloud Services of Virtual Machines, is een Azure-website zeer waarschijnlijk de beste keuze.

Met [Cloud Services][2] kunt u maximaal beschikbare, schaalbare webtoepassingen maken in een rijke PaaS (Platform as a Service)-omgeving. In tegenstelling tot Web Sites wordt een cloudservice eerst gemaakt in een ontwikkelingsomgeving, zoals Visual Studio, en vervolgens geïmplementeerd in Azure. Frameworks, zoals PHP, vereisen aangepaste implementatiestappen of taken die het framework installeren bij het opstarten van de rol. Het voornaamste voordeel van Cloud Services is de mogelijkheid om meerdere complexe architecturen voor meerdere lagen te ondersteunen. Een enkele cloudservice kan bestaan uit een front-end webrol en een of meerdere worker-rollen. Elke laag kan afzonderlijk worden geschaald. Tevens is er een hoger beheerniveau over de structuur van uw webtoepassing. U kunt bijvoorbeeld via het externe bureaublad de machines gebruiken die de rolexemplaren uitvoeren. Ook kunt u configuratiewijzigingen van geavanceerdere IIS'en en machines scripten die worden uitgevoerd bij de rolopstart, inclusief taken waarvoor beheer is vereist.

Met [Virtual Machines][3] kunt u webtoepassingen uitvoeren op virtuele machines in Azure. Deze mogelijkheid staat ook bekend als IaaS (Infrastructure as a Service). Maak via de portal nieuwe Windows-servers of Linux-machines, of upload een bestaande installatiekopie van een virtuele machine. Virtual Machines biedt u de hoogste mate van beheer over het besturingssysteem, configuratie en geïnstalleerde software en services. Dit is een goede optie voor de migratie van complexe lokale webtoepassingen naar de cloud, omdat de machines in hun geheel kunnen worden verplaatst. Met Virtual Networks kunt u deze virtuele machines tevens verbinden met lokale bedrijfsnetwerken. Net zoals met Cloud Services heeft u op afstand toegang tot deze machines en de mogelijkheid om configuratieveranderingen op systeembeheerdersniveau uit te voeren. In tegenstelling tot Web Sites en Cloud Services dient u echter de installatiekopie van de virtuele machines en de toepassingsarchitectuur volledig op infrastructuurniveau te beheren. Een voorbeeld hiervan is dat u uw eigen patches op het besturingssysteem moet toepassen.

## <a name="features"></a>Vergelijking van functies

In de onderstaande tabel wordt een vergelijking weergegeven van de mogelijkheden van Web Sites, Cloud Services en Virtual Machines, zodat u de beste keuze kunt maken. Vakjes met een sterretje worden nader toegelicht in de opmerkingen onder de tabel.

<table  cellspacing="0" border="1">
<tr>
   <th  align="left" valign="middle">Functie</th>

   <th  align="left" valign="middle">Web Sites</th>

   <th  align="left" valign="middle">Cloud Services (webrollen)</th>

   <th  align="left" valign="middle">Virtual Machines</th>

</tr>

<tr>
   <td  valign="middle"><p>Toegang tot services zoals Service Bus, Storage, SQL Database</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Hosten van web- of webserviceslaag in een architectuur met meerdere lagen</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Hosten van middelste laag in een architectuur met meerdere lagen</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Geïntegreerde ondersteuning van MySQL-as-a-service</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>1</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Ondersteuning van ASP.NET, klassieke ASP, Node.js, PHP, Python</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Uitbreiding naar meerdere exemplaren zonder opnieuw te implementeren</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X <sup>2</sup>
</td>

</tr>

<tr>
   <td  valign="middle"><p>Ondersteuning van SSL</p>
</td>

   <td  valign="middle">X <sup>3</sup>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Visual Studio-integratie</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Foutopsporing op afstand</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Code distribueren met TFS</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Code distribueren met GIT, FTP</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Code distribueren met Web Deploy</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle"><sup>4</sup>
</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Ondersteuning van WebMatrix</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Bijna onmiddellijke implementatie</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Exemplaren delen en configuratie</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Opschalen naar grotere machines zonder opnieuw te implementeren</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Meerdere implementatieomgevingen (productie en fasering)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Netwerkisolatie met Azure Virtual Network</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Ondersteuning van Azure Traffic Manager</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Toegang tot servers via extern bureaublad</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Mogelijkheid tot definiëren/uitvoeren opstarttaken</p>
</td>

   <td  valign="middle" />

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Automatisch beheer van updates van besturingssysteem</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

<tr>
   <td  valign="middle"><p>Geïntegreerde eindpuntbewaking</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

</tr>

<tr>
   <td  valign="middle"><p>Naadloos schakelen tussen platforms (32-bits/64-bits)</p>
</td>

   <td  valign="middle">X</td>

   <td  valign="middle">X</td>

   <td  valign="middle" />

</tr>

</table>

<sup>1</sup> Web- of worker-rollen kunnen MySQL-as-a-service integreren via aanbiedingen van ClearDB, maar niet als onderdeel van de Management Portal-werkstroom.

<sup>2</sup> Hoewel Virtual Machines kunnen uitbreiden naar meerdere exemplaren, dienen de services die op deze machines worden uitgevoerd zodanig te worden geschreven dat ze met deze uitbreiding kunnen omgaan. Er moet een aanvullende taakverdeler worden geconfigureerd om opdrachten naar de machines te routeren. Ten slotte dient er een Affiniteitsgroep te worden gemaakt voor alle machines die lid zijn van de rol om ze te beschermen tegen gelijktijdig herstarten door onderhouds- of hardwarefouten.

<sup>3</sup> Voor Web Sites wordt SSL voor aangepaste domeinnamen alleen ondersteund in de standaardmodus. Voor meer informatie over het gebruik van SSL met Web Sites, zie [Een SSL-certificaat voor een Azure-website configureren][14].

<sup>4</sup> Web Deploy wordt ondersteund voor cloudservices tijdens het implementeren naar rollen met één exemplaar. Voor productierollen zijn echter meerdere exemplaren vereist om aan de Azure SLA te voldoen. Web Deploy is daarom geen geschikt mechanisme voor de implementatie van cloudservices tijdens de productie.



[1]: http://go.microsoft.com/fwlink/?LinkId=306051
[2]: http://go.microsoft.com/fwlink/?LinkId=306052
[3]: http://go.microsoft.com/fwlink/?LinkID=306053
[4]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[5]: http://www.windowsazure.com/en-us/documentation/scripts/?services=web-sites
[6]: http://www.windowsazure.com/en-us/develop/net/
[7]: http://www.windowsazure.com/en-us/develop/php/
[8]: http://www.windowsazure.com/en-us/develop/nodejs/
[9]: http://www.windowsazure.com/en-us/develop/python/
[10]: http://www.windowsazure.com/en-us/documentation/services/sql-database/
[11]: http://www.windowsazure.com/en-us/documentation/services/service-bus/
[12]: http://www.windowsazure.com/en-us/documentation/services/storage/
[13]: http://www.windowsazure.com/en-us/gallery/store/
[14]: http://www.windowsazure.com/en-us/develop/net/common-tasks/enable-ssl-web-site/
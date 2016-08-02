<properties
    pageTitle="Een web-app in Azure App Service verbinden met Redis-cache via het Memcache-protocol | Microsoft Azure"
    description="Een web-app in Azure App Service verbinden met Redis-cache met het Memcache-protocol"
    services="app-service\web"
    documentationCenter="php"
    authors="SyntaxC4"
    manager="wpickett"
    editor="riande"/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="windows"
    ms.workload="na"
    ms.date="02/29/2016"
    ms.author="cfowler"/>

# Een web-app in Azure App Service verbinden met Redis-cache via het Memcache-protocol

In dit artikel wordt uitgelegd hoe u een WordPress-web-app in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) verbindt met [Azure Redis-Cache][12] met behulp van het [Memcache][13]-protocol. Als u een bestaande web-app hebt die een Memcached-server gebruikt voor caching in het geheugen, kunt u deze migreren naar Azure App Service en de eigen cachingoplossing van Microsoft Azure gebruiken met weinig of geen wijzigingen in de toepassingscode. Bovendien kunt u uw Memcache-expertise gebruiken om in Azure App Service uiterst schaalbare, gedistribueerde apps te maken met Azure Redis-cache voor caching in het geheugen, terwijl u gebruik kunt maken van populaire toepassingsframeworks zoals .NET, PHP, Node.js, Java en Python.  

Met App Service Web Apps kan in dit toepassingsscenario de Memcache-shim van Web Apps worden gebruikt. Dit is een lokale Memcache-server die als een Memcache-proxy fungeert om oproepen naar Azure Redis-cache op te slaan in de cache. Hierdoor kunnen alle apps die met het Memcache-protocol communiceren, gegevens in de cache opslaan met Redis-cache. Deze Memcache-shim werkt op protocolniveau en kan dus door een toepassing of toepassingsframework worden gebruikt zolang deze communiceert via het Memcache-protocol.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## Vereisten

De Memcache-shim van Web Apps kan worden gebruikt met elke toepassing, mits deze communiceert via het Memcache-protocol. De toepassing in dit specifieke voorbeeld is een schaalbare WordPress-site die kan worden ingericht vanuit Azure Marketplace.

Volg de stappen in deze artikelen:

* [Een exemplaar van de Azure Redis Cache Service inrichten][0]
* [Een schaalbare WordPress-site implementeren in Azure][1]

Nadat u de schaalbare WordPress-site hebt geïmplementeerd en een exemplaar van Redis-cache hebt ingericht, kunt u verder gaan en de Memcache-shim inschakelen in Azure App Service Web Apps.

## De Memcache-shim van Web Apps inschakelen

U moet u drie app-instellingen opgeven om de Memcache-shim te configureren. U kunt dit doen met behulp van een aantal methoden, waaronder de [klassieke portal] [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715)[3], de [Azure PowerShell-Cmdlets][5] of de [Azure-opdrachtregelinterface][5]. In het kader van dit artikel gebruik ik de [Azure Portal][4] om de app-instellingen op te geven. U kunt de volgende waarden vinden op de blade **Instellingen** van uw Redis-cache-exemplaar.

![Blade Instellingen van Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### App-instelling REDIS_HOST toevoegen

De eerste app-instelling die u moet opgeven, is de app-instelling **REDIS\_HOST**. Met deze instelling geeft u de bestemming op waarnaar de shim de cachegegevens doorstuurt. De waarde voor de app-instelling REDIS_HOST vindt u op de blade **Eigenschappen** van het Redis-cache-exemplaar.

![Hostnaam van Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Stel de sleutel van de app-instelling in op **REDIS\_HOST** en de waarde van de app-instelling op de **hostnaam** van het Redis-cache-exemplaar.

![App-instelling REDIS_HOST van web-app](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### App-instelling REDIS_KEY toevoegen

De tweede app-instelling die u moet opgeven, is de app-instelling **REDIS\_KEY**. Deze instelling verstrekt de verificatietoken die nodig is voor een veilige toegang tot het Redis-cache-exemplaar. U vindt de waarde voor de app-instelling REDIS_KEY op de blade **Toegangssleutels** van het Redis-cache-exemplaar.

![Primaire sleutel voor Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Stel de sleutel van de app-instelling in op **REDIS\_KEY** en de waarde van de app-instelling op de **Primaire sleutel** van het Redis-cache-exemplaar.

![App-instelling REDIS_KEY van Azure-website](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### App-instelling MEMCACHESHIM_REDIS_ENABLE toevoegen

De laatste app-instelling wordt gebruikt om de Memcache-shim in Web Apps in te schakelen. Hierbij worden REDIS_HOST en REDIS_KEY gebruikt om verbinding te maken met de Azure Redis-cache en de cacheaanroepen door te sturen. Stel de sleutel van de app-instelling in op **MEMCACHESHIM\_REDIS\_ENABLE** en de waarde op **waar**.

![App-instelling MEMCACHESHIM_REDIS_ENABLE van web-app](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

Wanneer u de drie (3) app-instellingen hebt toegevoegd, klikt u op **Opslaan**.

## Memcache-extensie voor PHP inschakelen

De Memcache-extensie moet worden geïnstalleerd op PHP, het taalframework voor uw WordPress-site, om ervoor te zorgen dat de toepassing met het Memcache-protocol kan werken.

### De extensie php_memcache downloaden

Blader naar [PECL][6]. Klik in de cachingcategorie op [memcache][7]. Klik in de kolom met downloads op de DDL-koppeling.

![PECL-website van PHP](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Download de koppeling Non-Thread Safe (NTS) x86 voor de versie van PHP die in Web Apps is ingeschakeld. (De standaardversie is PHP 5.4.)

![Memcache-pakket voor PHP PECL](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### De extensie php_memcache inschakelen

Nadat u het bestand hebt gedownload, kunt u het uitpakken en **php\_memcache.dll** uploaden naar de map **D:\\home\\site\\wwwroot\\bin\\ext\\**. Nadat php_memcache.dll is geüpload naar de web-app, moet u de extensie inschakelen voor de PHP-runtime. Als u de Memcache-extensie wilt inschakelen in de Azure Portal, opent u de blade **Toepassingsinstellingen** voor de web-app en voegt u een nieuwe app-instelling met de sleutel **PHP\_EXTENSIONS** en de waarde **bin\\ext\\php_memcache.dll** toe.


> [AZURE.NOTE] Als de web-app meerdere PHP-extensies moet laden, moet de waarde van PHP_EXTENSIONS een door komma's gescheiden lijst zijn van relatieve paden naar DLL-bestanden.

![App-instelling PHP_EXTENSIONS van web-app](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

Wanneer u klaar bent, klikt u op **Opslaan**.

## WordPress-invoegtoepassing voor Memcache installeren

> [AZURE.NOTE] U kunt ook de [invoegtoepassing Memcached Object Cache](https://wordpress.org/plugins/memcached/) downloaden via WordPress.org.

Klik op de pagina met WordPress-invoegtoepassingen op **Nieuwe toevoegen**.

![Pagina met WordPress-invoegtoepassingen](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

Typ **memcached** in het zoekvak en druk op **Enter**.

![Nieuwe WordPress-invoegtoepassing toevoegen](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Zoek **Memcached Object Cache** in de lijst en klik op **Nu installeren**.

![WordPress invoegtoepassing voor Memcache installeren](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### De WordPress-invoegtoepassing voor Memcache inschakelen

>[AZURE.NOTE] Volg de instructies in de blog [How to enable a Site Extension in Web Apps][8] (Een site-extensie inschakelen in Web Apps) om Visual Studio Team Services te installeren.

Voeg in het bestand `wp-config.php` de volgende code toe boven het commentaar 'stop editing', vlak bij het einde van het bestand.

```php
$memcached_servers = array(
    'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

Nadat deze code is geplakt, wordt het document automatisch in monaco opgeslagen.

De volgende stap is het inschakelen van de object-cache-invoegtoepassing. Daarvoor sleept u **object-cache.php** van de map **wp-content/plugins/memcached** naar de map **wp-content** om de functionaliteit van Memcache Object Cache in te schakelen.

![De memcache-invoegtoepassing object-cache.php zoeken](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

Als het bestand **object-cache.php** in de map **wp-content** staat, is Memcached Object Cache ingeschakeld.

![De Memcache-invoegtoepassing object-cache.php inschakelen](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## Controleren of de invoegtoepassing Memcache Object Cache werkt

Alle stappen om de Memcache-shim van Web Apps in te schakelen, zijn nu voltooid. U hoeft alleen nog te controleren of het Redis-cache-exemplaar wordt gevuld met de gegevens.

### Ondersteuning voor niet-SSL-poort inschakelen in Azure Redis-cache

>[AZURE.NOTE] Op het moment van publicatie van dit artikel biedt de Redis CLI geen ondersteuning voor SSL-verbindingen. Daarom zijn de volgende stappen noodzakelijk.

Blader in de Azure Portal naar het Redis-cache-exemplaar dat u voor deze web-app hebt gemaakt. Nadat de blade van de cache is geopend, klikt u op het pictogram **Instellingen**.

![Knop Instellingen van Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

Selecteer in de lijst de optie **Toegangspoorten**.

![Toegangspoort voor Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Klik op **Nee** voor **Alleen toegang met SSL-beveiliging toestaan**.

![Alleen toegang met SSL-beveiliging voor Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

U ziet dat de niet-SSL-poort nu is ingesteld. Klik op **Opslaan**.

![Toegangspoort zonder SSL voor Azure Redis-cache](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### Verbinding maken met Azure Redis-cache vanuit Redis CLI

>[AZURE.NOTE] Bij deze stap wordt ervan uitgegaan dat Redis lokaal is geïnstalleerd op uw ontwikkelcomputer. [Installeer Redis lokaal via deze instructies][9].

Open een willekeurige opdrachtregelconsole en typ de volgende opdracht:

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

Vervang **&lt;hostname-for-redis-cache&gt;** door de werkelijke hostnaam van xxxxx.redis.cache.windows.net en **&lt;primary-key-for-redis-cache&gt;** door de toegangssleutel voor de cache. Druk vervolgens op **Enter**. Zodra de CLI is verbonden met het Redis-cache-exemplaar, voert u een willekeurige Redis-opdracht uit. In onderstaande schermafbeelding heb ik ervoor gekozen om de sleutels weer te geven.

![Verbinding maken met Azure Redis-cache vanuit Redis CLI in Terminal](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

De aanroep naar de lijst met sleutels moet een waarde retourneren. Als dat niet het geval is, gaat u terug naar de web-app en probeert u het opnieuw.

## Conclusie

Gefeliciteerd! De WordPress-app heeft nu een gecentraliseerde geheugencache om de doorvoer te verbeteren. De Memcache-shim van Web Apps kan worden gebruikt met elke Memcache-client, ongeacht de programmeertaal of het toepassingsframework. Als u feedback wilt geven of een vraag wilt stellen over de Memcache-shim van Web Apps, plaatst u een bericht op de [MSDN-forums][10] of [Stackoverflow][11].

>[AZURE.NOTE] Als u aan de slag wilt met Azure App Service voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen](http://go.microsoft.com/fwlink/?LinkId=523751). Hier kunt u direct een tijdelijke web-app maken in App Service. U hebt geen creditcard nodig en u gaat geen verplichtingen aan.

## Wat is er gewijzigd
* Als u van Websites wilt overstappen op App Service, raadpleegt u de volgende handleiding: [Azure App Service en de invloed ervan op bestaande Azure Services](http://go.microsoft.com/fwlink/?LinkId=529714)


[0]: ../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: ../powershell-install-configure.md
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org



<!--HONumber=Jun16_HO2-->



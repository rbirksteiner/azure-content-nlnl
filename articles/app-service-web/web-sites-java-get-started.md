<properties
    pageTitle="Een Java-web-app maken in Azure App Service | Microsoft Azure"
    description="In deze zelfstudie ziet u hoe u een Java-web-app implementeert in Azure App Service"
    services="app-service\web"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="robmcm"/>

# Een Java web-app maken in Azure App Service

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

In deze zelfstudie ziet u hoe u met behulp van de [Azure Portal] een Java-[web-app maakt in Azure App Service]. De Azure Portal is een webinterface waarmee u Azure-resources kunt beheren.

> [AZURE.NOTE] U hebt een Microsoft Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u [uw voordelen als Visual Studio-abonnee activeren] of [zich aanmelden voor een gratis proefversie].
>
> Als u met Azure App Service aan de slag wilt voordat u zich aanmeldt voor een Azure-account, gaat u naar [App Service uitproberen]. Daar kunt u direct een tijdelijke en eenvoudige web-app maken in App Service: u hebt geen creditcard nodig en bent nergens toe verplicht.

## Opties voor Java-toepassingen

Er zijn verschillende manieren waarop u een Java-toepassing kunt instellen in een web-app van App Service. 

1. Maak een app en configureer **Toepassingsinstellingen**.

    App Service biedt verschillende versies voor Tomcat en Jetty, met standaardconfiguratie. Als de toepassing die u wilt hosten, met een van de ingebouwde versies werkt, is deze methode voor het instellen van een webcontainer het gemakkelijkste. De methode is ideaal als u slechts een WAR-bestand naar een webcontainer hoeft te uploaden. Voor deze methode maakt u een app in de Azure Portal en gaat u naar de blade **Toepassingsinstellingen** voor uw app om uw versie van Java en de gewenste Java-webcontainer te kiezen. Wanneer u deze methode gebruikt, wordt zowel Java als uw webcontainer uitgevoerd vanuit Programmabestanden. Met de andere methoden worden de webcontainer en mogelijk ook de JVM op uw schijf geplaatst. Als u dit model gebruikt, hebt u geen toegang om bestanden in dit gedeelte van het bestandssysteem te bewerken. Dit betekent dat u bepaalde acties niet kunt uitvoeren. Zo kunt u het bestand *server.xml* niet configureren en kunt u geen bibliotheekbestanden in de map */lib* plaatsen. Voor meer informatie raadpleegt u de sectie [Een Java-web-app maken en configureren](#appsettings) verderop in deze zelfstudie.
    
2. Een sjabloon gebruiken uit Azure Marketplace

    Azure Marketplace bevat sjablonen waarmee u automatisch Java-web-apps met Tomcat- of Jetty-webcontainers kunt maken en configureren. De webcontainers die door de sjablonen worden gemaakt, kunnen worden geconfigureerd. Voor meer informatie raadpleegt u de sectie [Een Java-sjabloon gebruiken uit Azure Marketplace](#marketplace) in deze zelfstudie.
  
3. Een app maken en handmatig de configuratiebestanden kopiëren en bewerken 

    Misschien wilt u een aangepaste Java-toepassing hosten die niet is geïmplementeerd in een webcontainer van App Service. Bijvoorbeeld:
    
    * Uw Java-toepassing vereist een versie van Tomcat of Jetty die niet rechtstreeks wordt ondersteund door App Service of wordt verstrekt in de galerie.
    * Uw Java-toepassing accepteert HTTP-aanvragen en wordt niet als een WAR geïmplementeerd in een bestaande webcontainer.
    * U wilt de webcontainer geheel zelf configureren. 
    * U wilt een versie van Java gebruiken die niet wordt ondersteund in App Service en u wilt deze zelf uploaden.

    In dergelijke gevallen kunt u een app maken met de Azure Portal en vervolgens de juiste runtime-bestanden handmatig opgeven. De bestanden tellen dan wel mee voor het opslagruimtequotom van uw App Service-plan. Voor meer informatie raadpleegt u [Een aangepaste Java-web-app uploaden naar Azure].

## <a name="portal"></a> Een Java-web-app maken en configureren

In deze sectie wordt beschreven hoe u een web-app maakt en deze voor Java configureert met de blade **Toepassingsinstellingen** van de portal.

1. Meld u aan bij de [Azure Portal].

2. Klik op **Nieuw > Web + Mobiel > Web-app**.

    ![Nieuwe web-app][newwebapp]

4. Typ in het vak **Web-app** een naam voor de web-app.

    Deze naam moet uniek zijn in het domein azurewebsites.net, omdat de URL van de web-app {naam}.azurewebsites.net wordt. Als de ingevoerde naam niet uniek is, ziet u een rood uitroepteken in het tekstvak.

5. Selecteer een **Resourcegroep** of maak een nieuwe.

    Zie [De Azure Portal gebruiken om Azure-resources te beheren] voor meer informatie over resourcegroepen.

6. Selecteer een **App Service-plan/-locatie** of maak een nieuw(e).

    Zie [Overzicht van Azure App Service-plannen] voor meer informatie over App Service-plannen.

7. Klik op **Maken**.

    ![Web-app maken][newwebapp2]
 
8. Klik op **Web-apps > {uw web-app}** wanneer de web-app is gemaakt.
 
    ![Web-app selecteren][selectwebapp]

9. Klik op de blade **Web-app** op **Instellingen**.

10. Klik op **Toepassingsinstellingen**.

11. Kies de gewenste **Java-versie**. 

12. Kies de gewenste **Secundaire Java-versie**. Als u **Nieuwste** selecteert, gebruikt de app de nieuwste secundaire versie die in App Service voor die primaire Java-versie beschikbaar is. Het item **Nieuwste** is alleen beschikbaar voor Java-apps die zijn gemaakt via **Toepassingsinstellingen**. Als u een Java-app maakt vanuit de galerie, moet u uw eigen container en JVM-wijzigingen beheren. 

12. Kies de gewenste **Webcontainer**. Als u een containernaam selecteert die begint met **Nieuwste**, zal uw app altijd de nieuwste versie blijven gebruiken van de primaire versie van de webcontainer die beschikbaar is in de App Service. 

    ![Webcontainerversies][versions]

13. Klik op **Opslaan**.

    Binnen enkele tellen wordt uw web-app op Java gebaseerd en geconfigureerd voor gebruik van de geselecteerde webcontainer.

14. Klik op **Web-apps > {uw nieuwe web-app}**.

15. Klik op de **URL** om naar de nieuwe site te bladeren.

    Op de webpagina wordt bevestigd dat u een op Java gebaseerde web-app hebt gemaakt.

## <a name="marketplace"></a> Een Java-sjabloon gebruiken uit Azure Marketplace

In deze sectie wordt beschreven hoe u Azure Marketplace gebruikt om een Java-web-app te maken. Dezelfde algemene werkwijze kan ook worden gebruikt om een op Java gebaseerde mobiele app of API-app te maken. 

1. Meld u aan bij de [Azure Portal]

2. Klik op **Nieuw > Marketplace**.

    ![Nieuwe Marketplace][newmarketplace]

3. Klik op **Web en mobiel**.

    Misschien moet u naar links scrollen om de blade **Marketplace** te zien, waar u **Web en mobiel** kunt selecteren.

4. Voer in het zoekvak de naam in van een Java-toepassingsserver, zoals **Apache Tomcat** of **Jetty**. Druk vervolgens op Enter.

5. Klik in de zoekresultaten op de Java-toepassingsserver.

    ![Mobiele app met Jetty][webmobilejetty]

6. Klik in de eerste blade van **Apache Tomcat** of **Jetty** op **Maken**.

    ![Blade in Jetty-portal][jettyblade]

7. Typ in de volgende **Apache Tomcat**- of **Jetty**-blade een naam voor de web-app in het vak **Web-app**.

    Deze naam moet uniek zijn in het domein azurewebsites.net, omdat de URL van de web-app {naam}.azurewebsites.net wordt. Als de ingevoerde naam niet uniek is, ziet u een rood uitroepteken in het tekstvak.

8. Selecteer een **Resourcegroep** of maak een nieuwe.

    Zie [De Azure Portal gebruiken om Azure-resources te beheren] voor meer informatie over resourcegroepen.

9. Selecteer een **App Service-plan/-locatie** of maak een nieuw(e).

    Zie [Overzicht van Azure App Service-plannen] voor meer informatie over App Service-plannen.

10. Klik op **Maken**.

    ![Maken in Jetty-portal][jettyportalcreate2]

    In korte tijd, meestal binnen een minuut, is de nieuwe web-app in Azure gemaakt.

11. Klik op **Web-apps > {uw nieuwe web-app}**.

12. Klik op de **URL** om naar de nieuwe site te bladeren.

    ![Jetty-URL][jettyurl]

    Tomcat wordt geleverd met een standaardset pagina's. Als u Tomcat hebt gekozen, ziet u een pagina zoals in het volgende voorbeeld.

    ![Web-app met Apache Tomcat][tomcat]

    Als u Jetty hebt gekozen, ziet u een pagina zoals in het volgende voorbeeld. Jetty heeft geen standaardset pagina's. Daarom wordt hier dezelfde JSP gebruikt die ook voor een lege Java-site wordt gebruikt.

    ![Web-app met Jetty][jetty]

Nu u de web-app met een app-container hebt gemaakt, gaat u naar de sectie [Volgende stappen](#next-steps) voor meer informatie over het uploaden van de toepassing naar de web-app.

## Volgende stappen

In dit stadium hebt u een werkende Java-toepassingsserver in uw web-app in Azure App Service. Als u uw eigen code in de web-app wilt implementeren, raadpleegt u [Een toepassing of webpagina toevoegen aan uw Java-web-app].

In het [Java Developer Center] vindt u meer informatie over het ontwikkelen van Java-toepassingen in Azure.

<!-- URL List -->

[Een toepassing of webpagina toevoegen aan uw Java-web-app]: ./web-sites-java-add-app.md
[Overzicht van Azure App Service-plannen]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Azure Portal]: https://portal.azure.com/
[uw voordelen als Visual Studio-abonnee activeren]: http://go.microsoft.com/fwlink/?LinkId=623901
[aanmelden voor een gratis proefversie]: http://go.microsoft.com/fwlink/?LinkId=623901
[App Service uitproberen]: http://go.microsoft.com/fwlink/?LinkId=523751
[web-app maakt in Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Java Developer Center]: /develop/java/
[De Azure Portal gebruiken om Azure-resources te beheren]: ../azure-portal/resource-group-portal.md
[Een aangepaste Java-web-app uploaden naar Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versies]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png



<!--HONumber=Jun16_HO2-->



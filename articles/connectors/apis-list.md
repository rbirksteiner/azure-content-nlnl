<properties
    pageTitle="Lijst van door Microsoft beheerde connectors voor gebruik in Microsoft Azure Logic Apps | Microsoft Azure App Service"
    description="Een volledige lijst van de door Microsoft beheerde connectors die u kunt gebruiken voor het bouwen van logische apps in Azure App Service"
    services="app-service\logic"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/31/2016"
    ms.author="deonhe"/>

# Lijst met beheerde connectors

>[AZURE.NOTE] Deze versie van het artikel is van toepassing op de preview-schemaversie 2015-08-01 van logische apps. Klik voor de preview-schemaversie 2014-12-01 op [Lijst met connectors](../app-service-logic/app-service-logic-connectors-list.md). 

Zie [Prijzen van Azure App Service](https://azure.microsoft.com/pricing/details/app-service/) voor informatie over prijzen en een overzicht van wat is opgenomen in elke servicecategorie.

> [AZURE.NOTE] Als u aan de slag wilt met Azure Logic Apps en u zich nog niet hebt aangemeld voor een Azure-account, gaat u naar [Logic App uitproberen](https://tryappservice.azure.com/?appservice=logic). U kunt onmiddellijk een tijdelijke logische app in App Service maken. U hebt geen creditcard nodig en u gaat geen verplichtingen aan.

Selecteer een pictogram voor informatie over hoe u snel gebruik kunt maken van deze connectors voor het bouwen van apps die deze services aanroepen. Deze connectors kunnen worden gebruikt voor het bouwen van Logic Apps, PowerApps en flows.

|Connectors||||
|-----------|-----------|-----------|-----------|
|[![API Icon][blobicon]<br/>**Azure Blob**][azureblobdoc]|[![API Icon][boxicon]<br/>**Box**][boxDoc]|[![API Icon][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|[![API Icon][dropboxicon]<br/>**Dropbox**][dropboxdoc]|
|[![API Icon][facebookicon]<br/>**Facebook**][facebookdoc]|[![API Icon][ftpicon]<br/>**FTP**][ftpdoc]|[![API Icon][githubicon]<br/>**GitHub**][githubdoc]|[![API Icon][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![API Icon][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![API Icon][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![API Icon][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![API Icon][office365icon]<br/>**Office 365**<br/>**Users**][office365usersdoc]|
|[![API Icon][office365icon]<br/>**Office 365**<br/>**Video**][office365videodoc]|[![API Icon][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![API Icon][onedriveicon]<br/>**OneDrive<br/>voor Bedrijven**][onedriveforbusinessdoc]|[![API Icon][outlookicon]<br/>**Outlook**][outlookdoc]|
|[![API Icon][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![API Icon][rssicon]<br/>**RSS**][rssdoc]|[![API Icon][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![API Icon][sendgridicon]<br/>**SendGrid**][sendgriddoc]|
|[![API Icon][servicebusicon]<br/>**Service Bus**][servicebusdoc]|[![API Icon][sftpicon]<br/>**SFTP**][sftpdoc]|[![API Icon][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![API Icon][slackicon]<br/>**Slack**<br/>][slackdoc]|
|[![API Icon][smtpicon]<br/>**SMTP**][smtpdoc]|[![API Icon][sqlicon]<br/>**SQL Azure**][sqldoc]|[![API Icon][trelloicon]<br/>**Trello**][trellodoc]|[![API Icon][twilioicon]<br/>**Twilio**][twiliodoc]|
|[![API Icon][twittericon]<br/>**Twitter**][twitterdoc]|[![API Icon][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![API Icon][yammericon]<br/>**Yammer**][yammerdoc] | |

> [AZURE.NOTE] Als u logische apps met het preview-schema 2014-12-01 hebt gemaakt, zult u merken dat de integratieconnectors voor ondernemingen, zoals voor BizTalk, niet hierboven worden vermeld. We weten dat deze belangrijk zijn en we werken er hard aan om ze snel beschikbaar te stellen. Op dit moment weten we geen exacte beschikbaarheidsdatum, maar u kunt erop vertrouwen dat dit bij ons een zeer hoge prioriteit heeft. U kunt in de tussentijd toegang krijgen tot uw [v1 API's en BizTalk API's vanuit Logic Apps](https://blogs.msdn.microsoft.com/logicapps/2016/02/25/accessing-v1-apis-and-biztalk-apis-from-logic-apps/). Bedankt voor uw begrip. Kom regelmatig terug voor het laatste nieuws.


### Connectors kunnen triggers zijn
Meerdere connectors bieden triggers die aan uw app kunnen doorgeven dat er specifieke gebeurtenissen plaatsvinden. De FTP-connector heeft bijvoorbeeld de trigger OnUpdatedFile. U kunt een logische app, PowerApp of flow opbouwen die naar deze trigger luistert en actie onderneemt wanneer de trigger wordt geactiveerd.

Er zijn twee soorten triggers:  

* Poll-triggers: Met deze triggers wordt uw service met een opgegeven frequentie gepeild om te controleren of er nieuwe gegevens zijn. Als er nieuwe gegevens beschikbaar zijn, wordt een nieuw exemplaar van uw app uitgevoerd met de gegevens als invoer. Om te voorkomen dat dezelfde gegevens meerdere keren wordt gebruikt, kunnen gegevens worden opgeschoond die zijn gelezen en aan uw app zijn doorgegeven.
* Push-triggers: Met deze triggers wordt geluisterd naar gegevens op een eindpunt of wordt geluisterd of een gebeurtenis plaatsvindt. Vervolgens wordt een nieuw exemplaar van uw app geactiveerd. De twitter-connector is hier een voorbeeld van.


### Connectors kunnen acties zijn
Connectors kunnen ook worden gebruikt als acties in uw apps. Acties zijn handig voor het opzoeken van gegevens die vervolgens in de uitvoering van uw app kunnen worden gebruikt. U kunt tijdens het afhandelen van een order bijvoorbeeld klantgegevens in een SQL-database opzoeken. Of u wilt gegevens in een doeltabel schrijven, bijwerken of verwijderen. U kunt dit doen met de acties die met behulp van de connectors worden opgegeven. Acties zijn toegewezen aan de bewerkingen die zijn gedefinieerd in de Swagger-metagegevens.


[Wat is er nieuw](../app-service-logic/app-service-logic-schema-2015-08-01.md)  
[Nu een logische app bouwen](../app-service-logic/app-service-logic-create-a-logic-app.md)  
[Nu aan de slag met PowerApps](../power-apps/powerapps-get-started-azure-portal.md)  
[Logische apps migreren naar de nieuwste schemaversie.](connectors-schema-migration.md) 

<!--Connectors Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Maak verbinding met Azure-blob voor het beheren van bestanden in uw blobcontainer."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Gebruik Bing voor het zoeken op het web naar onder meer afbeeldingen, nieuws en video’s."
[boxDoc]: ./connectors-create-api-box.md "Maak verbinding met Box voor het uploaden, ophalen, verwijderen, opsommen en meer bestandstaken."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Maak verbinding met Dynamics CRM Online en doe meer met uw online-CRM-gegevens."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Maak verbinding met Dropbox voor het ophalen, verwijderen, opsommen en meer bestandstaken."
[exceldoc]: ./connectors-create-api-excel.md "Maak verbinding met Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Maak verbinding met Facebook om iets op een tijdlijn te plaatsen, een paginafeed te krijgen, en meer."
[ftpdoc]: ./connectors-create-api-ftp.md "Maak verbinding met een FTP-/FTPS-server en voer verschillende FTP-taken uit, waaronder het uploaden, ophalen en verwijderen van bestanden."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Maak verbinding met GoogleDrive en werk met uw gegevens."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "Met de Office 365-connector kunt u e-mails verzenden en ontvangen, uw agenda beheren en uw contactpersonen beheren met uw Office 365-account."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Maak verbinding met uw persoonlijke Microsoft OneDrive om bestanden te uploaden, te verwijderen, weer te geven en meer."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Maak verbinding met uw zakelijke Microsoft OneDrive om bestanden te uploaden, te verwijderen, weer te geven en meer."
[outlookdoc]: ./connectors-create-api-outlook.md "Maak verbinding maken met uw Outlook-postvak voor toegang tot uw e-mail en meer."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Maak verbinding met Microsoft Project Online."
[rssdoc]: ./connectors-create-api-rss.md "RSS-connector biedt gebruikers de mogelijkheid om feeditems te publiceren en op te halen. Gebruikers kunnen ook bewerkingen activeren wanneer een nieuw item naar de feed wordt gepubliceerd."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Maak verbinding maken met uw Salesforce-account en beheer uw accounts, potentiële klanten, verkoopkansen en meer."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Maak verbinding met Microsoft Project Online."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Hiermee kunnen berichten vanuit de Service Bus-wachtrijen en -onderwerpen worden verzonden en kunnen berichten vanuit de Service Bus-wachtrijen en -abonnementen worden ontvangen."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Maak verbinding met SharePoint Online om documenten en lijstitems te beheren."
[slackdoc]: ./connectors-create-api-slack.md "Maak verbinding met Slack en plaats berichten in Slack-kanalen."
[sftpdoc]: ./connectors-create-api-sftp.md "Maak verbinding met SFTP voor het uploaden, ophalen en verwijderen van bestanden en meer."
[githubdoc]: ./connectors-create-api-github.md "Maak verbinding met GitHub en kan problemen bijhouden."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Verzend betere e-mail."
[smtpdoc]: ./connectors-create-api-smtp.md "Hiermee wordt verbinding gemaakt met een SMTP-server en kunnen e-mail met bijlagen worden verzonden."
[sqldoc]: ./connectors-create-api-sqlazure.md "Maak verbinding met SQL Azure Database. U kunt items in een SQL-databasetabel maken, bijwerken, ophalen en verwijderen."
[trellodoc]: ./connectors-create-api-trello.md "Trello is de gratis, flexibele en visuele manier om iets te organiseren met wie dan ook."
[twiliodoc]: ./connectors-create-api-twilio.md "Hiermee wordt verbinding gemaakt met Twilio en kunnen berichten worden verzonden en opgehaald, beschikbare nummers worden opgehaald, binnenkomende telefoonnummers worden beheerd, en meer."
[twitterdoc]: ./connectors-create-api-twitter.md "Maak verbinding met Twitter om tijdlijnen op te halen, tweets te posten en meer."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Zorg dat alles synchroon blijft."
[yammerdoc]: ./connectors-create-api-yammer.md "Maak verbinding met Yammer om berichten te plaatsen en nieuwe berichten op te halen."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png



<!--HONumber=Jun16_HO2-->



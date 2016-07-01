<properties
    pageTitle="Een Java API-app bouwen en implementeren in Azure App Service"
    description="Lees meer over het maken van een Java API-app-pakket en de implementatie ervan in Azure App Service."
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="bradygaster"/>

# Een Java API-app bouwen en implementeren in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

In deze zelfstudie ziet u hoe u met behulp van [Git] een Java-toepassing maakt en implementeert in Azure App Service. De instructies in deze zelfstudie kunnen worden uitgevoerd in elk besturingssysteem waarmee Java kan worden uitgevoerd. De code in deze zelfstudie wordt gebouwd met [Maven]. [Jax-RS] wordt gebruikt voor het maken van de RESTful-service en wordt met behulp van [Swagger Editor] gegenereerd op basis van de [Swagger]-metagegevensspecificatie.

## Vereisten

1. [Java Development Kit 8] (of hoger)
1. [Maven] is geïnstalleerd op uw ontwikkelcomputer
1. [Git] is geïnstalleerd op uw ontwikkelcomputer
1. Een betaald abonnement of een [gratis] proefabonnement op [Microsoft Azure]
1. Een HTTP-testtoepassing zoals [Postman]

## Ondersteuning voor de API met behulp van Swagger.IO

Met de online-editor Swagger.io kunt u Swagger JSON- of YAML-code invoeren die de structuur van uw API aangeeft. Zodra u de 'surface area' van de API hebt ontworpen, kunt u de code voor verschillende platforms en frameworks exporteren. In de volgende sectie gaat u de ondersteunde code wijzigen, zodat deze de mock-functionaliteit bevat. 

In deze demonstratie begint u met Swagger JSON-hoofdtekst die u in de editor Swagger.io plakt. Op basis van deze tekst wordt de code gegenereerd, waarbij gebruik wordt gemaakt van JAX RS voor toegang tot een REST API-eindpunt. Vervolgens bewerkt u de ondersteunde code zodat hiermee mock-gegevens worden geretourneerd, waarbij een REST API wordt gesimuleerd die boven op een mechanisme voor gegevenspersistentie wordt gebouwd.  

1. Kopieer de volgende Swagger JSON-code naar het Klembord:

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Navigeer naar [Online Swagger Editor]. Klik in de editor op de menuopdracht **File -> Paste JSON**.

    ![Menuopdracht Paste JSON][paste-json]

1. Plak de Swagger JSON-code voor de API voor de contactpersonenlijst die u eerder hebt gekopieerd. 

    ![JSON-code plakken in Swagger][pasted-swagger]

1. Bekijk de documentatie en het API-overzicht die in de editor worden weergegeven. 

    ![Door Swagger gegenereerde documenten weergeven][view-swagger-generated-docs]

1. Selecteer de menuoptie **Generate Server -> JAX-RS** voor ondersteuning van de code op de server die u later gaat bewerken om mock-implementatie toe te voegen. 

    ![Menuopdracht Generate Code][generate-code-menu-item]

    Als de code is gegenereerd, wordt een zip-bestand weergegeven dat u kunt downloaden. Dit bestand bevat de code die door de Swagger-codegenerator wordt ondersteund, en alle gekoppelde bouwscripts. Pak de hele bibliotheek uit naar een map op uw ontwikkelwerkstation. 

## De code bewerken om API-implementatie toe te voegen

In deze sectie vervangt u de serverimplementatie van de met Swagger gegenereerde code door uw aangepaste code. Met de nieuwe code wordt een matrixlijst met contactpersoonsentiteiten naar de aanroepende client geretourneerd. 

1. Open het modelbestand *Contact.java* (opgeslagen in de map *src/gen/java/io/swagger/model*) met behulp van [Visual Studio Code] of uw favoriete teksteditor. 

    ![Modelbestand voor contactpersonen openen][open-contact-model-file]

1. Voeg de volgende constructor toe aan de klasse **Contact**. 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Open het service-implementatiebestand *ContactsApiServiceImpl.java* (opgeslagen in de map *src/main/java/io/swagger/api/impl*) met behulp van [Visual Studio Code] of uw favoriete teksteditor.

    ![Servicecodebestand voor contactpersonen openen][open-contact-service-code-file]

1. Overschrijf de code in het bestand door deze nieuwe code om een mock-implementatie aan de servicecode toe te voegen. 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Open een opdrachtprompt en wijzig de map in de hoofdmap van uw toepassing.

1. Voer de volgende Maven-opdracht uit om de code te bouwen en lokaal uit te voeren met behulp van de Jetty-app-server. 

        mvn package jetty:run

1. In het opdrachtvenster ziet u dat uw code met Jetty op poort 8080 is gestart. 

    ![Servicecodebestand voor contactpersonen openen][run-jetty-war]

1. Gebruik [Postman] om de API-methode voor het ophalen van alle contactpersonen aan te vragen via http://localhost:8080/api/contacts.

    ![De API voor contactpersonen aanroepen][calling-contacts-api]

1. Gebruik [Postman] om de API-methode voor het ophalen van een specifieke contactpersoon aan te vragen via http://localhost:8080/api/contacts/2.

    ![De API voor contactpersonen aanroepen][calling-specific-contact-api]

1. Bouw ten slotte het Java WAR-bestand (Web ARchive) door de volgende Maven-opdracht in de console uit te voeren. 

        mvn package war:war

1. Als het WAR-bestand is gebouwd, wordt dit in de **doel**map geplaatst. Navigeer naar de **doel**map en wijzig de naam van het WAR-bestand in **ROOT.war**. (Zorg ervoor dat het hoofdlettergebruik overeenkomt met deze indeling.)

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Voer ten slotte de volgende opdrachten vanuit de hoofdmap van uw toepassing uit om een **implementatie**map te maken waarmee u het WAR-bestand in Azure implementeert. 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## De uitvoer publiceren naar Azure App Service

In deze sectie vindt u meer informatie over het maken van een nieuwe API-app met behulp van Azure Portal, het voorbereiden van die API-app voor het hosten van Java-toepassingen en het implementeren van het nieuwe WAR-bestand in Azure App Service, zodat de nieuwe API-app kan worden uitgevoerd. 

1. Maak een nieuwe API-app in [Azure Portal]. Hiervoor klikt u op de menuopdracht **Nieuw -> Web en mobiel -> API-app**, voert u de app-details in en klikt u vervolgens op **Maken**.

    ![Een nieuwe API-app maken][create-api-app]

1. Als de API-app is gemaakt, opent u de blade **Instellingen** van de app en klikt u vervolgens op de menuopdracht **Toepassingsinstellingen**. Selecteer de meest recente Java-versies en selecteer de meest recente Tomcat-versie in het menu **Webcontainer**. Klik vervolgens op **Opslaan**.

    ![Java instellen in de blade API-app][set-up-java]

1. Klik op de opdracht **Referenties voor implementatie** in het menu Instellingen en geef de gebruikersnaam en het wachtwoord op die u wilt gebruiken voor het publiceren van bestanden naar uw API-app. 

    ![Implementatiereferenties instellen][deployment-credentials]

1. Klik op de opdracht **Implementatiebron** in het menu Instellingen. Klik vervolgens op de knop **Bron kiezen**, selecteer de optie **Lokale Git-opslagplaats** en klik vervolgens op **OK**. Hiermee wordt een Git-opslagplaats gemaakt die in Azure wordt uitgevoerd, met een koppeling naar uw API-app. Telkens wanneer u code doorvoert naar de *hoofd*vertakking van de Git-opslagplaats, wordt de code gepubliceerd naar uw live API-app-exemplaar dat wordt uitgevoerd. 

    ![Een nieuwe lokale Git-opslagplaats instellen][select-git-repo]

1. Kopieer de URL van de nieuwe Git-opslagplaats naar het Klembord. Sla de URL op, u hebt deze straks nodig. 

    ![Een nieuwe Git-opslagplaats voor uw app instellen][copy-git-repo-url]

1. Push het WAR-bestand met Git naar de onlineopslagplaats. Hiervoor gaat u naar de **implementatie**map die u eerder hebt gemaakt, zodat u de code eenvoudig kunt doorvoeren naar de opslagplaats die wordt uitgevoerd in App Service. Als u in het consolevenster bent en zich in de map bevindt waarin de map met de web-apps is opgeslagen, voert u de volgende Git-opdrachten uit om het proces uit te voeren en een implementatie te starten. 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Als u de **push**aanvraag hebt uitgevoerd, wordt u gevraagd het wachtwoord op te geven dat u eerder voor de implementatiereferentie hebt gemaakt. Nadat u de referenties hebt ingevoerd, ziet u in de portal dat de update is geïmplementeerd.

1. Als u Postman nogmaals gebruikt om de zojuist geïmplementeerde API-app aan te vragen die in Azure App Service wordt uitgevoerd, ziet u dat het gedrag consistent is, dat met de app nu contactpersoonsgegevens op de verwachte manier worden geretourneerd en dat gebruik wordt gemaakt van eenvoudige codewijzigingen in de ondersteunde Java-code met Swagger.io. 

    ![Java REST API voor contactpersonen live in Azure gebruiken][postman-calling-azure-contacts]

## Volgende stappen

In dit artikel bent u met een Swagger JSON-bestand begonnen en hebt u wat ondersteunde Java-code opgehaald uit de editor Swagger.io. Van daaruit hebben eenvoudige wijzigingen en een Git-implementatieproces geleid tot een functionele API-app, geschreven in Java. In de volgende zelfstudie ziet u hoe u [API-apps met behulp van CORS kunt gebruiken vanuit JavaScript-clients][CORS voor API in App Service]. In latere zelfstudies in de reeks ziet u hoe u verificatie en autorisatie kunt implementeren.

Om voort te bouwen op dit voorbeeld, kunt u meer lezen over de [opslag-SDK voor Java] voor het persistent maken van de JSON-blobs. U kunt ook gebruikmaken van [Java SDK voor DocumentDB] om uw contactpersoonsgegevens op te slaan in Azure DocumentDB. 

In het [Java Developer Center] vindt u meer informatie over het gebruik van Java in Azure.

<!-- URL List -->

[CORS voor API in App Service]: app-service-api-cors-consume-javascript.md
[Azure Portal]: https://portal.azure.com/
[Java SDK voor DocumentDB]: ../documentdb/documentdb-java-application.md
[gratis]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Java Developer Center]: /develop/java/
[Java Development Kit 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax-RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Online Swagger Editor]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[opslag-SDK voor Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Swagger Editor]: http://editor.swagger.io/
[Visual Studio Code]: https://code.visualstudio.com

<!-- IMG List -->

[plakken-json]: ./media/app-service-api-java-api-app/paste-json.png
[geplakt-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[swagger-gegenereerde-documenten-weergeven]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[menuopdracht-generate-code]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[modelbestand-contactpersonen-openen]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[servicecodebestand-contactpersonen-openen]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[jetty-war-uitvoeren]: ./media/app-service-api-java-api-app/run-jetty-war.png
[contactpersonen-api-aanroepen]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[specifieke-contactpersonen-api-aanroepen]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[api-app-maken]: ./media/app-service-api-java-api-app/create-api-app.png
[java-installeren]: ./media/app-service-api-java-api-app/set-up-java.png
[implementatie-referenties]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-repo-selecteren]: ./media/app-service-api-java-api-app/select-git-repo.png
[git-repo-url-kopiëren]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-aanroepen-azure-contactpersonen]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png



<!--HONumber=Jun16_HO2-->



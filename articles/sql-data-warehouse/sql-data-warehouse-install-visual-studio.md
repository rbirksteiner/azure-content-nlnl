<properties
   pageTitle="Visual Studio en SSDT voor SQL Data Warehouse installeren | Microsoft Azure"
   description="Visual Studio en SQL Server Development Tools (SSDT) voor Azure SQL Data Warehouse installeren"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="sonyama;barbkess"/>

# Visual Studio 2015 en SSDT voor SQL Data Warehouse installeren

Als u toepassingen voor SQL Data Warehouse wilt gaan ontwikkelen, raden we het gebruik aan van Visual Studio 2015 met de meest recente versie van SQL Server Data Tools (SSDT).  Visual Studio 2013 met SSDT wordt ook ondersteund voor compatibiliteit met eerdere versies.  

Wanneer u Visual Studio met SSDT gebruikt, kunt u de SQL Server-objectverkenner gebruiken om tabellen, weergaven, opgeslagen procedures en nog veel meer objecten in de SQL Data Warehouse visueel te verkennen, en kunt u query's uitvoeren.

> [AZURE.NOTE] SQL Data Warehouse biedt nog geen ondersteuning voor Visual Studio-Database-projecten.  Deze functie wordt in een toekomstige versie toegevoegd.

## Stap 1: Visual Studio 2015 installeren

Volg deze koppelingen om Visual Studio 2015 te downloaden en te installeren. Als u Visual Studio 2013 of 2015 al hebt geïnstalleerd, kunt u doorgaan met stap 2, SSDT installeren.

1. [Download Visual Studio 2015][].
2. Volg de instructies in [Installing Visual Studio][] (Visual Studio installeren) op MSDN en kies de standaardconfiguraties.

## Stap 2: SSDT installeren

Als u SSDT voor Visual Studio wilt installeren, voert u de volgende stappen uit om in Visual Studio op een SSDT-update te controleren.

1. Klik in Visual Studio op **Tools** / **Extensions and Updates…** / **Updates** (Extra > Extensies en updates…> Updates)
2. Selecteer **Product Updates** (Productupdates) en zoek naar **Microsoft SQL Server Update for database tooling** (Microsoft SQL Server Update voor databasehulpprogramma's)

Als er geen update wordt gevonden, hebt u de meest recente versie al geïnstalleerd.  Als u wilt controleren of SSDT is geïnstalleerd, klikt u op **Help** / **About Microsoft Visual Studio** (Help > Info) en zoekt u in de lijst naar SQL Server Data Tools.  De meest recente versie van SSDT is 14.0.60525.0.  Als de installatieoptie niet beschikbaar is via Visual Studio, kunt u ook naar de pagina [Downloadpagina van SSDT][] gaan en SSDT handmatig installeren.

## Volgende stappen

Nu u de nieuwste versie van SSDT hebt, kunt u [verbinding maken][] met uw SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[verbinding maken]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Download Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Downloadpagina van SSDT]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=Jun16_HO2-->



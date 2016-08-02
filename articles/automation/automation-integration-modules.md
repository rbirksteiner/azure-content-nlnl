<properties
   pageTitle="Een Azure Automation-integratiemodule maken | Microsoft Azure"
   description="Zelfstudie die u helpt bij het maken, testen en bij het voorbeeldgebruik van integratiemodules in Azure Automation."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="" />

<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/24/2016"
   ms.author="magoedte" />

# Azure Automation-integratiemodules

PowerShell is de basistechnologie achter Azure Automation. Omdat Azure Automation is gebouwd op PowerShell, zijn de PowerShell-modules bepalend voor de uitbreidbaarheid van Azure Automation. In dit artikel worden de details van het gebruik van Azure Automation van PowerShell-modules, die 'integratiemodules' worden genoemd, besproken en de aanbevolen procedures voor het maken van uw eigen PowerShell-modules om er zeker van te zijn dat ze werken als integratiemodules binnen Azure Automation. 

## Wat is een PowerShell-module?

Een PowerShell-module is een groep PowerShell-cmdlets, zoals **Get-Date** of **Copy-Item**, die kunnen worden gebruikt vanuit de PowerShell-console, scripts, werkstromen, runbooks en PowerShell DSC-resources, zoals WindowsFeature of File, die kunnen worden gebruikt vanuit PowerShell DSC-configuraties. Alle functionaliteit van PowerShell is zichtbaar via cmdlets en DSC-resources, en elke cmdlet/DSC-resource wordt ondersteund door een PowerShell-module. Veel van deze modules worden bij PowerShell zelf geleverd. De cmdlet **Get-Date** maakt bijvoorbeeld deel uit van de PowerShell-module Microsoft.PowerShell.Utility en de cmdlet **Copy-Item** maakt deel uit van de PowerShell-module Microsoft.PowerShell.Management en de Package DSC-resource maakt deel uit van de PowerShell-module PSDesiredStateConfiguration. Deze modules worden geleverd met PowerShell. Maar veel PowerShell-modules worden niet als onderdeel van PowerShell geleverd en worden in plaats daarvan gedistribueerd met producten van Microsoft of van derden zoals System Center 2012 Configuration Manager of door de enorme PowerShell-community op locaties zoals PowerShell Gallery.  De modules zijn nuttig omdat ze complexe taken eenvoudiger maken via ingekapselde functionaliteit.  Meer informatie over [PowerShell-modules vindt u op MSDN](https://msdn.microsoft.com/library/dd878324%28v=vs.85%29.aspx). 

## Wat is een Azure Automation-integratiemodule?

Een integratiemodule verschilt niet heel veel van een PowerShell-module. Het is gewoon een PowerShell-module die desgewenst één extra bestand bevat: een metagegevensbestand waarin een Azure Automation-verbindingstype wordt opgegeven dat moet worden gebruikt met de cmdlets van de module in runbooks. Optioneel bestand of niet, deze PowerShell-modules kunnen in Azure Automation worden geïmporteerd om de bijbehorende cmdlets beschikbaar te maken voor gebruik in runbooks en de bijbehorende DSC-resources beschikbaar te maken voor gebruik in DSC-configuraties. Achter de schermen worden deze modules in Azure Automation opgeslagen en bij de uitvoeringstijd van runbooktaken en DSC-compilatietaken worden ze in de Azure Automation-sandboxes geladen waar runbooks worden uitgevoerd en DSC-configuraties worden gecompileerd.  Eventuele DSC-resources in modules worden ook automatisch op de Automation DSC-pull-server geplaatst, zodat ze kunnen worden opgehaald door machines waarmee wordt geprobeerd DSC-configuraties toe te passen.  Een aantal Azure PowerShell-modules worden gebruiksklaar in Azure Automation geleverd zodat u meteen aan de slag kunt gaan met het automatiseren van Azure-beheer, maar u kunt eenvoudig PowerShell-modules importeren voor elk systeem, elke service of elk hulpprogramma waarmee u wilt integreren. 

De indeling waarin u een integratiemodulepakket importeert, is een gecomprimeerd bestand met dezelfde naam als de module en met een ZIP-extensie. Het bevat de Windows PowerShell-module en ondersteunende bestanden, inclusief een manifestbestand (.psd1) als de module die een heeft.

Als de module een Azure Automation-verbindingstype moet bevatten, moet deze ook een bestand bevatten met de naam *<ModuleName>*-Automation.json waarin de eigenschappen van het verbindingstype zijn opgegeven. Dit is een JSON-bestand dat in de modulemap van uw gecomprimeerde ZIP-bestand is geplaatst. Het bevat de velden van een 'verbinding' die is vereist om verbinding te maken met het systeem of de service die door de module wordt aangegeven. Uiteindelijk wordt hiermee een verbindingstype gemaakt in Azure Automation. Met dit veld kunt u de veldnamen en typen instellen en opgeven of de velden moeten worden versleuteld en/of optioneel moeten zijn, voor het verbindingstype van de module. Hier volgt een sjabloon in de JSON-bestandsindeling:

```
{ 
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  false,
      "Name":  "ComputerName",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
   "TypeName":  "System.String"
   }],
   "ConnectionTypeName":  "DataProtectionManager",
   "IntegrationModuleName":  "DataProtectionManager"
}
```

Als u Service Management Automation hebt geïmplementeerd en integratiemodulepakketten hebt gemaakt voor uw Automation-runbooks, zou deze er bekend moeten uitzien. 


## Aanbevolen procedures voor ontwerpen

Hoewel integratiemodules in feite PowerShell-modules zijn, wil dit niet zeggen dat we geen set procedures hebben voor het ontwerpen hiervan. Er is toch een aantal zaken waar u aan moet denken bij het ontwerpen van een PowerShell-module, om deze zo bruikbaar mogelijk te maken in Azure Automation. Sommige hiervan zijn specifiek voor Azure Automation en sommige zijn nuttig om uw modules goed te laten werken in PowerShell Workflow, ongeacht of u Automation gebruikt. 

1. Neem een samenvatting, beschrijving en Help-URI op voor elke cmdlet in de module. In PowerShell kunt u bepaalde Help-informatie voor cmdlets definiëren, zodat de gebruiker hulp ontvangt bij het gebruik van cmdlets met de cmdlet **Get-Help**. U kunt bijvoorbeeld als volgt een samenvatting en Help-URI definiëren voor een PowerShell-module, geschreven in een PSM1-bestand.<br>  

    ```
    <#
        .SYNOPSIS
         Gets all outgoing phone numbers for this Twilio account 
    #>
    function Get-TwilioPhoneNumbers {
    [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
    HelpUri='http://www.twilio.com/docs/api/rest/outgoing-caller-ids')]
    param(
       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AccountSid,

       [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [string]
       $AuthToken,

       [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
       [ValidateNotNullOrEmpty()]
       [Hashtable]
       $Connection
    )

    $cred = CreateTwilioCredential -Connection $Connection -AccountSid $AccountSid -AuthToken $AuthToken

    $uri = "$TWILIO_BASE_URL/Accounts/" + $cred.UserName + "/IncomingPhoneNumbers"
    
    $response = Invoke-RestMethod -Method Get -Uri $uri -Credential $cred

    $response.TwilioResponse.IncomingPhoneNumbers.IncomingPhoneNumber
    }
    ```
<br> 
  Als u deze informatie opgeeft, wordt deze Help-informatie niet alleen getoond met de cmdlet **Get-Help** in de PowerShell-console, maar wordt deze Help-functionaliteit ook weergegeven in Azure Automation, bijvoorbeeld wanneer u activiteiten invoegt tijdens het ontwerpen van een runbook. Als u op Gedetailleerde Help-informatie weergeven klikt, wordt de Help-URI op een ander tabblad van de webbrowser geopend die u gebruikt voor toegang tot Azure Automation.<br>![Help bij integratiemodule](media/automation-integration-modules/automation-integration-module-activitydesc.png)
2. Als de module op een extern systeem wordt uitgevoerd, geldt het volgende: a. Deze moet een metagegevensbestand voor de integratiemodule bevatten waarin de informatie wordt gedefinieerd die nodig is om verbinding te maken met dat externe systeem, dus het verbindingstype. b. Elke cmdlet in de module moet een verbindingsobject (een exemplaar van dat verbindingstype) kunnen bevatten als een parameter.  
    Cmdlets in de module worden eenvoudiger te gebruiken in Azure Automation als u toestaat dat een object met de velden van het verbindingstype als een parameter aan de cmdlet wordt doorgegeven. Op deze manier hoeven gebruikers parameters van de verbindingsasset niet toe te wijzen aan de overeenkomende parameters van de cmdlet, elke keer dat ze een cmdlet aanroepen. Op basis van het bovenstaande runbookvoorbeeld wordt een Twilio-verbindingsasset genaamd CorpTwilio gebruikt voor toegang tot Twilio en voor het retourneren van alle telefoonnummers in het account.  Ziet u hoe hiermee de velden van de verbinding worden toegewezen aan de parameters van de cmdlet?<br>

    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'
    
      Get-TwilioPhoneNumbers 
        -AccountSid $CorpTwilio.AccountSid  
        -AuthToken $CorptTwilio.AuthToken
    }
    ```
<br>
    Een eenvoudigere en betere manier om dit te doen is het rechtstreeks doorgeven van het verbindingsobject aan de cmdlet -

    ```
    workflow Get-CorpTwilioPhones
    {
      $CorpTwilio = Get-AutomationConnection -Name 'CorpTwilio'

      Get-TwilioPhoneNumbers -Connection $CorpTwilio
    }
    ```
<br>
    U kunt dergelijk gedrag mogelijk maken voor uw cmdlets door deze toe te staan een verbindingsobject rechtstreeks als parameter te accepteren, in plaats van alleen verbindingsvelden voor parameters. Doorgaans wilt u voor elk een parameterset, zodat een gebruiker die Azure Automation niet gebruikt, uw cmdlets kan aanroepen zonder een hash-tabel te bouwen die als het verbindingsobject moet fungeren. Parameterset **SpecifyConnectionFields** hieronder wordt gebruikt om de eigenschappen van het verbindingsveld één voor één door te geven. Met **UseConnectionObject** kunt u de verbinding rechtstreeks doorgeven. Zoals u kunt zien is voor de cmdlet Send-TwilioSMS in de [Twilio PowerShell-module](https://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) de doorgave op beide manieren mogelijk: 

    ```
    function Send-TwilioSMS {
      [CmdletBinding(DefaultParameterSetName='SpecifyConnectionFields', `
      HelpUri='http://www.twilio.com/docs/api/rest/sending-sms')]
      param(
         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AccountSid,

         [Parameter(ParameterSetName='SpecifyConnectionFields', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [string]
         $AuthToken,

         [Parameter(ParameterSetName='UseConnectionObject', Mandatory=$true)]
         [ValidateNotNullOrEmpty()]
         [Hashtable]
         $Connection

       )
    }
    ```
<br>
3. Definieer het uitvoertype voor alle cmdlets in de module. Als u een uitvoertype voor een cmdlet definieert, kan IntelliSense u tijdens het ontwerpen helpen bij het bepalen van de uitvoereigenschappen van de cmdlet, voor gebruik tijdens het ontwerpen. Het is vooral nuttig tijdens het grafisch ontwerpen van een Automation-runbook, waarbij ontwerptijdkennis essentieel is voor een goede gebruikerservaring met uw module.<br> ![Uitvoertype grafisch runbook](media/automation-integration-modules/runbook-graphical-module-output-type.png)<br> Dit is vergelijkbaar met de functionaliteit 'automatisch aanvullen' van de uitvoer van een cmdlet in PowerShell ISE zonder deze te hoeven uitvoeren.<br> ![POSH IntelliSense](media/automation-integration-modules/automation-posh-ise-intellisense.png)<br>
4. Cmdlets in de module mogen geen complexe objecttypen voor parameters hebben. PowerShell Workflow verschilt van PowerShell in het volgende opzicht: complexe typen worden in gedeserialiseerde vorm opgeslagen. Primitieve typen blijven primitieven, maar complexe typen worden geconverteerd naar hun gedeserialiseerde versies, die in wezen eigenschappenverzamelingen zijn. Als u bijvoorbeeld de cmdlet **Get-Process** hebt gebruikt in een runbook (of een PowerShell Workflow), wordt een object van het type [Deserialized.System.Diagnostic.Process] geretourneerd, niet van het verwachte type [System.Diagnostic.Process]. Dit type heeft dezelfde eigenschappen als het niet-gedeserialiseerde type, maar geen van de methoden van dat type. En als u deze waarde als een parameter probeert door te geven aan een cmdlet, waarbij door de cmdlet een waarde [System.Diagnostic.Process] voor deze parameter wordt verwacht, wordt de volgende foutmelding weergegeven: *Argumenttransformatie kan niet worden verwerkt voor parameter 'process'. Fout: "De waarde "System.Diagnostics.Process (CcmExec)" van type "Deserialized.System.Diagnostics.Process" kan niet worden geconverteerd naar type "System.Diagnostics.Process".*   Dit komt omdat de typen niet overeenkomen van het verwachte type [System.Diagnostic.Process] en het opgegeven type [Deserialized.System.Diagnostic.Process]. De manier om dit probleem op te lossen is ervoor te zorgen dat de cmdlets van uw module geen complexe typen voor parameters hebben. Dit is de verkeerde manier.

    ```
    function Get-ProcessDescription {
      param (
            [System.Diagnostic.Process] $process
      )
      $process.Description
    }
    ``` 
<br>
 En dit is de juiste manier, een primitieve nemen die intern kan worden gebruikt door de cmdlet om het complexe object op te halen en te gebruiken. Aangezien de cmdlets worden uitgevoerd in de context van PowerShell, en niet PowerShell Workflow, wordt het correcte type in de cmdlet $process [System.Diagnostic.Process].  

    ```
    function Get-ProcessDescription {
      param (
            [String] $processName
      )
      $process = Get-Process -Name $processName

      $process.Description
    }
    ```
<br>
 Verbindingsassets in runbooks zijn hashtabellen, die een complex type zijn, en toch lijken deze hashtabellen te kunnen worden doorgegeven in cmdlets voor hun -Connection-parameter, zonder gecaste uitzondering. In technisch opzicht kunnen sommige PowerShell-typen goed worden gecast van hun geserialiseerde vorm naar hun gedeserialiseerde vorm en kunnen daarom worden doorgegeven in cmdlets voor parameters die het niet-gedeserialiseerde type accepteren. Hashtabel is daar een van. Het is mogelijk om gedefinieerde typen van een module-auteur te implementeren op een manier die deze ook correct kan deserialiseren, maar er zijn dan enkele compromissen nodig. Het type moet een standaardconstructor hebben, alle eigenschappen van het type moeten openbaar zijn en het type moet een PSTypeConverter hebben. Voor al gedefinieerde typen waarvan de module-auteur geen eigenaar is, is er echter geen manier om ze 'op te lossen', vandaar de aanbeveling om complexe typen voor parameters helemaal te vermijden. Tip voor het ontwerpen van een runbook: als om de een of andere reden een parameter van een complex type nodig is voor uw cmdlets, of u gebruikt de module van iemand anders waarvoor een parameter van een complex type is vereist, is de tijdelijke oplossing in PowerShell Workflow-runbooks en PowerShell Workflows in lokale PowerShell het verpakken van de cmdlet waarmee het complexe type wordt gegenereerd en de cmdlet waarmee het complexe type wordt gebruikt in dezelfde InlineScript-activiteit. Omdat met InlineScript de inhoud wordt uitgevoerd als PowerShell in plaats van PowerShell Workflow, zou met de cmdlet waarmee het complexe type wordt gegenereerd, dat correcte type worden gemaakt, niet het gedeserialiseerde complexe type.
5. Maak alle cmdlets in de module staatloos. In PowerShell Workflow wordt elke cmdlet die in de werkstroom wordt aangeroepen in een andere sessie uitgevoerd. Dit betekent dat cmdlets die afhankelijk zijn van de sessiestatus gemaakt/gewijzigd door andere cmdlets in dezelfde module niet zullen werken in PowerShell Workflow-runbooks.  Hier ziet u een voorbeeld van wat u niet moet doen.

    ```
    $globalNum = 0
    function Set-GlobalNum {
       param(
           [int] $num
       )
      
       $globalNum = $num
    }
    function Get-GlobalNumTimesTwo {
       $output = $globalNum * 2
     
       $output
    }
    ```
<br>
6. De module moet volledig zijn opgenomen in een pakket waarvoor Xcopy kan worden gebruikt. Omdat Azure Automation-modules worden gedistribueerd naar de Automation-sandboxes wanneer runbooks moeten worden uitgevoerd, moeten ze onafhankelijk van de host werken waarop ze worden uitgevoerd. Dit betekent dat u het modulepakket moet kunnen zippen, het verplaatsen naar een andere host met dezelfde of een nieuwere PowerShell-versie en dit normaal moet kunnen laten functioneren wanneer het in de PowerShell-omgeving van die host is geïmporteerd. Hiervoor mag de module niet afhankelijk zijn van bestanden buiten de modulemap (de map die wordt gezipt wanneer u in Azure Automation importeert), of van unieke registerinstellingen voor een host, zoals die die zijn ingesteld bij de installatie van een product. Als deze aanbevolen procedure niet wordt gevolgd, is de module niet bruikbaar in Azure Automation.  

## Volgende stappen

- Zie [Mijn eerste PowerShell Workflow-runbook](automation-first-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks
- Zie [Een Windows PowerShell-module schrijven](https://msdn.microsoft.com/library/dd878310(v=vs.85).aspx) voor meer informatie over het maken van PowerShell-modules



<!--HONumber=Jun16_HO2-->



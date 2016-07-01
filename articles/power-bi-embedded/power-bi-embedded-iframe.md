<properties
   pageTitle="Microsoft Power BI Embedded Preview: een Power BI-rapport insluiten met een IFrame"
   description="Microsoft Power BI Embedded Preview: essentiële code voor het integreren van een rapport in uw app, verifiëren met behulp van het Power BI Embedded-app-token, rapporten ophalen"
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="05/16/2016"
   ms.author="derrickv"/>

# Een Power BI-rapport insluiten met een IFrame
In dit artikel vindt u de essentiële code voor het gebruik van de **Power BI Embedded** REST API, app-tokens, een iFrame en wat JavaScript voor het integreren (of insluiten) van een rapport in uw app.

In [Aan de slag met Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md) leest u hoe u een **Werkruimteverzameling** configureert met een of meer **werkruimten** voor uw rapportinhoud. Vervolgens gaat u in het voorbeeld [Aan de slag met Microsoft Power BI Embedded](power-bi-embedded-get-started-sample.md) een rapport in een **werkruimte** importeren.

Dit artikel bevat de stappen voor het insluiten van een rapport in uw app. Zoals beschreven in dit artikel, moet u het voorbeeld [Een rapport integreren met een IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) op GitHub downloaden. Dit voorbeeld is een eenvoudig ASP.NET-webformulier dat is bedoeld om de essentiële C#- en JavaScript-code te illustreren die u nodig hebt voor het integreren van een rapport. Zie voor een meer geavanceerd voorbeeld dat gebruikmaakt van het MVC-ontwerp (Model-View-Controller) voor het integreren van een rapport [Voorbeeld van een dashboard-web-app](http://go.microsoft.com/fwlink/?LinkId=761493) op GitHub.

Hieronder vindt u de procedure voor het integreren van een **Power BI Embedded**-rapport in uw app.

Volg deze de stappen voor het integreren van een rapport.

- Stap 1: [Haal een rapport op in een werkruimte](#GetReport). In deze stap gebruikt u een app-tokenstroom om te zorgen dat een toegangstoken de [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST-bewerking aanroept. Wanneer u een rapport van de lijst **Get Reports** hebt opgehaald, sluit u een rapport in een app in met een **IFrame**-element.
- Stap 2: [Sluit een rapport in een app in](#EmbedReport). In deze stap gebruikt u een insluittoken voor een rapport, wat JavaScript en een IFrame om een rapport in een web-app te integreren (ofwel in te sluiten).

Als u het voorbeeld wilt uitvoeren om te zien hoe u een rapport kunt integreren, downloadt u het voorbeeld [Een rapport integreren met een IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) op GitHub en configureert u drie Web.Config-instellingen:

- **AccessKey**: een **AccessKey** wordt gebruikt voor het genereren van een JSON Web Token (JWT), die wordt gebruikt om rapporten op te halen en een rapport in te sluiten. Voor meer informatie over hoe u een **AccessKey** kunt krijgen, raadpleegt u [Aan de slag met Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md).
- **WorkspaceName**: voor meer informatie over hoe u een **WorkspaceName** kunt krijgen, raadpleegt u [Aan de slag met Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md).
- **WorkspaceId**: voor meer informatie over hoe u een **WorkspaceId** kunt krijgen, raadpleegt u [Aan de slag met Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md).

In de volgende secties ziet u de code de u nodig hebt voor het integreren van een rapport.

<a name="GetReport"/>
## Een rapport ophalen in een werkruimte

Voor het integreren van een rapport in een app, hebt u een **ID** en **embedUrl** voor een rapport nodig. U kunt een **ID** en **embedUrl** voor een rapport ophalen door de [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST-bewerking aan te roepen en een rapport te kiezen in de JSON-lijst. In [Een rapport insluiten in een app](#EmbedReport) gebruikt u een **ID** en **embedUrl** voor een rapport om het rapport in uw app in te sluiten.

### Reactie op Get reports JSON
```
{
  "@odata.context":"https://api.powerbi.com/beta/collections/{WorkspaceName}/workspaces/{WorkspaceId}/$metadata#reports","value":[
    {
      "id":"804d3664-…-e71882055dba","name":"Import report sample","webUrl":"https://embedded.powerbi.com/reports/804d3664-...-e71882055dba","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=804d3664-...-e71882055dba"
    },{
      "id":"1d7cff58-…-380610e263a9","name":"Sample Report 2","webUrl":"https://embedded.powerbi.com/reports/1d7cff58-...-380610e263a9","embedUrl":"https://embedded.powerbi.com/appTokenReportEmbed?reportId=1d7cff58-...-380610e263a9"
    }
  ]
}

```

Voor het aanroepen van de [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx) REST-bewerking gebruik u een app-token. Zie [Informatie over de app-tokenstroom in Power BI Embedded](power-bi-embedded-app-token-flow.md) voor meer informatie over de app-tokenstroom. In de volgende code wordt beschreven hoe een JSON-lijst met rapporten wordt opgehaald. Informatie over het insluiten van een rapport vindt u in [Een rapport insluiten in een app](#EmbedReport).

```
protected void getReportsButton_Click(object sender, EventArgs e)
{
    //Get an app token to generate a JSON Web Token (JWT). An app token flow is a claims-based design pattern.
    //To learn how you can code an app token flow to generate a JWT, see the PowerBIToken class.
    var appToken = PowerBIToken.CreateDevToken(workspaceName, workspaceId);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = appToken.Generate(accessKey);

    //Set app token textbox to JWT string to show that the JWT was generated
    appTokenTextbox.Text = jwt;

    //Construct reports uri resource string
    var uri = String.Format("https://api.powerbi.com/beta/collections/{0}/workspaces/{1}/reports", workspaceName, workspaceId);

    //Configure reports request
    System.Net.WebRequest request = System.Net.WebRequest.Create(uri) as System.Net.HttpWebRequest;
    request.Method = "GET";
    request.ContentLength = 0;

    //Set the WebRequest header to AppToken, and jwt
    //Note the use of AppToken instead of Bearer
    request.Headers.Add("Authorization", String.Format("AppToken {0}", jwt));

    //Get reports response from request.GetResponse()
    using (var response = request.GetResponse() as System.Net.HttpWebResponse)
    {
        //Get reader from response stream
        using (var reader = new System.IO.StreamReader(response.GetResponseStream()))
        {
            //Deserialize JSON string into PBIReports
            PBIReports PBIReports = JsonConvert.DeserializeObject<PBIReports>(reader.ReadToEnd());

            //Clear Textbox
            tb_reportsResult.Text = string.Empty;

            //Get each report
            foreach (PBIReport rpt in PBIReports.value)
            {
                tb_reportsResult.Text += String.Format("{0}\t{1}\t{2}\n", rpt.id, rpt.name, rpt.embedUrl);
            }
        }
    }
}
```

<a name="EmbedReport"/>
## Een rapport insluiten een app

Voordat u een rapport in uw app kunt insluiten, moet u een token voor een rapport insluiten. Dit token is vergelijkbaar met een app-token dat wordt gebruikt om **Power BI Embedded** REST-bewerkingen aan te roepen, maar dit token wordt gegenereerd voor een rapportresource in plaats van een REST-resource. Hier volgt de code voor het ophalen van een app-token voor een rapport. Zie [Rapport insluiten in uw app](#EmbedReportJS) voor informatie over het gebruik van het app-token voor het rapport.

<a name="EmbedReportToken"/>
### Een app-token ophalen voor een rapport

```
protected void getReportAppTokenButton_Click(object sender, EventArgs e)
{
    //Get an embed token for a report.
    var token = PowerBIToken.CreateReportEmbedToken(workspaceName, workspaceId, getReportAppTokenTextBox.Text);

    //After you get a PowerBIToken which has Claims including your WorkspaceName and WorkspaceID,
    //you generate JSON Web Token (JWT) . The Generate() method uses classes from System.IdentityModel.Tokens: SigningCredentials,
    //JwtSecurityToken, and JwtSecurityTokenHandler.
    string jwt = token.Generate(accessKey);

    //Set textbox to JWT string. The JavaScript embed code uses the embed jwt for a report.
    reportAppTokenTextbox.Text = jwt;
}
```

<a name="EmbedReportJS"/>
### Rapport insluiten in uw app

Voor het insluiten van een **Power BI**-rapport in uw app, gebruikt u een IFrame en wat JavaScript-code. Hier volgt een voorbeeld van IFrame en JavaScript-code voor het insluiten van een rapport. Wanneer u alle voorbeeldcode voor het insluiten van een rapport wilt zien, gaat u naar het voorbeeld [Een rapport integreren met een IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe) op GitHub.

![Iframe](media\power-bi-embedded-integrate-report\Iframe.png)


```
window.onload = function () {
    // Client side click to embed a selected report.
    var el = document.getElementById("bEmbedReportAction");
    if (el.addEventListener) {
        el.addEventListener("click", updateEmbedReport, false);
    } else {
        el.attachEvent('onclick', updateEmbedReport);
    }

};

// Update embed report
function updateEmbedReport() {
    // Check if the embed url was selected
    var embedUrl = document.getElementById('tb_EmbedURL').value;

    // To load a report do the following:
    // 1: Set the url
    // 2: Add a onload handler to submit the auth token
    var iframe = document.getElementById('iFrameEmbedReport');
    iframe.src = embedUrl;
    iframe.onload = postActionLoadReport;
}

// Post the auth token to the iFrame.
function postActionLoadReport() {

    // Get the app token.
    accessToken = document.getElementById('MainContent_reportAppTokenTextbox').value;

    // Construct the push message structure
    var m = { action: "loadReport", accessToken: accessToken };
    message = JSON.stringify(m);

    // Push the message.
    iframe = document.getElementById('iFrameEmbedReport');
    iframe.contentWindow.postMessage(message, "*");
}
```
Nadat u een rapport in uw app hebt ingesloten, kunt u het rapport filteren. In de volgende sectie wordt beschreven hoe u een rapport filtert met behulp van een URL-syntaxis.

## Een rapport filteren

U kunt een ingesloten rapport filteren met behulp van een URL-syntaxis. U doet dit door een queryreeksparameter aan uw iFrame src-url toe te voegen met het opgegeven filter. U kunt **filteren op een waarde** en **het filterdeelvenster verbergen**.


**Filteren op een waarde**

Als u wilt filteren op een waarde, gebruikt u een **$filter**-querysyntaxis met een **eq**-operator, als volgt:

```
https://app.powerbi.com/reportEmbed
?reportId=d2a0ea38-0694-...-ee9655d54a4a&
$filter={tableName/fieldName}%20eq%20'{fieldValue}'
```

U zou bijvoorbeeld kunnen filteren op een winkelketen genaamd 'Lindseys'. Het filterdeel van de url ziet er dan als volgt uit:

```
$filter=Store/Chain%20eq%20'Lindseys'
```

> [AZURE.NOTE] {tableName/fieldName} mag geen spaties of speciale tekens bevatten. De {fieldValue} accepteert één categorische waarde.

**Het filterdeelvenster verbergen**

Wanneer u het **filterdeelvenster** wilt verbergen, voegt u als volgt **filterPaneEnabled** toe aan de rapportquerytekenreeks:

```
&filterPaneEnabled=false
```

## Conclusie

In dit artikel hebt u kennisgemaakt met de code voor het integreren van een **Power BI**-rapport in uw app. Wanneer u snel aan de slag wilt gaan met het integreren van een rapport in een app, downloadt u deze voorbeelden op GitHub:

- [Voorbeeld: Een rapport integreren met een IFrame](https://github.com/Azure-Samples/power-bi-embedded-iframe)
- [Voorbeeld van dashboard-web-app](http://go.microsoft.com/fwlink/?LinkId=761493)

## Zie ook
- [Aan de slag met Microsoft Power BI Embedded Preview](power-bi-embedded-get-started.md)
- [Aan de slag met het voorbeeld](power-bi-embedded-get-started-sample.md)
- [System.IdentityModel.Tokens.SigningCredentials](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [System.IdentityModel.Tokens.JwtSecurityToken](https://msdn.microsoft.com/library/system.identitymodel.tokens.jwtsecuritytoken.aspx)
- [System.IdentityModel.Tokens.JwtSecurityTokenHandler](https://msdn.microsoft.com/library/system.identitymodel.tokens.signingcredentials.aspx)
- [Get Reports](https://msdn.microsoft.com/library/mt711510.aspx)



<!--HONumber=Jun16_HO2-->



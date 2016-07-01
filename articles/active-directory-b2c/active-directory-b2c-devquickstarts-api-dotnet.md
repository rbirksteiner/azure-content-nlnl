<properties
    pageTitle="Voorbeeld van Azure AD B2C | Microsoft Azure"
    description="Een .NET-web-API maken met Azure Active Directory B2C, beveiligd met OAuth 2.0-toegangstokens voor verificatie."
    services="active-directory-b2c"
    documentationCenter=".net"
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="05/16/2016"
    ms.author="dastrock"/>

# Voorbeeld van Azure Active Directory B2C: een .NET-web-API maken

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Met Azure Active Directory (Azure AD) B2C kunt u een web-API beveiligen met OAuth 2.0-toegangstokens. Met deze tokens kunnen client-apps die gebruikmaken van Azure AD B2C, worden geverifieerd bij de API. In dit artikel leest u hoe u een .NET Model-View-Controller-app (MVC) voor takenlijsten maakt die gebruikersregistratie, aanmelding en profielbeheer bevat. De takenlijst van elke gebruiker wordt opgeslagen door een taakservice. Dit is een web-API waarmee geverifieerde gebruikers taken kunnen maken en lezen in hun takenlijsten.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Een Azure AD B2C-directory maken

Voordat u Azure AD B2C kunt gebruiken, moet u een directory, of tenant, maken. Een directory is een container voor alle gebruikers, apps, groepen en meer. Als u nog geen directory hebt, [maakt u een B2C-directory](active-directory-b2c-get-started.md) voordat u doorgaat in deze handleiding.

## Een app maken

Vervolgens maakt u een app in uw B2C-directory. Hiermee geeft u informatie door aan Azure AD die nodig is om veilig te communiceren met uw app. Volg [deze instructies](active-directory-b2c-app-registration.md) om een app te maken. Zorg ervoor dat:

- U een **web-app** of **web-API** in de toepassing opneemt.
- U de **URI voor omleiden** `https://localhost:44316/` gebruikt voor de web-app. Dit is de standaardlocatie van de web-app-client voor dit codevoorbeeld.
- U de **toepassings-id** kopieert die is toegewezen aan uw app. U hebt deze later nodig.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Het beleid maken

In Azure AD B2C wordt elke gebruikerservaring gedefinieerd door een [beleid](active-directory-b2c-reference-policies.md). De client in dit codevoorbeeld bevat drie identiteitservaringen: registreren, aanmelden en profiel bewerken. U moet een beleid maken voor elk type, zoals wordt beschreven in het [naslagartikel voor beleid](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Wanneer u uw drie beleidsregels maakt:

- Kiest u **Registratie met gebruikers-id** of **Registratie via e-mail** in de blade met identiteitsproviders.
- Kiest u **Weergavenaam** en andere registratiekenmerken in het registratiebeleid.
- Kiest u **Weergavenaam**- en **Object-id**-claims als toepassingsclaims voor elk beleid. U kunt ook andere claims kiezen.
- Kopieert u de **Naam** van elk beleid nadat u dit hebt gemaakt. U hebt deze beleidsnamen later nodig.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nadat u de drie beleidsregels hebt gemaakt, kunt u uw app maken.

## De code downloaden

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-devquickstarts-bug-fix.md)]

De code voor deze zelfstudie [wordt bewaard in GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Als u het voorbeeld wilt maken terwijl u aan het werk bent, kunt u [een basisproject downloaden als zip-bestand](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). U kunt het basisproject ook klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

De voltooide app is ook [beschikbaar als zip-bestand](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) of in de `complete`-vertakking van dezelfde opslagplaats.

Nadat u de voorbeeldcode hebt gedownload, opent u het SLN-bestand in Visual Studio om aan de slag te gaan. Het oplossingsbestand bevat twee projecten: `TaskWebApp` en `TaskService`. `TaskWebApp` is een MVC-webtoepassing waarmee de gebruiker werkt. `TaskService` is de web-API voor de back-end van de app waarin elke takenlijst van de gebruiker wordt opgeslagen.

## De web-app voor taken configureren

Wanneer een gebruiker met `TaskWebApp` werkt, verzendt de client aanvragen naar Azure AD en worden tokens teruggestuurd waarmee de web-API `TaskService` kan worden aangeroepen. Als u de gebruiker wilt aanmelden en tokens wilt ophalen, moet u bepaalde informatie over uw app opgeven voor `TaskWebApp`. Open in het `TaskWebApp`-project het bestand `web.config` in de hoofdmap van het project en vervang de waarden in de sectie `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Er zijn ook twee `[PolicyAuthorize]`-decorators waarvoor u de aanmeldingsbeleidsnaam moet opgeven. Met het kenmerk `[PolicyAuthorize]` wordt een bepaald beleid aangeroepen wanneer een gebruiker een pagina in de app wil openen waarvoor verificatie is vereist.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

Zie [Build a .NET web app](active-directory-b2c-devquickstarts-web-dotnet.md) (Een .NET web-app maken) voor meer informatie over de manier waarop Azure AD B2C wordt gebruikt door een web-app zoals `TaskWebApp`.

## De API beveiligen

Wanneer u een client hebt die de API aanroept namens gebruikers, kunt u `TaskService` beveiligen met Bearer-tokens van OAuth 2.0. De API kan tokens accepteren en valideren met behulp van de Microsoft Open Web Interface voor .NET-bibliotheek (OWIN).

### OWIN installeren
Installeer eerst de OWIN OAuth-verificatiepijplijn:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### Uw B2C-gegevens invoeren
Open het bestand `web.config` in de hoofdmap van het `TaskService`-project en vervang de waarden in de sectie `<appSettings>`. Deze waarden worden gebruikt in de API- en OWIN-bibliotheek.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

### Een OWIN-opstartklasse toevoegen
Voeg een OWIN-opstartklasse toe aan het `TaskService`-project met de naam `Startup.cs`.  Klik met de rechtermuisknop op het project, selecteer achtereenvolgens **Toevoegen** en **Nieuw item** en zoek naar OWIN.


```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

### OAuth 2.0-verificatie configureren
Open het bestand `App_Start\Startup.Auth.cs` en implementeer de `ConfigureAuth(...)`-methode:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
    private const string discoverySuffix = ".well-known/openid-configuration";

    public void ConfigureAuth(IAppBuilder app)
    {   
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API accepts tokens only from its own clients
            ValidAudience = clientId,
        };

        app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
        {   
            // This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
        });
    }
}
```

### De taakcontroller beveiligen
Nadat de app is geconfigureerd voor het gebruik van OAuth 2.0-verificatie, kunt u uw web-API beveiligen door een `[Authorize]`-tag toe te voegen aan de taakcontroller. Dit is de controller waarop alle bewerkingen van de takenlijst worden uitgevoerd, dus moet u de hele controller beveiligen op klasseniveau. U kunt ook de `[Authorize]`-tag toevoegen aan afzonderlijke acties voor nauwkeuriger beheer.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### Gebruikersgegevens ophalen uit het token
`TasksController` slaat taken in een database op waarin elke taak een gekoppelde gebruiker heeft die 'eigenaar' is van de taak. De eigenaar wordt geïdentificeerd met de **object-id** van de gebruiker. (Daarom moet u de object-id toevoegen als toepassingsclaim in al uw beleidsregels.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## De voorbeeld-app uitvoeren

Bouw ten slotte `TaskWebApp` en `TaskService` en voer deze uit. Registreer u bij de app met een e-mailadres of gebruikersnaam. Maak een paar taken in de takenlijst van de gebruiker en bekijk hoe deze zelfs bewaard blijven in de API nadat u de client hebt gestopt en opnieuw hebt gestart.

## Het beleid bewerken

Nadat u een API hebt beveiligd met behulp van Azure AD B2C, kunt u experimenteren met het beleid voor de app en de effecten hiervan (of het gebrek daaraan) op de API bekijken. U kunt <!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->de toepassingsclaims in het beleid bewerken en de beschikbare gebruikersgegevens in de web-API wijzigen. Claims die u toevoegt, zijn beschikbaar in uw .NET MVC-web-API in het `ClaimsPrincipal`-object, zoals eerder in dit artikel is beschreven.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Jun16_HO2-->



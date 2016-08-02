<properties
    pageTitle="Azure Active Directory B2C Preview: Een web-API aanroepen vanuit een iOS-toepassing | Microsoft Azure"
    description="In dit artikel wordt beschreven hoe u een iOS-takenlijst-app maakt die een Node.js-web-API aanroept met behulp van OAuth 2.0-bearer-tokens. De iOS-app en de web-API maken beide gebruik van Azure Active Directory B2C om gebruikersidentiteiten te beheren en gebruikers te verifiëren."
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

    ms.date="05/31/2016"
    ms.author="brandwe"/>

# Azure AD B2C Preview: Een web-API aanroepen vanuit een iOS-toepassing

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Met Azure Active Directory (Azure AD) B2C kunt u in slechts enkele korte stappen krachtige functies voor self-service identiteitsbeheer toevoegen aan uw iOS-apps en web-API's. In dit artikel ziet u hoe u een iOS-takenlijst-app maakt die een Node.js-web-API aanroept met behulp van OAuth 2.0-bearer-tokens. De iOS-app en de web-API maken beide gebruik van Azure AD B2C om gebruikersidentiteiten te beheren en gebruikers te verifiëren.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
    Deze snelstartgids kan alleen volledig worden doorlopen als u al een web-API hebt die is beveiligd met Azure AD B2C. We hebben er één gemaakt voor .NET en voor Node.js, die u kunt gebruiken. In deze procedure wordt ervan uitgegaan dat het web-API-voorbeeld voor Node.js is geconfigureerd. Raadpleeg het [Voorbeeld van Azure Active Directory web-API voor Node.js](active-directory-b2c-devquickstarts-api-node.md) voor meer informatie.
).

> [AZURE.NOTE]
    In dit artikel wordt niet beschreven hoe u aanmelding, registratie en profielbeheer implementeert met behulp van Azure AD B2C. Dit artikel gaat over het aanroepen van web-API's nadat de gebruiker is geverifieerd. Als u dit nog niet hebt gedaan, leest u eerst de [zelfstudie Aan de slag met .NET-web-app](active-directory-b2c-devquickstarts-web-dotnet.md) voor meer informatie over de basisprincipes van Azure AD B2C.

## Een Azure AD B2C-directory maken

Voordat u Azure AD B2C kunt gebruiken, moet u een directory, of tenant, maken. Een directory is een container voor alle gebruikers, apps, groepen en meer. Als u nog geen directory hebt, [maakt u een B2C-directory](active-directory-b2c-get-started.md) voordat u verdergaat.

## Een app maken

Vervolgens maakt u een app in uw B2C-directory. Hiermee voorziet u Azure AD van de informatie die nodig is om veilig te communiceren met uw app. Zowel de app als de web-API worden in dit geval aangegeven met één **toepassings-id** omdat ze samen één logische app vormen. Volg [deze instructies](active-directory-b2c-app-registration.md) om een app te maken. Zorg ervoor dat:

- U een **web-app of web-API** in de toepassing opneemt.
- U `http://localhost:3000/auth/openid/return` invoert als **antwoord-URL**. Dit is de standaard-URL voor dit codevoorbeeld.
- U een **toepassingsgeheim** maakt voor uw toepassing en dit kopieert. U hebt dit later nodig.
- U de **toepassings-id** kopieert die is toegewezen aan uw app. Deze hebt u ook later nodig.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Het beleid maken

In Azure AD B2C wordt elke gebruikerservaring gedefinieerd door [beleid](active-directory-b2c-reference-policies.md). Deze app bevat drie identiteitservaringen: registreren, aanmelden en aanmelden via Facebook. U moet één beleidsregel maken voor elk type, zoals wordt beschreven in het [naslagartikel voor beleid](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Wanneer u uw drie beleidsregels maakt:

- Kiest u **Weergavenaam** en registratiekenmerken in het registratiebeleid.
- Kiest u **Weergavenaam**- en **Object-id**-toepassingsclaims voor elk beleid. U kunt ook andere claims kiezen.
- Kopieert u de **naam** van elk beleid nadat u dit hebt gemaakt. Deze moet het voorvoegsel `b2c_1_` bevatten.  U hebt deze beleidsnamen later nodig.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nadat u de drie beleidsregels hebt gemaakt, kunt u uw app maken.

In dit artikel wordt niet beschreven hoe u de zojuist gemaakte beleidsregels gebruikt. Voor meer informatie over de werking van beleid in Azure AD B2C, leest u eerst de [zelfstudie Aan de slag met .NET-web-app](active-directory-b2c-devquickstarts-web-dotnet.md).

## De code downloaden

De code voor deze zelfstudie [wordt bewaard in GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Als u het voorbeeld wilt maken terwijl u aan het werk bent, kunt u [het basisproject downloaden als zip-bestand](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). U kunt de basis ook klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **U moet het basisproject downloaden om deze zelfstudie te kunnen voltooien.** Vanwege de complexiteit van de implementatie van een volledig functionerende toepassing in iOS, bevat het **basisproject** UX-code die wordt uitgevoerd nadat u de zelfstudie hebt voltooid. Dit is een tijdbesparende maatregel voor ontwikkelaars. De UX-code behoort niet tot het onderwerp van hoe u B2C toevoegt aan een iOS-toepassing.

De voltooide app is [beschikbaar als ZIP-bestand](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) en staat ook in de `complete`-vertakking van dezelfde opslagplaats.

Vervolgens laadt u `podfile` via CocoaPods. Hiermee maakt u een nieuwe Xcode-werkruimte die u gaat laden. Als u niet over CocoaPods beschikt, [gaat u naar de website om het te installeren](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## De iOS-takenlijstoepassing configureren

U moet nog enkele algemene parameters opgeven om ervoor te zorgen dat de iOS-takenlijst-app kan communiceren met Azure AD B2C. Open in de map `Microsoft Tasks` het bestand `settings.plist` in de hoofdmap van het project en vervang de waarden in sectie `<dict>`. Deze waarden worden in de hele app gebruikt.

```
<dict>
    <key>authority</key>
    <string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
    <key>clientId</key>
    <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    <key>scopes</key>
    <array>
        <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    </array>
    <key>additionalScopes</key>
    <array>
    </array>
    <key>redirectUri</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>taskWebAPI</key>
    <string>http://localhost/tasks:3000</string>
    <key>emailSignUpPolicyId</key>
    <string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
    <key>faceBookSignInPolicyId</key>
    <string><your sign in policy for FB></string>
    <key>emailSignInPolicyId</key>
    <string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
    <key>fullScreen</key>
    <false/>
    <key>showClaims</key>
    <true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Toegangstokens ophalen en de takenlijst-API aanroepen

In deze sectie wordt beschreven hoe u een OAuth 2.0-tokenuitwisseling in een web-app kunt uitvoeren met behulp van bibliotheken en frameworks van Microsoft. Als u geen ervaring hebt met autorisatiecodes en toegangstokens, vindt u meer informatie in de [Naslaginformatie over het OAuth 2.0-protocol](active-directory-b2c-reference-protocols.md).

### Header-bestanden maken met behulp van methoden

U hebt methoden nodig om een token met het geselecteerde beleid te verkrijgen en vervolgens de taakserver aan te roepen. Stel deze nu in.

Maak een bestand met de naam `samplesWebAPIConnector.h` onder `/Microsoft Tasks` in het Xcode-project

Voeg de volgende code toe om te definiëren wat u moet doen:

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Dit zijn eenvoudige CRUD-bewerkingen (Create -maken, Read - lezen, Update - bijwerken en Delete - verwijderen) voor uw API, evenals een methode `doPolicy`. Met deze methode verkrijgt u een token met het gewenste beleid.

Er moeten echter nog twee header-bestanden worden gedefinieerd. Deze bevatten uw taakitem en beleidsgegevens. Maak deze nu als volgt:

Maak het bestand `samplesTaskItem.h` met de volgende code:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Maak ook het bestand `samplesPolicyData.h` waarin u de beleidsgegevens opslaat:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Een implementatie voor de taak en beleidsitems toevoegen

Nu de header-bestanden zijn gemaakt, kunt u de code schrijven voor het opslaan van de gegevens die u in het voorbeeld gaat gebruiken.

Maak het bestand `samplesPolicyData.m` met de volgende code:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Instellingscode schrijven voor de aanroep van ADAL voor iOS

De snelle code voor het opslaan van uw objecten voor de UI is nu klaar. Vervolgens moet u code schrijven om de ADAL (Active Directory Authentication Library) voor iOS te openen met de parameters die u in `settings.plist` hebt geplaatst. Hiermee verkrijgt u een toegangstoken die u kunt doorgeven aan de server.

Al het werk wordt uitgevoerd in `samplesWebAPIConnector.m`.

Maak eerst de `doPolicy()`-implementatie die u hebt geschreven in het header-bestand `samplesWebAPIConnector.h`:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

De methode is eenvoudig. Als invoer worden het `samplesPolicyData`-object dat u hebt gemaakt, het bovenliggende object `ViewController` en een retouraanroep gebruikt. De retouraanroep is interessant; we gaan die nader bekijken.

- `completionBlock` heeft `ADProfileInfo` als een type dat wordt geretourneerd met behulp van een `userInfo`-object. `ADProfileInfo` is het type dat alle antwoorden van de server bevat, met inbegrip van claims.
- Let ook op `readApplicationSettings`. Deze leest de gegevens die u hebt opgegeven in `settings.plist`.
- En tot slot hebt u een grote methode `getClaimsWithPolicyClearingCache`. Dit is de eigenlijk aanroep om ADAL voor iOS aan te roepen die u moet schrijven. We komen hier later op terug.

Nu gaan we de grote methode `getClaimsWithPolicyClearingCache` schrijven. Deze is groot genoeg voor een eigen sectie.

### De aanroep van ADAL voor iOS maken

Nadat u het basisproject hebt gedownload van GitHub, ziet u dat het diverse aanroepen bevat ter ondersteuning van de voorbeeldtoepassing. Ze volgen allemaal het patroon van `get(Claims|Token)With<verb>ClearningCache`. Doordat ze de conventies van Objective C volgen, lezen ze bijna als gewoon Engels. Bijvoorbeeld 'Get a token with extra parameters that I provide to you and clear the cache' (Haal een token met extra parameters die ik opgeef, en wis de cache) is `getTokenWithExtraParamsClearingCache()`.

U schrijft 'Get claims and a token with the policy that I provide to you and don't clear the cache' (Haal claims en een token met het beleid dat ik verstrekt en wis de cache niet), oftewel `getClaimsWithPolicyClearingCache`. U krijgt altijd een token terug van ADAL; u hoeft dus niet per se 'claims en een token' in de methode op te geven. Soms wilt u echter alleen het token, zonder dat u de claims moet parseren. Daarom hebben we een methode zonder claims, `getTokenWithPolicyClearingCache` genaamd, in het geraamte opgenomen.

Schrijf nu deze code:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

Het eerste deel ziet er waarschijnlijk vertrouwd uit.

- Laad de instellingen die zijn opgegeven in `settings.plist` en wijs ze toe aan `data`.
- Stel `ADAuthenticationError` in. Deze verwerkt elke fout die afkomstig is van ADAL voor iOS.
- Maak `authContext`. Hiermee wordt de aanroep van ADAL ingesteld. U geeft hieraan uw bevoegdheid door om alles op te starten.
- Geef `authContext` een verwijzing naar de bovenliggende controller zodat u hiernaar kunt terugkeren.
- Converteer `redirectURI`, een tekenreeks in `settings.plist`, in het NSURL-type dat ADAL verwacht.
- Stel `correlationId` in. Dit is een UUID die de oproep tussen de client en de server en terug kan volgen. Dit is handig voor foutopsporing.

Vervolgens belandt u bij de werkelijke aanroep naar ADAL. Hier verandert de aanroep ten aanzien van wat u verwacht te zien in eerdere toepassingen van ADAL voor iOS:

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

U ziet dat de aanroep tamelijk eenvoudig is.

`scopes`: de bereiken die u aan de server doorgeeft en die u bij een server wilt aanvragen voor een gebruiker die zich aanmeldt. Voor B2C Preview geeft u `client_id` door. Dit zal in de toekomst waarschijnlijk veranderen in het lezen van bereiken. We zullen dit document dan bijwerken.
`additionalScopes`: dit zijn extra bereiken die u misschien voor uw toepassing wilt gebruiken. Deze zullen in de toekomst waarschijnlijk worden gebruikt.
`clientId`: de toepassings-id die u via de portal hebt verkregen.
`redirectURI`: de omleiding waarnaar het token naar verwachting weer wordt gepubliceerd.
`identifier`: een manier om een gebruiker te identificeren zodat u kunt zien of er een bruikbaar token in de cache staat. U hoeft de server dan niet altijd om een nieuw token te vragen. Dit wordt uitgevoerd in een type dat `ADUserIdentifier` wordt genoemd, en u kunt opgeven wat u als id wilt gebruiken. Gebruik `username`.
`promptBehavior`: dit is afgeschaft. Dit moet `AD_PROMPT_ALWAYS` zijn.
`extraQueryParameters`: alles wat u verder aan de server wilt doorgeven in URL-notatie.
`policy`: het beleid dat u aanroept. Dit is het belangrijkste deel voor deze procedure.

U kunt in `completionBlock` zien dat u `ADAuthenticationResult` doorgeeft. Het bevat uw token en profielgegevens (als de aanroep is geslaagd).

Met de bovenstaande code kunt u een token verkrijgen voor het beleid dat u opgeeft. U kunt dit token vervolgens gebruiken om de API aan te roepen.

### De taakaanroepen naar de server maken

Nu u een token hebt, moet u dit aan de API verstrekken voor autorisatie.

Drie methoden moeten worden geïmplementeerd:

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` biedt een matrix die de taken in uw server vertegenwoordigt. `addTask` en `deleteTask` voeren de volgende acties uit en retourneren `true` of `false` als ze zijn geslaagd.

Schrijf eerst `getTaskList`:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

Het bespreken van de taakcode valt buiten het bereik van deze procedure. Maar misschien is u iets interessants opgevallen: een `craftRequest`-methode die de taak-URL gebruikt. Dit is de methode die u gebruikt om de aanvraag voor de server te maken met behulp van het toegangstoken dat u hebt ontvangen. Schrijf deze nu.

Voeg de volgende code toe aan het bestand `samplesWebAPIConnector.m`:

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Deze krijgt een URI (Web Uniform Resource Identifier), voegt het token eraan toe met behulp van de `Bearer`-header in HTTP en geeft het vervolgens weer aan u terug. U kunt de API `getTokenClearingCache` aanroepen. Het lijkt misschien vreemd, maar u gebruikt deze aanroep gewoon om een token op te halen uit de cache en te controleren of het nog geldig is. (De `getToken`-aanroepen doen dit voor u door ADAL te vragen.) U gebruikt deze code in elke aanroep. Vervolgens maakt u de aanvullende taakmethoden.

Schrijf `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Dit volgt hetzelfde patroon, maar hier wordt ook de laatste methode geïntroduceerd die u moet implementeren: `convertTaskToDictionary`. De methode neemt uw matrix en verandert deze in een woordenboekobject. Dit object kan eenvoudiger worden omgezet tot de queryparameters die u moet doorgeven aan de server. De code is eenvoudig:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Schrijf nu `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Afmelding toevoegen aan uw toepassing

Het laatste wat u moet doen, is een afmeldfunctie implementeren voor uw toepassing. Dit is heel eenvoudig. Doe het volgende in het bestand `sampleWebApiConnector.m`:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## De voorbeeld-app uitvoeren

Ontwikkel en voer ten slotte de app uit in Xcode. Registreer u of meld u aan bij de app en maak taken voor een aangemelde gebruiker. Meld u af en meld u opnieuw aan als een andere gebruiker, en maak taken voor die gebruiker.

U ziet dat de taken per gebruiker op de API worden opgeslagen, omdat de API de identiteit van de gebruiker afleidt uit het toegangstoken dat het ontvangt.

Ter referentie is het complete voorbeeld [als ZIP-bestand beschikbaar](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). U kunt dit ook klonen vanuit GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Volgende stappen

Nu kunt u verder met geavanceerdere B2C-onderwerpen. U kunt het volgende proberen:

[Een Node.js-web-API aanroepen vanuit een Node.js-web-app]()

[De UX voor een B2C-app aanpassen]()



<!--HONumber=Jun16_HO2-->



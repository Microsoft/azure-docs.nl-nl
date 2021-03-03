---
title: Toegangs beheer Azure Service Bus met hand tekeningen voor gedeelde toegang
description: Overzicht van Service Bus toegangs beheer met behulp van hand tekeningen voor gedeelde toegang, Details over SAS-autorisatie met Azure Service Bus.
ms.topic: article
ms.date: 01/19/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: d210da4b653a20dd273dfce723f0bf9d5dbf743b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737814"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Toegangs beheer Service Bus met hand tekeningen voor gedeelde toegang

In dit artikel worden *Shared Access signatures* (SAS) beschreven, hoe ze werken en hoe u ze op een platform neutraal manier gebruikt.

SAS-beveiligingen hebben toegang tot Service Bus op basis van autorisatie regels. Deze worden geconfigureerd op een naam ruimte of een bericht entiteit (wachtrij of onderwerp). Een autorisatie regel heeft een naam, is gekoppeld aan specifieke rechten en bevat een paar cryptografische sleutels. U gebruikt de naam en sleutel van de regel via de Service Bus SDK of uw eigen code om een SAS-token te genereren. Een client kan vervolgens het token door geven aan Service Bus om autorisatie voor de aangevraagde bewerking te bewijzen.

> [!NOTE]
> Azure Service Bus ondersteunt het toestaan van toegang tot een Service Bus naam ruimte en de bijbehorende entiteiten met behulp van Azure Active Directory (Azure AD). Het autoriseren van gebruikers of toepassingen die gebruikmaken van het OAuth 2,0-token dat wordt geretourneerd door Azure AD, biedt een superieure beveiliging en gebruiks vriendelijk gebruik van Shared Access signatures (SAS). Met Azure AD hoeft u geen tokens op te slaan in uw code en mogelijke beveiligings problemen met Risico's.
>
> Micro soft raadt u aan Azure AD te gebruiken met uw Azure Service Bus-toepassingen wanneer dat mogelijk is. Raadpleeg voor meer informatie de volgende artikelen:
> - [Verifieer en autoriseer een toepassing met Azure Active Directory om toegang te krijgen tot Azure service bus entiteiten](authenticate-application.md).
> - [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Azure Service Bus bronnen](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Overzicht van SAS

Shared Access Signatures zijn een autorisatie mechanisme op basis van claims met behulp van eenvoudige tokens. Met SAS worden sleutels nooit door gegeven op de kabel. Sleutels worden gebruikt voor het cryptografisch ondertekenen van informatie die later door de service kan worden geverifieerd. SAS kan worden gebruikt in combi natie met een gebruikers naam-en wachtwoord schema waarbij de client onmiddellijk in bezit is van een naam van een autorisatie regel en een overeenkomende sleutel. SAS kan ook worden gebruikt als een federatief beveiligings model, waarbij de client een time-Limited en ondertekend toegangs token ontvangt van een beveiligings token service, zonder dat deze ooit in bezit is van de handtekening sleutel.

SAS-verificatie in Service Bus is geconfigureerd met benoemde [Shared Access Authorization-autorisatie regels](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) die gekoppelde toegangs rechten hebben en een paar primaire en secundaire cryptografische sleutels. De sleutels zijn 256-bits waarden in Base64-weer gave. U kunt regels configureren op het niveau van de naam ruimte, op Service Bus [relays](../azure-relay/relay-what-is-it.md), [wacht rijen](service-bus-messaging-overview.md#queues)en [onderwerpen](service-bus-messaging-overview.md#topics).

Het [Shared Access Signature](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) -token bevat de naam van de gekozen autorisatie regel, de URI van de resource die moet worden geopend, het verloop van de bron en een door de HMAC-sha256 cryptografische hand tekening die wordt berekend op basis van deze velden met behulp van de primaire of secundaire cryptografie sleutel van de gekozen autorisatie regel.

## <a name="shared-access-authorization-policies"></a>Beleid voor gedeelde toegang

Elke Service Bus naam ruimte en elke Service Bus entiteit heeft een beleid voor gedeelde toegang dat bestaat uit regels. Het beleid op het niveau van de naam ruimte is van toepassing op alle entiteiten in de naam ruimte, ongeacht hun afzonderlijke beleids configuratie.

Voor elke autorisatie beleids regel besluit u over drie stukjes informatie: **naam**, **bereik** en **rechten**. De **naam** is gewoon. een unieke naam binnen dat bereik. Het bereik is eenvoudig genoeg: het is de URI van de bron in kwestie. Voor een Service Bus naam ruimte is de scope de Fully Qualified Domain Name (FQDN), zoals `https://<yournamespace>.servicebus.windows.net/` .

De rechten die worden verleend door de beleids regel, kunnen een combi natie zijn van:

* ' Verzenden ': het recht om berichten te verzenden naar de entiteit
* Luis teren: verleent het recht om te ontvangen (wachtrij, abonnementen) en alle gerelateerde bericht afhandeling
* ' Beheren ': verleent het recht om de topologie van de naam ruimte te beheren, met inbegrip van het maken en verwijderen van entiteiten

Het recht ' beheren ' bevat de rechten Send en receive.

Een naam ruimte-of entiteits beleid kan Maxi maal 12 Shared Access Authorization Rules bevatten, zodat er ruimte is voor drie sets regels, elk met de basis rechten en de combi natie van verzenden en Luis teren. Deze limiet onderstreept dat het SAS-beleids archief niet bedoeld is als een gebruikers-of service account-archief. Als uw toepassing toegang moet verlenen tot Service Bus op basis van gebruikers-of service-identiteiten, moet het een beveiligings token service implementeren die SAS-tokens uitgeeft na een verificatie en toegangs controle.

Aan een autorisatie regel is een *primaire sleutel* en een *secundaire sleutel* toegewezen. Dit zijn cryptografische sterke sleutels. Zorg ervoor dat ze niet verloren gaan of lekken: ze zijn altijd beschikbaar in de [Azure Portal][Azure portal]. U kunt een van de gegenereerde sleutels gebruiken en u kunt deze op elk gewenst moment opnieuw genereren. Als u een sleutel in het beleid opnieuw genereert of wijzigt, worden alle eerder uitgegeven tokens op basis van die sleutel onmiddellijk ongeldig. Actieve verbindingen die zijn gemaakt op basis van deze tokens blijven echter werken totdat het token verloopt.

Wanneer u een Service Bus naam ruimte maakt, wordt automatisch een beleids regel gemaakt met de naam **RootManageSharedAccessKey** voor de naam ruimte. Dit beleid heeft beheer machtigingen voor de volledige naam ruimte. Het is raadzaam deze regel te behandelen als **een account met** beheerders rechten en niet in uw toepassing te gebruiken. U kunt aanvullende beleids regels maken op het tabblad **configureren** van de naam ruimte in de portal via Power shell of Azure cli.

## <a name="best-practices-when-using-sas"></a>Best practices bij gebruik van SAS
Wanneer u gebruikmaakt van hand tekeningen voor gedeelde toegang in uw toepassingen, moet u rekening houden met twee mogelijke Risico's:

- Als een SAS wordt gelekt, kan deze worden gebruikt door iedereen die deze verkrijgt, waardoor uw Service Bus-resources mogelijk kunnen worden aangetast.
- Als een SAS die aan een client toepassing is gegeven, verloopt en de toepassing geen nieuwe SA'S kan ophalen van uw service, wordt de functionaliteit van de toepassing mogelijk belemmerd.

De volgende aanbevelingen voor het gebruik van hand tekeningen voor gedeelde toegang kunnen helpen bij het oplossen van deze Risico's:

- **Clients automatisch de SAS vernieuwen, indien nodig**: clients moeten de sa's goed vernieuwen voordat ze verlopen, zodat er tijd is om nieuwe pogingen te doen als de service die de SAS aanbiedt, niet beschikbaar is. Als uw SAS is bedoeld om te worden gebruikt voor een klein aantal directe, korte, langdurige bewerkingen die naar verwachting binnen de verloop periode zullen worden voltooid, is het mogelijk onnodig dat de SA'S niet naar verwachting worden verlengd. Als u echter een client hebt die regel matig aanvragen maakt via SAS, is het mogelijk dat de verval datum wordt afgespeeld. De belangrijkste overweging is om de nood zaak van de SA'S te verkorten (zoals eerder vermeld), met de nood zaak om ervoor te zorgen dat de client vernieuwingen vroeg genoeg aanvraagt (om onderbrekingen te voor komen als gevolg van het verlopen van SAS vóór een geslaagde vernieuwing).
- **Wees voorzichtig met de SAS-start tijd**: als u de begin tijd voor SAS **nu** instelt, wordt de klok scheefheid (verschillen in de huidige tijd op basis van verschillende computers) mogelijk voor de eerste paar minuten afgenomen. In het algemeen stelt u de start tijd in op ten minste 15 minuten in het verleden. U kunt de service ook niet instellen, waardoor deze onmiddellijk in alle gevallen geldig is. Hetzelfde geldt ook voor de verloop tijd. Houd er rekening mee dat u gedurende een wille keurige aanvraag Maxi maal 15 minuten aan de klok kunt laten hellen. 
- **Zorg ervoor dat de resource toegankelijk** is: een beveiligings best practice is om een gebruiker te voorzien van de mini maal vereiste bevoegdheden. Als een gebruiker alleen lees toegang nodig heeft tot één entiteit, dan verlenen zij Lees-en schrijf toegang tot de ene entiteit en niet lezen/schrijven/verwijderen voor alle entiteiten. Het helpt ook de schade te beperken als een SAS is aangetast omdat de SAS minder kracht in de handen van een aanvaller heeft.
- **Gebruik niet altijd SAS**: soms zijn de Risico's die zijn gekoppeld aan een bepaalde bewerking ten opzichte van uw event hubs, tegen de voor delen van SAS. Voor dergelijke bewerkingen maakt u een middelste laag service die naar uw Event Hubs schrijft na de validatie van de bedrijfs regel, verificatie en controle.
- **Altijd https gebruiken**: altijd https gebruiken om een SAS te maken of te distribueren. Als een SAS wordt door gegeven via HTTP en onderschept, kan een aanvaller die een man-in-the-Middle-koppeling uitvoert, de SA'S lezen en deze vervolgens gebruiken, net zoals de beoogde gebruiker kan hebben, mogelijk gevoelige gegevens in gevaar brengen of beschadiging van gegevens door de kwaadwillende gebruiker kan veroorzaken.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuratie voor Shared Access Signature-verificatie

U kunt de [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -regel configureren voor service bus naam ruimten, wacht rijen of onderwerpen. Het configureren van een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) op een service bus-abonnement wordt momenteel niet ondersteund, maar u kunt regels die zijn geconfigureerd in een naam ruimte of onderwerp, gebruiken om de toegang tot abonnementen te beveiligen. Zie voor een werk voorbeeld waarin deze procedure wordt geïllustreerd het voor beeld van het [gebruik van Shared Access Signature (SAS) met Service Bus abonnementen](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

![SAS](./media/service-bus-sas/service-bus-namespace.png)

In deze afbeelding zijn de *manageRuleNS*-, *SendRuleNS*-en *listenRuleNS* -autorisatie regels van toepassing op zowel wachtrij W1 als onderwerp T1, terwijl *listenRuleQ* en *SendRuleQ* alleen van toepassing zijn op wachtrij W1 en *sendRuleT* alleen van toepassing op het onderwerp T1.

## <a name="generate-a-shared-access-signature-token"></a>Een Shared Access Signature-token genereren

Elke client die toegang heeft tot de naam van een verificatie regel naam en een van de handtekening sleutels kan een SAS-token genereren. Het token wordt gegenereerd door een teken reeks in de volgende indeling te maken:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

- `se` -Token verloop datum direct. Geheel getal dat seconden weergeeft sinds de epoche `00:00:00 UTC` op 1 januari 1970 (UNIX-epoche) wanneer het token verloopt.
- `skn` -De naam van de autorisatie regel.
- `sr` -URL-gecodeerde URI van de bron waartoe toegang wordt verkregen.
- `sig` -URL-gecodeerde HMACSHA256-hand tekening. De hash-berekening ziet er ongeveer uit als de volgende pseudo code en retourneert base64 van onbewerkte binaire uitvoer.

    ```
    urlencode(base64(hmacsha256(urlencode('https://<yournamespace>.servicebus.windows.net/') + "\n" + '<expiry instant>', '<signing key>')))
    ```

Hier volgt een voor beeld van een C#-code voor het genereren van een SAS-token:

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

> [!IMPORTANT]
> Zie [SAS-token genereren](/rest/api/eventhub/generate-sas-token)voor voor beelden van het genereren van een SAS-token met behulp van verschillende programmeer talen. 


Het token bevat de niet-gehashte waarden, zodat de ontvanger de hash opnieuw kan berekenen met dezelfde para meters, zodat u kunt controleren of de uitgever in het bezit is van een geldige ondertekeningssleutel.

De resource-URI is de volledige URI van de Service Bus resource waarmee de toegang wordt geclaimd. Bijvoorbeeld, `http://<namespace>.servicebus.windows.net/<entityPath>` of `sb://<namespace>.servicebus.windows.net/<entityPath>` ; `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3` ... 

**De URI moet een [percentage zijn gecodeerd](/dotnet/api/system.web.httputility.urlencode).**

De gedeelde toegangs autorisatie regel die wordt gebruikt voor ondertekening moet worden geconfigureerd voor de entiteit die door deze URI wordt opgegeven of door een van de hiërarchische bovenliggende items. Bijvoorbeeld `http://contoso.servicebus.windows.net/contosoTopics/T1` of `http://contoso.servicebus.windows.net` in het vorige voor beeld.

Een SAS-token is geldig voor alle resources die worden voorafgegaan door de `<resourceURI>` gebruikt in de `signature-string` .


## <a name="regenerating-keys"></a>Sleutels opnieuw genereren

Het is raadzaam om regel matig de sleutels te genereren die worden gebruikt in het [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -object. De primaire en secundaire sleutel sleuven bestaan zodat u de sleutels geleidelijk kunt draaien. Als uw toepassing in het algemeen gebruikmaakt van de primaire sleutel, kunt u de primaire sleutel kopiëren naar de tweede sleutel sleuf en de primaire sleutel vervolgens opnieuw genereren. De nieuwe primaire-sleutel waarde kan vervolgens worden geconfigureerd in de client toepassingen, die de toegang tot de oude primaire sleutel in de secundaire sleuf blijven gebruiken. Zodra alle clients zijn bijgewerkt, kunt u de secundaire sleutel opnieuw genereren tot ten slotte de oude primaire sleutel buiten gebruik stellen.

Als u weet of vermoedt dat een sleutel is aangetast en u de sleutels moet intrekken, kunt u zowel de [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) als de [secundaire sleutel](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) van een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)opnieuw genereren en vervangen door nieuwe sleutels. Met deze procedure worden alle tokens die zijn ondertekend met de oude sleutels ongeldig gemaakt.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Verificatie Shared Access Signature met Service Bus

Het scenario dat hieronder wordt beschreven, omvat het configureren van de verificatie regels, het genereren van SAS-tokens en client autorisatie.

Zie [Shared Access Signature Authentication with Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/ManagingEntities/SASAuthorizationRule)voor een voor beeld van een service bus toepassing die de configuratie illustreert en gebruikmaakt van SAS-autorisatie.

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Toegang tot de autorisatie regels voor gedeelde toegang voor een entiteit

Met Service Bus .NET Framework bibliotheken hebt u toegang tot een [micro soft. ServiceBus. Messa ging. SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -object dat is geconfigureerd op een service bus wachtrij of onderwerp via de verzameling [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) in de bijbehorende [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) of [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

De volgende code laat zien hoe u autorisatie regels voor een wachtrij kunt toevoegen.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Shared Access Signature autorisatie gebruiken

Toepassingen die gebruikmaken van de Azure .NET SDK met de Service Bus .NET-bibliotheken, kunnen SAS-autorisatie gebruiken via de [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) -klasse. De volgende code illustreert het gebruik van de token provider om berichten te verzenden naar een Service Bus wachtrij. Als alternatief voor het gebruik dat hier wordt weer gegeven, kunt u ook een eerder uitgegeven token door geven aan de fabrieks methode van de token provider.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

U kunt de token provider ook rechtstreeks voor het uitgeven van tokens gebruiken om aan andere clients door te geven.

Verbindings reeksen kunnen bestaan uit een regel naam (*SharedAccessKeyName*) en regel sleutel (*SharedAccessKey*) of een eerder uitgegeven token (*SharedAccessSignature*). Als deze aanwezig zijn in de connection string die is door gegeven aan een wille keurige constructor of fabrieks methode die een connection string accepteert, wordt de SAS-token provider automatisch gemaakt en gevuld.

Als u SAS-autorisatie wilt gebruiken met Service Bus relays, kunt u SAS-sleutels gebruiken die zijn geconfigureerd voor de Service Bus naam ruimte. Als u expliciet een relay maakt in de naam ruimte ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) met een [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription))-object, kunt u de SAS-regels voor die relay instellen. Als u SAS-autorisatie wilt gebruiken met Service Bus-abonnementen, kunt u SAS-sleutels gebruiken die zijn geconfigureerd op een Service Bus naam ruimte of op een onderwerp.

## <a name="use-the-shared-access-signature-at-http-level"></a>De Shared Access Signature gebruiken (op HTTP-niveau)

Nu u weet hoe u hand tekeningen voor gedeelde toegang kunt maken voor entiteiten in Service Bus, kunt u een HTTP POST uitvoeren:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Houd er rekening mee dat dit voor alles werkt. U kunt SAS maken voor een wachtrij, onderwerp of abonnement.

Als u een SAS-token voor een afzender of client hebt, heeft deze de sleutel niet rechtstreeks en kan de hash niet worden teruggedraaid om deze te verkrijgen. Zo hebt u de controle over wat ze kunnen benaderen, en hoe lang het is. Het is belang rijk te onthouden dat wanneer u de primaire sleutel in het beleid wijzigt, alle gedeelde toegangs handtekeningen die worden gemaakt, ongeldig worden verklaard.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>De Shared Access Signature gebruiken (op AMQP niveau)

In de vorige sectie hebt u gezien hoe u het SAS-token gebruikt met een HTTP POST-aanvraag voor het verzenden van gegevens naar de Service Bus. Zoals u weet, hebt u toegang tot Service Bus met behulp van de Advanced Message Queueing Protocol (AMQP) die het voorkeurs protocol voor prestatie redenen is, in veel scenario's. Het gebruik van SAS-tokens met AMQP wordt beschreven in het document [AMQP Claim-Based Security versie 1,0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) . Dit is een werkend concept sinds 2013, maar wordt nu wel ondersteund door Azure.

Voordat u begint met het verzenden van gegevens naar Service Bus, moet de uitgever het SAS-token in een AMQP-bericht verzenden naar een goed gedefinieerd AMQP-knoop punt met de naam **$CBS** (dit kan worden weer gegeven als een ' speciale ' wachtrij die door de service wordt gebruikt om alle SAS-tokens te verkrijgen en te valideren). De uitgever moet het **ReplyTo** -veld in het AMQP-bericht opgeven. Dit is het knoop punt waarin de service antwoordt op de uitgever met het resultaat van de token validatie (een eenvoudig patroon van aanvraag/antwoord tussen Publisher en service). Dit antwoord knooppunt wordt ' op de hoogte ' gemaakt, zoals beschreven in de AMQP 1,0-specificatie. Nadat u hebt gecontroleerd of het SAS-token geldig is, kan de uitgever door gaan en gegevens verzenden naar de service.

De volgende stappen laten zien hoe u het SAS-token met het AMQP-protocol kunt verzenden met behulp van de [AMQP.net Lite](https://github.com/Azure/amqpnetlite) -bibliotheek. Dit is handig als u de officiële Service Bus SDK niet kunt gebruiken (bijvoorbeeld op WinRT, .NET Compact Framework, .NET Micro Framework en mono) die in C worden ontwikkeld \# . Deze bibliotheek is natuurlijk handig om te begrijpen hoe beveiliging op basis van claims werkt op het niveau van de AMQP, zoals u hebt gezien hoe het werkt op het HTTP-niveau (met een HTTP POST-aanvraag en het SAS-token dat in de header ' autorisatie ' is verzonden). Als u dergelijke uitgebreide kennis over AMQP niet nodig hebt, kunt u de officiële Service Bus SDK gebruiken met .NET Framework toepassingen.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

De `PutCbsToken()` methode ontvangt de *verbinding* (AMQP-verbindings klasse-instantie zoals gegeven door de [AMQP .net Lite-bibliotheek](https://github.com/Azure/amqpnetlite)) die de TCP-verbinding met de service en de *sasToken* -para meter vertegenwoordigt die het SAS-token moet verzenden.

> [!NOTE]
> Het is belang rijk dat de verbinding wordt gemaakt met **sasl-verificatie mechanisme ingesteld op anoniem** (en niet de standaard instelling met gebruikers naam en wacht woord die worden gebruikt wanneer u het SAS-token niet hoeft te verzenden).
>
>

Vervolgens maakt de uitgever twee AMQP-koppelingen voor het verzenden van het SAS-token en het ontvangen van het antwoord (het token validatie resultaat) van de service.

Het AMQP-bericht bevat een aantal eigenschappen en meer informatie dan een eenvoudig bericht. Het SAS-token is de hoofd tekst van het bericht (met behulp van de bijbehorende constructor). De eigenschap **' ReplyTo '** is ingesteld op de naam van het knoop punt voor het ontvangen van het validatie resultaat op de ontvanger koppeling (u kunt de naam ervan wijzigen als u wilt en het dynamisch wordt gemaakt door de service). De laatste drie toepassings-en aangepaste eigenschappen worden door de service gebruikt om aan te geven wat voor soort bewerking het moet worden uitgevoerd. Zoals beschreven in de specificatie van de CBS-concept moet het de **bewerkings naam** ("put-token"), het **type token** (in dit geval, a `servicebus.windows.net:sastoken` ) en de **"naam" van de doel groep** waarop het token van toepassing is (de hele entiteit).

Nadat het SAS-token op de afzender koppeling is verzonden, moet de uitgever het antwoord op de koppeling ontvanger lezen. Het antwoord is een eenvoudig AMQP-bericht met een toepassings eigenschap met de naam **' status-code '** die dezelfde waarden kan bevatten als een HTTP-status code.

## <a name="rights-required-for-service-bus-operations"></a>Benodigde rechten voor het Service Bus van bewerkingen

De volgende tabel bevat de toegangs rechten die zijn vereist voor verschillende bewerkingen op Service Bus resources.

| Bewerking | Claim vereist | Claim bereik |
| --- | --- | --- |
| **Naamruimte** | | |
| Autorisatie regel configureren voor een naam ruimte |Beheren |Elk naam ruimte adres |
| **Service register** | | |
| Privé beleid opsommen |Beheren |Elk naam ruimte adres |
| Beginnen met Luis teren op een naam ruimte |Luisteren |Elk naam ruimte adres |
| Berichten naar een listener verzenden in een naam ruimte |Verzenden |Elk naam ruimte adres |
| **Wachtrij** | | |
| Een wachtrij maken |Beheren |Elk naam ruimte adres |
| Een wachtrij verwijderen |Beheren |Een geldig wachtrij adres |
| Wacht rijen opsommen |Beheren |/$Resources/queues |
| De beschrijving van de wachtrij ophalen |Beheren |Een geldig wachtrij adres |
| Autorisatie regel configureren voor een wachtrij |Beheren |Een geldig wachtrij adres |
| Verzenden naar de wachtrij |Verzenden |Een geldig wachtrij adres |
| Berichten van een wachtrij ontvangen |Luisteren |Een geldig wachtrij adres |
| Berichten afbreken of volt ooien na ontvangst van het bericht in de modus Peek-Lock |Luisteren |Een geldig wachtrij adres |
| Een bericht uitstellen voor later ophalen |Luisteren |Een geldig wachtrij adres |
| Deadletter een bericht |Luisteren |Een geldig wachtrij adres |
| De status ophalen die is gekoppeld aan een berichten wachtrij sessie |Luisteren |Een geldig wachtrij adres |
| De status van een berichten wachtrij sessie instellen |Luisteren |Een geldig wachtrij adres |
| Een bericht plannen voor latere levering; bijvoorbeeld, [ScheduleMessageAsync ()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Luisteren | Een geldig wachtrij adres
| **Onderwerp** | | |
| Een onderwerp maken |Beheren |Elk naam ruimte adres |
| Een onderwerp verwijderen |Beheren |Een geldig onderwerp-adres |
| Onderwerpen opsommen |Beheren |/$Resources/topics |
| De beschrijving van het onderwerp ophalen |Beheren |Een geldig onderwerp-adres |
| Verificatie regel voor een onderwerp configureren |Beheren |Een geldig onderwerp-adres |
| Verzenden naar het onderwerp |Verzenden |Een geldig onderwerp-adres |
| **Abonnement** | | |
| Een abonnement maken |Beheren |Elk naam ruimte adres |
| Abonnement verwijderen |Beheren |.. /myTopic/Subscriptions/mySubscription |
| Abonnementen opsommen |Beheren |.. /myTopic/Subscriptions |
| Beschrijving van abonnement ophalen |Beheren |.. /myTopic/Subscriptions/mySubscription |
| Berichten afbreken of volt ooien na ontvangst van het bericht in de modus Peek-Lock |Luisteren |.. /myTopic/Subscriptions/mySubscription |
| Een bericht uitstellen voor later ophalen |Luisteren |.. /myTopic/Subscriptions/mySubscription |
| Deadletter een bericht |Luisteren |.. /myTopic/Subscriptions/mySubscription |
| De status ophalen die is gekoppeld aan een onderwerps sessie |Luisteren |.. /myTopic/Subscriptions/mySubscription |
| De status die is gekoppeld aan een onderwerps sessie instellen |Luisteren |.. /myTopic/Subscriptions/mySubscription |
| **Regels** | | |
| Een regel maken |Beheren |.. /myTopic/Subscriptions/mySubscription |
| Een regel verwijderen |Beheren |.. /myTopic/Subscriptions/mySubscription |
| Regels opsommen |Beheren of Belui Steren |.. /myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over de Service Bus-berichtenservice

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Service Bus-wachtrijen gebruiken](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com

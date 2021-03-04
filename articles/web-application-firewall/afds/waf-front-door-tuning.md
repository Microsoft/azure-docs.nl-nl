---
title: Retuning Web Application firewall (WAF) voor Azure front deur
description: In dit artikel leert u hoe u de WAF voor de voor deur kunt afstemmen.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: 8752886bc5304de420083212d29ccd3e1cb14084
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043691"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Retuning Web Application firewall (WAF) voor Azure front deur
 
De set met door Azure beheerde standaard regels is gebaseerd op de [OWASP core Rule set (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) en is zodanig ontworpen dat deze strikt uit het vak is. Het is vaak verwacht dat WAF-regels moeten worden afgestemd op de specifieke behoeften van de toepassing of organisatie met behulp van de WAF. Dit wordt vaak bereikt door regel uitsluitingen te definiëren, aangepaste regels te maken en zelfs regels uit te scha kelen die problemen of fout-positieven veroorzaken. Er zijn enkele dingen die u kunt doen als aanvragen die worden door gegeven via uw Web Application firewall (WAF), worden geblokkeerd.

Zorg er eerst voor dat u het [WAF overzicht van de voor deur](afds-overview.md) en het [WAF-beleid voor front-deur](waf-front-door-create-portal.md) documenten hebt gelezen. Zorg er ook voor dat u [WAF controle en logboek registratie](waf-front-door-monitor.md)hebt ingeschakeld. In deze artikelen wordt uitgelegd hoe de WAF functions werkt, hoe de WAF-regel sets werken en hoe u toegang krijgt tot WAF-Logboeken.
 
## <a name="understanding-waf-logs"></a>Informatie over WAF-logboeken
 
Het doel van WAF-Logboeken is om elke aanvraag weer te geven die overeenkomt met of wordt geblokkeerd door de WAF. Het is een verzameling van alle geëvalueerde aanvragen die overeenkomen of worden geblokkeerd. Als u ziet dat de WAF een aanvraag blokkeert die niet (een fout-positief) is, kunt u enkele dingen doen. Eerst beperkt u de specifieke aanvraag en zoekt u deze. Desgewenst kunt u [een aangepast antwoord bericht zo configureren](./waf-front-door-configure-custom-response-code.md) dat dit het `trackingReference` veld bevat om de gebeurtenis eenvoudig te identificeren en een logboek query uit te voeren op die specifieke waarde. Bekijk de logboeken om de specifieke URI, het tijds tempel of het client-IP-adres van de aanvraag te vinden. Wanneer u de gerelateerde logboek vermeldingen vindt, kunt u beginnen met het uitvoeren van onjuiste positieven. 
 
Stel bijvoorbeeld dat u een betrouwbaar verkeer hebt met de teken reeks `1=1` die u wilt door geven aan uw WAF. De aanvraag ziet er als volgt uit:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Als u de aanvraag probeert, blokkeert de WAF het verkeer dat uw reeks van *1 = 1* in een para meter of veld bevat. Dit is een teken reeks die vaak is gekoppeld aan een SQL-injectie aanval. U kunt de logboeken bekijken en de tijds tempel van de aanvraag bekijken en de regels die zijn geblokkeerd/overeenkomen.
 
In het volgende voor beeld verkennen we een `FrontdoorWebApplicationFirewallLog` logboek dat is gegenereerd als gevolg van een overeenkomst van een regel. De volgende Log Analytics query kan worden gebruikt om aanvragen te vinden die in de afgelopen 24 uur zijn geblokkeerd:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
In het `requestUri` veld ziet u dat de aanvraag specifiek is gemaakt `/api/Feedbacks/` . Verder vinden we de regel-ID `942110` in het `ruleName` veld. Als u de regel-ID weet, kunt u naar de [OWASP ModSecurity core-regel set officiële opslag plaats](https://github.com/coreruleset/coreruleset) gaan en deze [regel-id](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) doorzoeken om de code te controleren en precies te begrijpen waarvoor deze regel overeenkomt. 
 
Vervolgens ziet u door het `action` veld te controleren of deze regel is ingesteld op het blok keren van aanvragen bij het vergelijken en bevestigen we dat de aanvraag wordt geblokkeerd door de WAF omdat de `policyMode` is ingesteld op `prevention` . 
 
Nu gaan we de informatie in het veld controleren `details` . Hier ziet u de `matchVariableName` en de `matchVariableValue` informatie. We weten dat deze regel is geactiveerd, omdat iemands invoer *1 = 1* in het `comment` veld van de web-app.
 
```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```
 
Er is ook een waarde bij het controleren van de toegangs logboeken om uw kennis over een bepaalde WAF-gebeurtenis uit te breiden. Hieronder bekijken we het `FrontdoorAccessLog` logboek dat is gegenereerd als reactie op de bovenstaande gebeurtenis.
 
U ziet dat deze gerelateerde logboeken zijn gebaseerd op de `trackingReference` waarde die hetzelfde is. In verschillende velden die algemeen inzicht bieden, zoals `userAgent` en `clientIP` , zullen we de aandacht vestigen op de `httpStatusCode` `httpStatusDetails` velden en. Hier kunnen we bevestigen dat de client een HTTP 403-antwoord heeft ontvangen dat absoluut bevestigt dat deze aanvraag is geweigerd en geblokkeerd. 
 
```json
{
    "time": "2020-09-24T16:43:04.5430764Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorAccessLog",
    "operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
    "properties": {
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "httpMethod": "POST",
        "httpVersion": "1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "requestBytes": "2160",
        "responseBytes": "324",
        "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4183.83 Safari/537.36",
        "clientIp": "1.1.1.1",
        "socketIp": "1.1.1.1",
        "clientPort": "53566",
        "timeToFirstByte": "0.01",
        "timeTaken": "0.011",
        "securityProtocol": "",
        "routingRuleName": "DemoBERoutingRule",
        "rulesEngineMatchNames": [],
        "backendHostname": "13.88.65.130:3000",
        "isReceivedFromClient": true,
        "httpStatusCode": "403",
        "httpStatusDetails": "403",
        "pop": "WST",
        "cacheStatus": "CONFIG_NOCACHE"
    }
}
```

## <a name="resolving-false-positives"></a>Foutieve positieven oplossen
 
Als u een weloverwogen beslissing wilt nemen over het afhandelen van een vals-positief, is het belang rijk om vertrouwd te raken met de technologieën die uw toepassing gebruikt. Stel bijvoorbeeld dat er geen SQL-Server is in uw technologie stack en dat er valse positieven worden ontvangen die aan deze regels zijn gerelateerd. Als u deze regels uitschakelt, wordt uw beveiliging niet noodzakelijkerwijs verzwakt. 

Met deze informatie en de kennis die regel 942110 is die overeenkomt met de `1=1` teken reeks in ons voor beeld, kunnen we enkele dingen doen om te voor komen dat deze rechtmatige aanvraag wordt geblokkeerd:
 
* Uitsluitings lijsten gebruiken
  * Zie [WAF (Web Application firewall) met uitsluitingen voor de front-deur service](waf-front-door-exclusion.md) voor meer informatie over uitsluitings lijsten. 
* WAF-acties wijzigen
  * Zie [WAF-acties](afds-overview.md#waf-actions) voor meer informatie over welke acties kunnen worden uitgevoerd wanneer een aanvraag overeenkomt met de voor waarden van een regel.
* Aangepaste regels gebruiken
  * Zie [aangepaste regels voor Web Application Firewall met Azure front deur](waf-front-door-custom-rules.md) voor meer informatie over aangepaste regels.
* Regels uitschakelen 

> [!TIP]
> Wanneer u een benadering selecteert om legitieme aanvragen via de WAF toe te staan, kunt u deze zo smal maken als u wilt. Het is bijvoorbeeld beter om een uitsluitings lijst te gebruiken dan helemaal een regel uit te scha kelen.

### <a name="using-exclusion-lists"></a>Uitsluitings lijsten gebruiken

Een voor deel van het gebruik van een uitsluitings lijst is dat alleen de overeenkomende variabele die u hebt geselecteerd om op te nemen, niet meer voor die aanvraag wordt gecontroleerd. Dat wil zeggen dat u kunt kiezen tussen specifieke aanvraag headers, aanvraag cookies, query reeks argumenten of bericht hoofdtekst post argumenten die moeten worden uitgesloten als aan een bepaalde voor waarde wordt voldaan, in tegens telling tot het uitsluiten van de volledige aanvraag om te worden geïnspecteerd. De andere niet-opgegeven variabelen van de aanvraag worden gewoon gecontroleerd.
 
Het is belang rijk om te overwegen dat uitsluitingen een globale instelling zijn. Dit betekent dat de geconfigureerde uitsluiting geldt voor al het verkeer dat via uw WAF wordt door gegeven, niet alleen een specifieke Web-app of-URI. Dit kan bijvoorbeeld een probleem zijn als *1 = 1* een geldige aanvraag is in de hoofd tekst van een bepaalde web-app, maar niet voor anderen onder hetzelfde WAF-beleid. Als het zinvol is om verschillende uitsluitings lijsten te gebruiken voor verschillende toepassingen, kunt u overwegen om verschillende WAF-beleids regels te gebruiken voor elke toepassing en deze toe te passen op de front-end van elke toepassing.
 
Wanneer u uitsluitings lijsten voor beheerde regels configureert, kunt u ervoor kiezen om alle regels in een regelset, alle regels in een regel groep of een afzonderlijke regel uit te sluiten. Een uitsluitings lijst kan worden geconfigureerd met behulp van [Power shell](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject?view=azps-4.7.0&viewFallbackFrom=azps-3.5.0), [Azure cli](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/exclusion?view=azure-cli-latest#ext_front_door_az_network_front_door_waf_policy_managed_rules_exclusion_add), [rest API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)of de Azure Portal.

* Uitsluitingen op regel niveau
  * Het Toep assen van uitsluitingen op regel niveau betekent dat de opgegeven uitsluitingen niet alleen worden geanalyseerd op die afzonderlijke regel, terwijl deze nog steeds wordt geanalyseerd door alle andere regels in de regelset. Dit is het meest gedetailleerde niveau voor uitsluitingen en kan worden gebruikt voor het afstemmen van de beheerde regelset op basis van de informatie die u in de WAF-Logboeken vindt bij het oplossen van problemen met een gebeurtenis.
* Uitsluitingen op regel groeps niveau
  * Uitsluitingen Toep assen op een regel groeps niveau betekent dat de opgegeven uitsluitingen niet worden geanalyseerd op basis van de specifieke set regel typen. Als u bijvoorbeeld *SQLI* als een uitgesloten regel groep selecteert, worden de gedefinieerde uitsluitingen van aanvragen niet geïnspecteerd door een van de SQLI-specifieke regels, maar ze worden toch gecontroleerd door regels in andere groepen, zoals *php*, *RFI* of *XSS*. Dit type uitsluiting kan nuttig zijn wanneer u zeker weet dat de toepassing niet vatbaar is voor specifieke typen aanvallen. Een toepassing die geen SQL-data bases heeft, kan bijvoorbeeld alle *SQLI* -regels uitsluiten zonder dat het beveiligings niveau ervan wordt afbreuk.
* Uitsluitingen bij het niveau van de regel set 
  * Uitsluitingen Toep assen op een regel niveau: de opgegeven uitsluitingen worden niet geanalyseerd op basis van de beveiligings regels die in die regelset beschikbaar zijn. Dit is een uitgebreide uitsluiting en moet daarom zorgvuldig worden gebruikt.

In dit voor beeld wordt een uitzonde ring op het meest gedetailleerde niveau uitgevoerd (waarbij uitsluiting wordt toegepast op één regel) en we gaan de overeenkomst met de **hoofd tekst** van de aanvraag afstemmen uitsluiten die bevat `comment` . Dit is duidelijk omdat u de variabele Details in het logboek firewall kunt zien: `"matchVariableName": "PostParamValue:comment"` . Het kenmerk is `comment` . U kunt deze kenmerk naam ook op een aantal andere manieren vinden. Zie [aanvraag kenmerk namen zoeken](#finding-request-attribute-names).

![Uitsluitings regels](../media/waf-front-door-tuning/exclusion-rules.png)

![Regel uitsluiting voor specifieke regel](../media/waf-front-door-tuning/exclusion-rule.png)

Soms zijn er gevallen waarin specifieke para meters worden door gegeven aan de WAF op een manier die mogelijk niet intuïtief is. Er is bijvoorbeeld een token dat wordt door gegeven wanneer wordt geverifieerd met behulp van Azure Active Directory. Dit token, `__RequestVerificationToken` , wordt meestal als een aanvraag cookie door gegeven. In sommige gevallen, waarbij cookies echter worden uitgeschakeld, wordt dit token ook als een aanvraag post-argument door gegeven. Daarom moet u, om ervoor te zorgen dat het token fout-positieven van Azure AD voldoet aan `__RequestVerificationToken` de uitsluitings lijst voor zowel `RequestCookieNames` als `RequestBodyPostArgsNames` .

Uitsluitingen voor een veld naam (*selector*) betekent dat de waarde niet langer door de WAF wordt geëvalueerd. De veld naam zelf blijft echter worden geëvalueerd en in zeldzame gevallen kan deze overeenkomen met een WAF-regel en een actie activeren.

![Regel uitsluiting voor regelset](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>WAF-acties wijzigen

Een andere manier om het gedrag van WAF-regels te verwerken, is door de actie te kiezen die wordt ondernomen wanneer een aanvraag overeenkomt met de voor waarden van een regel. De beschik bare acties zijn: [toestaan, blok keren, aanmelden en omleiden](afds-overview.md#waf-actions).

In dit voor beeld hebben we de standaard actie *geblokkeerd* gewijzigd in de *logboek* actie voor regel 942110. Dit leidt ertoe dat de WAF de aanvraag registreert en door gaan met het evalueren van dezelfde aanvraag met de regels voor de resterende lagere prioriteit.

![WAF-acties](../media/waf-front-door-tuning/actions.png)

Nadat u dezelfde aanvraag hebt uitgevoerd, kunt u teruggaan naar de logboeken. we zien dat deze aanvraag overeenkomt met de regel-ID 942110 en dat het `action_s` veld nu het *logboek* aanduidt in plaats van *blok keren*. Vervolgens hebben we de logboek query uitgebreid om de informatie op te nemen `trackingReference_s` en te zien wat er nog meer is gebeurd met deze aanvraag.

![Logboek met meerdere regel overeenkomsten](../media/waf-front-door-tuning/actions-log.png)

In het algemeen zien we dat er in een andere SQLI-regel een overeenkomst wordt weer gegeven in milliseconden nadat de regel-ID 942110 is verwerkt. Dezelfde aanvraag komt overeen met regel-ID 942310 en deze keer dat het standaard actie *blok* is geactiveerd.

Een ander voor deel van het gebruik van de *logboek* actie tijdens het afstemmen of oplossen van problemen is dat u kunt bepalen of meerdere regels binnen een specifieke regel groep overeenkomen en een bepaalde aanvraag blok keren. U kunt de uitsluitingen vervolgens op het juiste niveau maken, dat wil zeggen op het niveau van de regel of regel groep. 

### <a name="using-custom-rules"></a>Aangepaste regels gebruiken

Zodra u hebt vastgesteld wat een overeenkomst met een WAF-regel veroorzaakt, kunt u aangepaste regels gebruiken om de manier aan te passen waarop de WAF reageert op de gebeurtenis. Aangepaste regels worden verwerkt vóór beheerde regels, ze kunnen meer dan één voor waarde bevatten en hun acties kunnen [toestaan, weigeren, Logboeken of omleiden](afds-overview.md#waf-actions). Wanneer er een overeenkomende regel is, wordt de verwerking van de WAF-engine gestopt. Dit betekent dat er geen andere aangepaste regels met een lagere prioriteit en beheerde regels meer worden uitgevoerd.

In het onderstaande voor beeld is een aangepaste regel gemaakt met twee voor waarden. De eerste voor waarde zoekt naar de `comment` waarde in de hoofd tekst van de aanvraag. De tweede voor waarde zoekt naar de `/api/Feedbacks/` waarde in de aanvraag-URI.

Met behulp van een aangepaste regel kunt u het meest gedetailleerd zijn bij het afstemmen van uw WAF-regels en voor het omgaan met valse positieven. In dit geval nemen we geen actie op basis van de `comment` aanvraag hoofdtekst waarde, die mogelijk voor meerdere sites of apps onder hetzelfde WAF-beleid bestaat. Door een andere voor waarde op te nemen die ook overeenkomt met een bepaalde aanvraag `/api/Feedbacks/` -URI, zorgen we ervoor dat deze aangepaste regel echt van toepassing is op dit expliciete use-case dat we gecontroleerden. Dit zorgt ervoor dat dezelfde aanval, indien uitgevoerd tegen verschillende omstandigheden, nog steeds wordt geïnspecteerd en wordt voor komen door de WAF-engine.

![Logboek](../media/waf-front-door-tuning/custom-rule.png)

Bij het verkennen van het logboek ziet u dat het `ruleName_s` veld de naam bevat die is opgegeven voor de aangepaste regel die we hebben gemaakt: `redirectcomment` . In het `action_s` veld kunt u zien dat de *omleidings* actie voor deze gebeurtenis is uitgevoerd. In het `details_matches_s` veld ziet u dat de Details voor beide voor waarden overeenkomen.

### <a name="disabling-rules"></a>Regels uitschakelen

Een andere manier om te zien wat een fout positief is, is door de regel uit te scha kelen die overeenkomt met de ingevoerde invoer die schadelijk is voor WAF. Omdat u de WAF-Logboeken hebt geparseerd en de regel omlaag hebt geverfijn tot 942110, kunt u deze uitschakelen in de Azure Portal. Zie [de firewall regels voor web-apps aanpassen met behulp van de Azure Portal](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Het uitschakelen van een regel is een voor deel wanneer u er zeker van bent dat alle aanvragen die aan deze specifieke voor waarde voldoen, rechtmatige aanvragen zijn of wanneer u zeker weet dat de regel niet van toepassing is op uw omgeving (zoals het uitschakelen van een SQL-injectie regel omdat u niet-SQL-back-endservers hebt). 
 
Het uitschakelen van een regel is echter een algemene instelling die van toepassing is op alle frontend-hosts die zijn gekoppeld aan het WAF-beleid. Wanneer u ervoor kiest om een regel uit te scha kelen, worden er mogelijk beveiligings lekken weer gegeven zonder beveiliging of detectie voor andere frontend-hosts die zijn gekoppeld aan het WAF-beleid.
 
Als u Azure PowerShell wilt gebruiken om een beheerde regel uit te scha kelen, raadpleegt u de documentatie van het [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject?preserve-view=true&view=azps-4.7.0) object. Als u Azure CLI wilt gebruiken, raadpleegt u de [`az network front-door waf-policy managed-rules override`](/cli/azure/ext/front-door/network/front-door/waf-policy/managed-rules/override?preserve-view=true&view=azure-cli-latest) documentatie.

![WAF-regels](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> Het is een goed idee om de wijzigingen die u in uw WAF-beleid aanbrengt, te documenteren. Voeg voorbeeld aanvragen toe om de foutieve positieve detectie te illustreren en leg duidelijk uit waarom u een aangepaste regel hebt toegevoegd, een regel of regelset hebt uitgeschakeld of een uitzonde ring hebt toegevoegd. Deze documentatie kan handig zijn als u uw toepassing in de toekomst opnieuw ontwerpt en u wilt controleren of uw wijzigingen nog geldig zijn. Het kan ook helpen als u ooit hebt gecontroleerd of als u wilt uitvullen waarom u het WAF-beleid opnieuw hebt geconfigureerd op basis van de standaard instellingen.

## <a name="finding-request-fields"></a>Aanvraag velden zoeken

Met een browser proxy zoals [Fiddler](https://www.telerik.com/fiddler)kunt u afzonderlijke aanvragen controleren en bepalen welke specifieke velden van een webpagina worden genoemd. Dit is handig wanneer we bepaalde velden moeten uitsluiten van inspectie met behulp van uitsluitings lijsten in WAF.

### <a name="finding-request-attribute-names"></a>Kenmerk namen van aanvragen zoeken
 
In dit voor beeld ziet u het veld waarin de `1=1` teken reeks is ingevoerd `comment` . Deze gegevens zijn door gegeven in de hoofd tekst van een POST-aanvraag.

![Fiddler-aanvraag met hoofd tekst](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Dit is een veld dat u kunt uitsluiten. Zie voor meer informatie over uitsluitings lijsten [Web Application firewall-uitsluitings lijsten](./waf-front-door-exclusion.md). U kunt de evaluatie in dit geval uitsluiten door de volgende uitzonde ring te configureren:

![Uitsluitings regel](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

U kunt ook de logboeken van de firewall bekijken om de informatie op te halen om te zien wat u moet toevoegen aan de uitsluitings lijst. Zie [metrische gegevens en logboeken bewaken in azure front deur](./waf-front-door-monitor.md)voor meer informatie over het inschakelen van logboek registratie.

Bekijk het firewall logboek in het `PT1H.json` bestand voor het uur dat de aanvraag die u wilt controleren, heeft plaatsgevonden. `PT1H.json` Er zijn bestanden beschikbaar in de containers van het opslag account waar de `FrontDoorWebApplicationFirewallLog` en de `FrontDoorAccessLog` Diagnostische logboeken worden opgeslagen.

In dit voor beeld ziet u de regel die de aanvraag heeft geblokkeerd (met dezelfde transactie referentie) en op exact dezelfde tijd heeft plaatsgevonden:

```json
{
    "time": "2020-09-24T16:43:04.5422943Z",
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/<Resource Group Name>/PROVIDERS/MICROSOFT.NETWORK/FRONTDOORS/AFDWAFDEMOSITE",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoor/WebApplicationFirewallLog/Write",
    "properties": {
        "clientIP": "1.1.1.1",
        "clientPort": "53566",
        "socketIP": "1.1.1.1",
        "requestUri": "http://afdwafdemosite.azurefd.net:80/api/Feedbacks/",
        "ruleName": "DefaultRuleSet-1.0-SQLI-942110",
        "policy": "AFDWAFDemoPolicy",
        "action": "Block",
        "host": "afdwafdemosite.azurefd.net",
        "trackingReference": "0mMxsXwAAAABEalekYeI4S55qpi5R7R0/V1NURURHRTA4MTIAZGI4NGQzZDgtNWQ5Ny00ZWRkLTg2ZGYtZDJjNThlMzI2N2I4",
        "policyMode": "prevention",
        "details": {
            "matches": [
                {
                    "matchVariableName": "PostParamValue:comment",
                    "matchVariableValue": "\"1=1\""
                }
            ],
            "msg": "SQL Injection Attack: Common Injection Testing Detected",
            "data": "Matched Data: \"1=1\" found within PostParamValue:comment: \"1=1\""
        }
    }
}
```

Met uw kennis van hoe de door Azure beheerde regel sets werken (Zie [Web Application firewall op de Azure front deur](afds-overview.md)) weet u dat de regel met de *actie:* de eigenschap Block wordt geblokkeerd op basis van de gegevens die in de aanvraag tekst worden gevonden. U kunt in de details zien dat deze overeenkomt met een patroon ( `1=1` ), en het veld heeft de naam `comment` . Volg dezelfde vorige stappen om de naam van de hoofd tekst van het aanvraag bericht met te sluiten `comment` .

### <a name="finding-request-header-names"></a>Namen van aanvraag headers zoeken

Fiddler is opnieuw een handig hulp programma om namen van aanvraag headers te vinden. In de volgende scherm afbeelding ziet u de kopteksten voor deze GET-aanvraag, waaronder content-type, User-agent, enzovoort. U kunt ook aanvraag headers gebruiken om uitsluitingen en aangepaste regels te maken in WAF.

![Fiddler-aanvraag met koptekst](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Een andere manier om aanvraag-en antwoord headers weer te geven, is door te zoeken in de ontwikkel hulpprogramma's van uw browser, zoals Edge of Chrome. U kunt op F12 drukken of met de rechter muisknop > Ontwikkelhulpprogramma's **inspecteren**  ->  en het tabblad **netwerk** selecteren. Laad een webpagina en klik op de aanvraag die u wilt inspecteren.

![Netwerk controle-aanvraag](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Cookie namen van aanvragen zoeken

Als de aanvraag cookies bevat, kan het tabblad cookies worden geselecteerd om ze weer te geven in Fiddler. Cookie-informatie kan ook worden gebruikt voor het maken van uitsluitingen of aangepaste regels in WAF.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Web Application Firewall](../overview.md).
- Lees hoe u [een Front Door maakt](../../frontdoor/quickstart-create-front-door.md).

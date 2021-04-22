---
title: Afstemmen Web Application Firewall (WAF) voor Azure Front Door
description: In dit artikel leert u hoe u de WAF kunt afstemmen voor Front Door.
services: web-application-firewall
author: mohitkusecurity
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: mohitku
ms.reviewer: tyao
ms.openlocfilehash: c0879edc0e3fbd6cf6bcadc26dd862f95ecf4fd4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872349"
---
# <a name="tuning-web-application-firewall-waf-for-azure-front-door"></a>Afstemmen Web Application Firewall (WAF) voor Azure Front Door
 
De door Azure beheerde standaardregelset is gebaseerd op de [OWASP Core Rule Set (CRS)](https://github.com/SpiderLabs/owasp-modsecurity-crs/tree/v3.1/dev) en is standaard ontworpen om strikt te zijn. Vaak wordt verwacht dat WAF-regels moeten worden afgestemd op de specifieke behoeften van de toepassing of organisatie die de WAF gebruikt. Dit wordt meestal bereikt door regeluitsluitingen te definiëren, aangepaste regels te maken en zelfs regels uit te stellen die mogelijk problemen of fout-positieven veroorzaken. Er zijn enkele dingen die u kunt doen als aanvragen die via uw Web Application Firewall (WAF) moeten worden geblokkeerd.

Zorg er eerst voor dat u het [overzicht van Front Door WAF en](afds-overview.md) het [WAF-beleid](waf-front-door-create-portal.md) voor Front Door hebt gelezen. Zorg er ook voor dat u [WAF-bewaking en -logboekregistratie hebt ingeschakeld.](waf-front-door-monitor.md) In deze artikelen wordt uitgelegd hoe de WAF werkt, hoe de WAF-regelsets werken en hoe u toegang krijgt tot WAF-logboeken.
 
## <a name="understanding-waf-logs"></a>WAF-logboeken begrijpen
 
Het doel van WAF-logboeken is om elke aanvraag weer te geven die door de WAF wordt gematcht of geblokkeerd. Het is een verzameling van alle geëvalueerde aanvragen die worden gematcht of geblokkeerd. Als u merkt dat de WAF een aanvraag blokkeert die niet mag (een fout-positief), kunt u een aantal dingen doen. Zoek eerst de specifieke aanvraag. Indien gewenst kunt u [een](./waf-front-door-configure-custom-response-code.md) aangepast antwoordbericht configureren om het veld op te nemen om de gebeurtenis gemakkelijk te identificeren en een logboekquery uit te voeren op die `trackingReference` specifieke waarde. Bekijk de logboeken om de specifieke URI, tijdstempel of client-IP van de aanvraag te vinden. Wanneer u de gerelateerde logboekgegevens vindt, kunt u beginnen met het reageren op fout-positieven. 
 
Stel dat u een legitiem verkeer hebt met de tekenreeks `1=1` die u via uw WAF wilt doorgeven. De aanvraag ziet er als volgende uit:

```
POST http://afdwafdemosite.azurefd.net/api/Feedbacks HTTP/1.1
Host: afdwafdemosite.azurefd.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 55

UserId=20&captchaId=7&captchaId=15&comment="1=1"&rating=3
```

Als u de aanvraag probeert, blokkeert de WAF verkeer dat uw *tekenreeks van 1=1* in een parameter of veld bevat. Dit is een tekenreeks die vaak is gekoppeld aan een SQL-injectieaanval. U kunt de logboeken bekijken en de tijdstempel van de aanvraag en de regels zien die zijn geblokkeerd/overeenkomen.
 
In het volgende voorbeeld verkennen we een logboek `FrontdoorWebApplicationFirewallLog` dat is gegenereerd vanwege een regelmatch. De volgende Log Analytics-query kan worden gebruikt om aanvragen te vinden die in de afgelopen 24 uur zijn geblokkeerd:

```kusto
AzureDiagnostics
| where Category == 'FrontdoorWebApplicationFirewallLog'
| where TimeGenerated > ago(1d)
| where action_s == 'Block'

```
 
In het `requestUri` veld ziet u dat de aanvraag specifiek aan is `/api/Feedbacks/` gedaan. Verderop vinden we de regel-id `942110` in het `ruleName` veld . Als u de regel-id kent, kunt u naar de officiële opslagplaats [](https://github.com/coreruleset/coreruleset/blob/v3.1/dev/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf) [OWASP ModSecurity Core Rule Set](https://github.com/coreruleset/coreruleset) gaan en zoeken op die regel-id om de code te controleren en precies te begrijpen op welke code deze regel van pas komt. 
 
Vervolgens zien we, door het veld te controleren, dat deze regel is ingesteld op het blokkeren van aanvragen bij het afstemmen, en we bevestigen dat de aanvraag in feite is geblokkeerd door de WAF omdat de is ingesteld `action` `policyMode` op `prevention` . 
 
Laten we nu de informatie in het veld `details` controleren. Hier kunt u de informatie en `matchVariableName` `matchVariableValue` zien. We leren dat deze regel is geactiveerd omdat iemand *1=1* heeft ingevoerd in het `comment` veld van de web-app.
 
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
 
Het is ook belangrijk om de toegangslogboeken te controleren om uw kennis over een bepaalde WAF-gebeurtenis uit te breiden. Hieronder bekijken we het `FrontdoorAccessLog` logboek dat is gegenereerd als reactie op de bovenstaande gebeurtenis.
 
U kunt zien dat dit gerelateerde logboeken zijn op basis van `trackingReference` de waarde die hetzelfde is. Onder verschillende velden die algemeen inzicht bieden, zoals `userAgent` en , wordt de aandacht vestigen op de velden en `clientIP` `httpStatusCode` `httpStatusDetails` . Hier kunnen we bevestigen dat de client een HTTP 403-antwoord heeft ontvangen, dat absoluut bevestigt dat deze aanvraag is geweigerd en geblokkeerd. 
 
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

## <a name="resolving-false-positives"></a>Fout-positieven oplossen
 
Als u een weloverwogen beslissing wilt nemen over het verwerken van een fout-positief, is het belangrijk om vertrouwd te raken met de technologieën die uw toepassing gebruikt. Stel bijvoorbeeld dat uw technologiestack geen SQL-server heeft en dat u fout-positieven krijgt met betrekking tot deze regels. Het uitschakelen van deze regels betekent niet noodzakelijkerwijs dat uw beveiliging in de weg zit. 

Met deze informatie, en de wetenschap dat regel 942110 de regel is die overeen komt met de tekenreeks in ons voorbeeld, kunnen we een aantal dingen doen om te voorkomen dat deze legitieme aanvraag wordt `1=1` geblokkeerd:
 
* Uitsluitingslijsten gebruiken
  * Zie [Web Application Firewall (WAF) met Front Door Service-uitsluitingslijsten](waf-front-door-exclusion.md) voor meer informatie over uitsluitingslijsten. 
* WAF-acties wijzigen
  * Zie [WAF Actions (WAF-acties)](afds-overview.md#waf-actions) voor meer informatie over welke acties kunnen worden ondernomen wanneer een aanvraag overeenkomt met de voorwaarden van een regel.
* Aangepaste regels gebruiken
  * Zie [Aangepaste regels voor Web Application Firewall met Azure Front Door](waf-front-door-custom-rules.md) voor meer informatie over aangepaste regels.
* Regels uitschakelen 

> [!TIP]
> Wanneer u een benadering selecteert om legitieme aanvragen via de WAF toe te staan, probeert u dit zo beperkt mogelijk te maken. Het is bijvoorbeeld beter om een uitsluitingslijst te gebruiken dan een regel volledig uit te sluiten.

### <a name="using-exclusion-lists"></a>Uitsluitingslijsten gebruiken

Een voordeel van het gebruik van een uitsluitingslijst is dat alleen de overeenkomstvariabele die u wilt uitsluiten, niet meer wordt geïnspecteerd voor die bepaalde aanvraag. Dat wil zeggen dat u kunt kiezen tussen specifieke aanvraagheaders, aanvraagcookies, queryreeksargumenten of aanvraagteksttekstargumenten die moeten worden uitgesloten als aan een bepaalde voorwaarde wordt voldaan, in plaats van de hele aanvraag uit te sluiten van inspectie. De andere niet-opgegeven variabelen van de aanvraag worden nog steeds normaal geïnspecteerd.
 
Het is belangrijk om te overwegen dat uitsluitingen een globale instelling zijn. Dit betekent dat de geconfigureerde uitsluiting van toepassing is op al het verkeer dat uw WAF passeert, niet alleen op een specifieke web-app of URI. Dit kan bijvoorbeeld een probleem zijn als *1 =1* een geldige aanvraag is in de body voor een bepaalde web-app, maar niet voor anderen onder hetzelfde WAF-beleid. Als het zinvol is om verschillende uitsluitingslijsten voor verschillende toepassingen te gebruiken, kunt u voor elke toepassing verschillende WAF-beleidsregels gebruiken en deze toepassen op de front-end van elke toepassing.
 
Bij het configureren van uitsluitingslijsten voor beheerde regels kunt u ervoor kiezen om alle regels binnen een regelset, alle regels binnen een regelgroep of een afzonderlijke regel uit te sluiten. Een uitsluitingslijst kan worden geconfigureerd met [Behulp van PowerShell,](/powershell/module/az.frontdoor/New-AzFrontDoorWafManagedRuleExclusionObject) [Azure CLI,](/cli/azure/network/front-door/waf-policy/managed-rules/exclusion#az_network_front_door_waf_policy_managed_rules_exclusion_add) [REST API](/rest/api/frontdoorservice/webapplicationfirewall/policies/createorupdate)of de Azure Portal.

* Uitsluitingen op regelniveau
  * Uitsluitingen toepassen op regelniveau betekent dat de opgegeven uitsluitingen niet alleen worden geanalyseerd op basis van die afzonderlijke regel, terwijl ze nog steeds worden geanalyseerd door alle andere regels in de regelset. Dit is het meest gedetailleerde niveau voor uitsluitingen en kan worden gebruikt om de beheerde regelset af te stemmen op basis van de informatie die u in de WAF-logboeken vindt bij het oplossen van problemen met een gebeurtenis.
* Uitsluitingen op het niveau van regelgroep
  * Uitsluitingen toepassen op het niveau van een regelgroep betekent dat de opgegeven uitsluitingen niet worden geanalyseerd op die specifieke set regeltypen. Als u *bijvoorbeeld SQLI* selecteert als uitgesloten regelgroep, wordt aangegeven dat de gedefinieerde aanvraaguitsluitingen niet worden geïnspecteerd door een van de SQLI-specifieke regels, maar dat deze nog steeds worden geïnspecteerd door regels in andere groepen, zoals *PHP,* *RFI* of *XSS.* Dit type uitsluiting kan nuttig zijn wanneer we zeker weten dat de toepassing niet vatbaar is voor specifieke typen aanvallen. Een toepassing die geen SQL-databases heeft, kan bijvoorbeeld alle *SQLI-regels* uitsluiten zonder dat dit nadelig is voor het beveiligingsniveau.
* Uitsluitingen op regelsetniveau 
  * Uitsluitingen toepassen op een regelsetniveau betekent dat de opgegeven uitsluitingen niet worden geanalyseerd op een van de beveiligingsregels die beschikbaar zijn in die regelset. Dit is een uitgebreide uitsluiting, dus deze moet zorgvuldig worden gebruikt.

In dit voorbeeld voeren we een uitsluiting uit op het meest gedetailleerde niveau (uitsluiting toepassen op één regel) en willen we de overeenkomstvariabele Aanvraag body **post args** name die `comment` bevat uitsluiten. Dit is duidelijk omdat u de overeenkomende variabeledetails in het firewalllogboek kunt zien: `"matchVariableName": "PostParamValue:comment"` . Het kenmerk is `comment` . U kunt deze kenmerknaam ook op een paar andere manieren vinden. Zie [Aanvraagkenmerknamen zoeken.](#finding-request-attribute-names)

![Uitsluitingsregels](../media/waf-front-door-tuning/exclusion-rules.png)

![Regeluitsluiting voor specifieke regel](../media/waf-front-door-tuning/exclusion-rule.png)

Af en toe zijn er gevallen waarbij specifieke parameters worden doorgegeven aan de WAF op een manier die mogelijk niet intuïtief is. Er is bijvoorbeeld een token dat wordt doorgegeven bij het authenticeren met behulp Azure Active Directory. Dit token, `__RequestVerificationToken` , wordt meestal doorgegeven als een aanvraagcookie. In sommige gevallen waarin cookies zijn uitgeschakeld, wordt dit token echter ook doorgegeven als argument na een aanvraag. Daarom moet u ervoor zorgen dat wordt toegevoegd aan de uitsluitingslijst voor zowel als om fout-positieven voor Het Azure AD-token `__RequestVerificationToken` `RequestCookieNames` aan te `RequestBodyPostArgsNames` pakken.

Uitsluitingen voor een veldnaam *(selector)* betekent dat de waarde niet meer wordt geëvalueerd door de WAF. De veldnaam zelf wordt echter nog steeds geëvalueerd en in zeldzame gevallen kan deze overeenkomen met een WAF-regel en een actie activeren.

![Regeluitsluiting voor regelset](../media/waf-front-door-tuning/exclusion-rule-selector.png)

### <a name="changing-waf-actions"></a>WAF-acties wijzigen

Een andere manier om het gedrag van WAF-regels te verwerken, is door de actie te kiezen die moet worden ondernomen wanneer een aanvraag overeenkomt met de voorwaarden van een regel. De beschikbare acties zijn: [Toestaan, Blokkeren, Logboek en Omleiden.](afds-overview.md#waf-actions)

In dit voorbeeld hebben we de standaardactie *Blokkeren gewijzigd* in de actie *Logboek* op regel 942110. Dit zorgt ervoor dat de WAF de aanvraag vast moet maken en dezelfde aanvraag blijft evalueren op basis van de resterende regels met een lagere prioriteit.

![WAF-acties](../media/waf-front-door-tuning/actions.png)

Nadat dezelfde aanvraag is gedaan, kunnen we teruggaan naar de logboeken en zien we dat deze aanvraag een overeenkomst is op regel-id 942110 en dat het veld nu Logboek in plaats van Blokkeren `action_s` aangeeft.   Vervolgens hebben we de logboekquery uitgebreid om de informatie op `trackingReference_s` te nemen en te zien wat er nog meer is gebeurd met deze aanvraag.

![Logboek met meerdere regel-overeenkomsten](../media/waf-front-door-tuning/actions-log.png)

Interessant is dat er een andere SQLI-regelmatch plaatsvindt in milliseconden nadat regel-id 942110 is verwerkt. Dezelfde aanvraag komt overeen met regel-id 942310 en deze keer is de standaardactie *Blokkeren* geactiveerd.

Een ander voordeel  van het gebruik van de actie Logboek tijdens het afstemmen of oplossen van problemen met WAF is dat u kunt bepalen of meerdere regels binnen een specifieke regelgroep overeenkomen en een bepaalde aanvraag blokkeren. Vervolgens kunt u uw uitsluitingen op het juiste niveau maken, dat wil zeggen op het niveau van de regel of regelgroep. 

### <a name="using-custom-rules"></a>Aangepaste regels gebruiken

Zodra u hebt vastgesteld wat de oorzaak is van een overeenkomst met een WAF-regel, kunt u aangepaste regels gebruiken om aan te passen hoe de WAF op de gebeurtenis reageert. Aangepaste regels worden verwerkt vóór beheerde regels. Ze kunnen meer dan één voorwaarde bevatten en hun acties kunnen [Toestaan, Weigeren, Logboek of Omleiden zijn.](afds-overview.md#waf-actions) Wanneer er een overeenkomst is met een regel, stopt de WAF-engine met verwerken. Dit betekent dat andere aangepaste regels met een lagere prioriteit en beheerde regels niet meer worden uitgevoerd.

In het onderstaande voorbeeld hebben we een aangepaste regel met twee voorwaarden gemaakt. De eerste voorwaarde zoekt naar de `comment` waarde in de aanvraag body. De tweede voorwaarde zoekt naar de `/api/Feedbacks/` waarde in de aanvraag-URI.

Door een aangepaste regel te gebruiken, kunt u de meest gedetailleerde zijn bij het afstemmen van uw WAF-regels en het omgaan met fout-positieven. In dit geval ondernemen we niet alleen actie op basis van de aanvraagwaarde voor de aanvraag, die kan bestaan op meerdere sites of apps onder `comment` hetzelfde WAF-beleid. Door een andere voorwaarde op te nemen die ook op een bepaalde aanvraag-URI moet overeenkomen, zorgen we ervoor dat deze aangepaste regel echt van toepassing is op deze expliciete `/api/Feedbacks/` use-case die we hebben onderzocht. Dit zorgt ervoor dat dezelfde aanval, indien uitgevoerd tegen verschillende omstandigheden, nog steeds wordt geïnspecteerd en voorkomen door de WAF-engine.

![Logboek](../media/waf-front-door-tuning/custom-rule.png)

Wanneer u het logboek verkent, kunt u zien dat het veld de naam bevat die is opgegeven `ruleName_s` voor de aangepaste regel die we hebben gemaakt: `redirectcomment` . In het `action_s` veld ziet u dat de omleidingsactie is ondernomen voor deze gebeurtenis.  In het `details_matches_s` veld ziet u dat de details van beide voorwaarden overeenkomen.

### <a name="disabling-rules"></a>Regels uitschakelen

Een andere manier om een fout-positief te krijgen, is door de regel uit te schakelen die overeen kwam met de invoer die volgens de WAF schadelijk was. Omdat u de WAF-logboeken hebt geparseerd en de regel hebt beperkt tot 942110, kunt u deze uitschakelen in de Azure Portal. Zie [Customize Web Application Firewall rules using the Azure Portal](../ag/application-gateway-customize-waf-rules-portal.md#disable-rule-groups-and-rules).
 
Het uitschakelen van een regel is een voordeel wanneer u zeker weet dat alle aanvragen die voldoen aan die specifieke voorwaarde in feite legitieme aanvragen zijn, of wanneer u zeker weet dat de regel niet van toepassing is op uw omgeving (zoals het uitschakelen van een SQL-injectieregel omdat u niet-SQL-back-enden hebt). 
 
Het uitschakelen van een regel is echter een globale instelling die van toepassing is op alle front-endhosts die zijn gekoppeld aan het WAF-beleid. Wanneer u ervoor kiest om een regel uit te schakelen, worden beveiligingsproblemen mogelijk blootgesteld zonder beveiliging of detectie voor andere front-endhosts die zijn gekoppeld aan het WAF-beleid.
 
Zie de objectdocumentatie als u Azure PowerShell beheerde regel wilt [`PSAzureManagedRuleOverride`](/powershell/module/az.frontdoor/new-azfrontdoorwafmanagedruleoverrideobject) uitschakelen. Als u Azure CLI wilt gebruiken, bekijkt u de [`az network front-door waf-policy managed-rules override`](/cli/azure/network/front-door/waf-policy/managed-rules/override) documentatie.

![WAF-regels](../media/waf-front-door-tuning/waf-rules.png)

> [!TIP]
> Het is een goed idee om eventuele wijzigingen in uw WAF-beleid te documenteren. Neem voorbeeldaanvragen op om de fout-positieve detectie te illustreren en duidelijk uit te leggen waarom u een aangepaste regel hebt toegevoegd, een regel of regelset hebt uitgeschakeld of een uitzondering hebt toegevoegd. Deze documentatie kan handig zijn als u uw toepassing in de toekomst opnieuw ontwerpt en moet controleren of uw wijzigingen nog geldig zijn. Het kan ook helpen als u ooit wordt gecontroleerd of moet rechtvaardigen waarom u het WAF-beleid opnieuw hebt geconfigureerd vanuit de standaardinstellingen.

## <a name="finding-request-fields"></a>Aanvraagvelden zoeken

Met behulp van een browserproxy [zoals Fiddler](https://www.telerik.com/fiddler)kunt u afzonderlijke aanvragen inspecteren en bepalen welke specifieke velden van een webpagina worden aangeroepen. Dit is handig wanneer bepaalde velden moeten worden uitgesloten van inspectie met behulp van uitsluitingslijsten in WAF.

### <a name="finding-request-attribute-names"></a>Kenmerknamen van aanvragen zoeken
 
In dit voorbeeld ziet u dat het veld waar de `1=1` tekenreeks is ingevoerd, wordt `comment` aangeroepen. Deze gegevens zijn doorgegeven in de body van een POST-aanvraag.

![Fiddler-aanvraag met de body](../media/waf-front-door-tuning/fiddler-request-attribute-name.png)

Dit is een veld dat u kunt uitsluiten. Zie Uitsluitingslijsten voor [Web Application Firewall voor meer informatie over uitsluitingslijsten.](./waf-front-door-exclusion.md) U kunt de evaluatie in dit geval uitsluiten door de volgende uitsluiting te configureren:

![Uitsluitingsregel](../media/waf-front-door-tuning/fiddler-request-attribute-name-exclusion.png)

U kunt ook de firewalllogboeken bekijken om de informatie op te halen om te zien wat u aan de uitsluitingslijst moet toevoegen. Zie Bewaking van metrische gegevens en logboeken in Azure Front Door om logboekregistratie [in te Azure Front Door.](./waf-front-door-monitor.md)

Controleer het firewalllogboek in het bestand voor het uur dat de aanvraag `PT1H.json` die u wilt controleren heeft plaatsgevonden. `PT1H.json` -bestanden zijn beschikbaar in de opslagaccountcontainers waar `FrontDoorWebApplicationFirewallLog` de diagnostische logboeken en `FrontDoorAccessLog` worden opgeslagen.

In dit voorbeeld ziet u de regel die de aanvraag heeft geblokkeerd (met dezelfde transactieverwijzing) en op exact hetzelfde moment heeft plaatsgevonden:

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

Als u weet hoe de door Azure beheerde regelsets werken (zie Web Application Firewall op [Azure Front Door](afds-overview.md)) weet u dat de regel met de actie *Blokkeren wordt* geblokkeerd op basis van de gegevens die overeenkomen in de aanvraag body. U kunt in de details zien dat deze overeenkomen met een patroon ( `1=1` ) en dat het veld de naam `comment` heeft. Volg dezelfde vorige stappen om de naam van de aanvraag body post args uit te sluiten die `comment` bevat.

### <a name="finding-request-header-names"></a>Namen van aanvraagheaders zoeken

Fiddler is opnieuw een handig hulpmiddel om headernamen van aanvragen te vinden. In de volgende schermopname ziet u de headers voor deze GET-aanvraag, waaronder Content-Type, User-Agent, en meer. U kunt ook aanvraagheaders gebruiken om uitsluitingen en aangepaste regels te maken in WAF.

![Fiddler-aanvraag met header](../media/waf-front-door-tuning/fiddler-request-header-name.png)

Een andere manier om aanvraag- en antwoordheaders weer te geven, is door te kijken in de ontwikkelhulpprogramma's van uw browser, zoals Edge of Chrome. Druk op F12 of klik met de rechtermuisknop op -> **Inspect** Ontwikkelhulpprogramma's en selecteer het tabblad Netwerk. Laad een webpagina en klik op de aanvraag die  ->  u wilt inspecteren. 

![Aanvraag voor netwerkcontrole](../media/waf-front-door-tuning/network-inspector-request.png)

### <a name="finding-request-cookie-names"></a>Aanvraagcookienamen zoeken

Als de aanvraag cookies bevat, kan het tabblad Cookies worden geselecteerd om ze weer te geven in Fiddler. Cookie-informatie kan ook worden gebruikt voor het maken van uitsluitingen of aangepaste regels in WAF.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Web Application Firewall](../overview.md).
- Lees hoe u [een Front Door maakt](../../frontdoor/quickstart-create-front-door.md).

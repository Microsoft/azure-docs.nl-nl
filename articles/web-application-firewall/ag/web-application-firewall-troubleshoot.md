---
title: Problemen oplossen-Azure Web Application firewall
description: Dit artikel bevat informatie over het oplossen van problemen met Web Application firewall (WAF) voor Azure-toepassing gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 483d261a8cc107d01cfb7a405eac43667d7efcc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92131833"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Problemen met Web Application firewall (WAF) voor Azure-toepassing gateway oplossen

Er zijn enkele dingen die u kunt doen als aanvragen die worden door gegeven via uw Web Application firewall (WAF), worden geblokkeerd.

Zorg er eerst voor dat u het [WAF-overzicht](ag-overview.md) en de [WAF-configuratie](application-gateway-waf-configuration.md) documenten hebt gelezen. Zorg er ook voor dat u [WAF-bewaking](../../application-gateway/application-gateway-diagnostics.md) hebt ingeschakeld in deze artikelen wordt uitgelegd hoe de WAF-functies, hoe de WAF-regel sets werken en hoe u toegang krijgt tot WAF-Logboeken.

De OWASP-rules zijn ontworpen om zeer strikt uit het vak te komen en te worden afgestemd op de specifieke behoeften van de toepassing of organisatie met behulp van WAF. Het is volledig normaal en wordt in veel gevallen verwacht, voor het maken van uitsluitingen, aangepaste regels en zelfs het uitschakelen van regels die problemen of valse positieven kunnen veroorzaken. Per site-en per-URI-beleid toestaan dat deze wijzigingen alleen van invloed zijn op specifieke sites/Uri's, waardoor wijzigingen niet van invloed moeten zijn op andere sites die mogelijk niet in dezelfde problemen worden uitgevoerd. 

## <a name="understanding-waf-logs"></a>Informatie over WAF-logboeken

Het doel van WAF-Logboeken is om elke aanvraag weer te geven die overeenkomt met of wordt geblokkeerd door de WAF. Het is een groot boek van alle geëvalueerde aanvragen die overeenkomen of worden geblokkeerd. Als u ziet dat de WAF een aanvraag blokkeert die niet (een fout-positief) is, kunt u enkele dingen doen. Eerst beperkt u de specifieke aanvraag en zoekt u deze. Bekijk de logboeken om de specifieke URI, het tijds tempel of de trans actie-ID van de aanvraag te vinden. Wanneer u de bijbehorende logboek vermeldingen vindt, kunt u beginnen met de fout-positieven.

Stel bijvoorbeeld dat u een geldig verkeer hebt met de teken reeks *1 = 1* die u wilt door geven aan uw WAF. Als u de aanvraag probeert, blokkeert de WAF het verkeer dat uw reeks van *1 = 1* in een para meter of veld bevat. Dit is een teken reeks die vaak is gekoppeld aan een SQL-injectie aanval. U kunt de logboeken bekijken en de tijds tempel van de aanvraag bekijken en de regels die zijn geblokkeerd/overeenkomen.

In het volgende voor beeld ziet u dat er vier regels worden geactiveerd tijdens dezelfde aanvraag (met behulp van het veld TransactionId). De eerste geeft aan dat deze overeenkomt, omdat de gebruiker een numerieke/IP-URL voor de aanvraag heeft gebruikt, waardoor de afwijkings Score door drie is verhoogd, omdat er een waarschuwing is. De volgende regel die overeenkomt, is 942130, wat u zoekt. U ziet de *1 = 1* in het `details.data` veld. Hierdoor wordt de afwijkings Score nog verder verhoogd, omdat het ook een waarschuwing is. Over het algemeen verhoogt elke regel met de actie die **overeenkomt met** de afwijkende Score, en op dit moment is de afwijkende Score zes. Zie [afwijkende Score modus](ag-overview.md#anomaly-scoring-mode)voor meer informatie.

De laatste twee logboek vermeldingen geven aan dat de aanvraag is geblokkeerd, omdat de afwijkende score hoog genoeg is. Deze vermeldingen hebben een andere actie dan de twee andere. Ze laten zien dat ze de aanvraag daad werkelijk hebben *geblokkeerd* . Deze regels zijn verplicht en kunnen niet worden uitgeschakeld. Ze mogen niet worden beschouwd als regels, maar ook als basis infrastructuur van de WAF-interne.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Herstellen van valse positieven

Met deze informatie en de kennis waarvan regel 942130 de teken reeks is die overeenkomt met *1 = 1* , kunt u een aantal dingen doen om te voor komen dat uw verkeer wordt geblokkeerd:

- Een uitsluitings lijst gebruiken

   Zie [WAF-configuratie](application-gateway-waf-configuration.md#waf-exclusion-lists) voor meer informatie over uitsluitings lijsten.
- Schakel de regel uit.

### <a name="using-an-exclusion-list"></a>Een uitsluitings lijst gebruiken

Als u een weloverwogen beslissing wilt nemen over het afhandelen van een vals-positief, is het belang rijk om vertrouwd te raken met de technologieën die uw toepassing gebruikt. Stel bijvoorbeeld dat er geen SQL-Server is in uw technologie stack en dat er valse positieven worden ontvangen die aan deze regels zijn gerelateerd. Als u deze regels uitschakelt, wordt uw beveiliging niet noodzakelijkerwijs verzwakt.

Een voor deel van het gebruik van een uitsluitings lijst is dat alleen een specifiek deel van een aanvraag wordt uitgeschakeld. Dit betekent echter dat een specifieke uitsluiting van toepassing is op alle verkeer dat via uw WAF wordt door gegeven, omdat het een globale instelling is. Dit kan bijvoorbeeld leiden tot een probleem als *1 = 1* een geldige aanvraag is in de hoofd tekst van een bepaalde app, maar niet voor andere. Een ander voor deel is dat u kunt kiezen tussen hoofd tekst, kopteksten en cookies die moeten worden uitgesloten als aan een bepaalde voor waarde wordt voldaan, in plaats van de hele aanvraag uit te sluiten.

Soms zijn er gevallen waarin specifieke para meters worden door gegeven aan de WAF op een manier die mogelijk niet intuïtief is. Er is bijvoorbeeld een token dat wordt door gegeven wanneer wordt geverifieerd met behulp van Azure Active Directory. Dit token *__RequestVerificationToken*, meestal worden door gegeven als een aanvraag cookie. In sommige gevallen waarin cookies zijn uitgeschakeld, wordt dit token ook door gegeven als een aanvraag kenmerk of "ARG". Als dit het geval is, moet u ervoor zorgen dat *__RequestVerificationToken* wordt toegevoegd aan de uitsluitings lijst ook als **aanvraag kenmerk naam** .

![Uitsluitingen](../media/web-application-firewall-troubleshoot/exclusion-list.png)

In dit voor beeld wilt u de naam van het **aanvraag kenmerk** uitsluiten die gelijk is aan *text1*. Dit is duidelijk omdat u de naam van het kenmerk in de firewall Logboeken kunt zien: **gegevens: overeenkomende gegevens: 1 = 1 gevonden binnen de argumenten: text1:1 = 1**. Het kenmerk is **text1**. U kunt deze kenmerk naam ook op een aantal andere manieren vinden. Zie [aanvraag kenmerk namen zoeken](#finding-request-attribute-names).

![WAF uitsluitings lijsten](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Regels uitschakelen

Een andere manier om te zien wat een fout positief is, is door de regel uit te scha kelen die overeenkomt met de ingevoerde invoer die schadelijk is voor WAF. Omdat u de WAF-Logboeken hebt geparseerd en de regel omlaag hebt geverfijn tot 942130, kunt u deze uitschakelen in de Azure Portal. Zie [Web Application firewall regels aanpassen via de Azure Portal](application-gateway-customize-waf-rules-portal.md).

Een voor deel van het uitschakelen van een regel is dat als u al het verkeer weet dat een bepaalde voor waarde bevat die normaal gesp roken geblokkeerd is, u de regel voor de hele WAF kunt uitschakelen. Als het echter alleen geldig verkeer is in een specifieke use-case, opent u een beveiligingslek door de regel voor de hele WAF uit te scha kelen, omdat het een globale instelling is.

Als u Azure PowerShell wilt gebruiken, raadpleegt u [Web Application firewall regels aanpassen via Power shell](application-gateway-customize-waf-rules-powershell.md). Als u Azure CLI wilt gebruiken, raadpleegt u [Web Application firewall-regels aanpassen via de Azure cli](application-gateway-customize-waf-rules-cli.md).

![WAF-regels](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Kenmerk namen van aanvragen zoeken

Met de hulp van [Fiddler](https://www.telerik.com/fiddler)kunt u afzonderlijke aanvragen controleren en bepalen welke specifieke velden van een webpagina worden genoemd. Dit kan helpen om bepaalde velden uit te sluiten van inspectie met behulp van uitsluitings lijsten.

In dit voor beeld ziet u dat het veld waarin de teken reeks van *1 = 1* is ingevoerd **text1** heet.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-1.png" alt-text="Scherm opname van de voortgang Telerik Fiddler Web debugger. Op het tabblad onbewerkt, wordt 1 = 1 weer gegeven na de naam Tekst1." border="false":::

Dit is een veld dat u kunt uitsluiten. Zie voor meer informatie over uitsluitings lijsten [Web Application firewall-aanvraag grootte limieten en uitsluitings lijsten](application-gateway-waf-configuration.md#waf-exclusion-lists). U kunt de evaluatie in dit geval uitsluiten door de volgende uitzonde ring te configureren:

![WAF-uitsluiting](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

U kunt ook de logboeken van de firewall bekijken om de informatie op te halen om te zien wat u moet toevoegen aan de uitsluitings lijst. Als u logboek registratie wilt inschakelen, raadpleegt u de [back-end status, bron logboeken en metrische gegevens voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Bekijk het firewall logboek en Bekijk de PT1H.jsin het bestand voor het uur dat de aanvraag die u wilt controleren, heeft plaatsgevonden.

In dit voor beeld ziet u dat u vier regels met dezelfde TransactionID hebt en dat deze allemaal op exact dezelfde tijd hebben plaatsgevonden:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Met uw kennis van de manier waarop de CRS-regel is ingesteld, en dat de geautomatiseerde ruleset 3,0 werkt met een afwijkend score systeem (Zie [Web Application Firewall voor Azure-toepassing gateway](ag-overview.md)) weet u dat de onderste twee regels met de **actie:** de eigenschap blocked wordt geblokkeerd op basis van de totale afwijkings Score. De regels voor het richten op zijn de bovenste twee.

De eerste vermelding wordt geregistreerd omdat de gebruiker een numeriek IP-adres heeft gebruikt om naar de Application Gateway te navigeren, die in dit geval kan worden genegeerd.

De tweede (regel 942130) is de interessante versie. U kunt in de details zien dat deze overeenkomt met een patroon (1 = 1), en het veld heet **text1**. Volg dezelfde vorige stappen om de naam van het **aanvraag kenmerk** uit te sluiten die **gelijk is aan** **1 = 1**.

## <a name="finding-request-header-names"></a>Namen van aanvraag headers zoeken

Fiddler is opnieuw een handig hulp programma om namen van aanvraag headers te vinden. In de volgende scherm afbeelding ziet u de kopteksten voor deze GET-aanvraag, waaronder *Content-type*, *User-agent*, enzovoort.

:::image type="content" source="../media/web-application-firewall-troubleshoot/fiddler-2.png" alt-text="Scherm opname van de voortgang Telerik Fiddler Web debugger. Op het tabblad RAW worden details van de aanvraag header weer gegeven, zoals de verbinding, het inhouds type en de gebruikers agent." border="false":::

Een andere manier om aanvraag-en reactie headers weer te geven, is door te kijken in de ontwikkel tools van Chrome. U kunt op F12 drukken of met de rechter muisknop > Ontwikkelhulpprogramma's **inspecteren**  ->  en het tabblad **netwerk** selecteren. Laad een webpagina en klik op de aanvraag die u wilt inspecteren.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Cookie namen van aanvragen zoeken

Als de aanvraag cookies bevat, kan het tabblad **cookies** worden geselecteerd om ze weer te geven in Fiddler.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Algemene para meters beperken om fout-positieven te elimineren

- Inspectie van aanvraag hoofdtekst uitschakelen

   Door controle van de **aanvraag tekst** in te stellen op uit, worden de aanvraag teksten van al het verkeer niet geëvalueerd door uw WAF. Dit kan handig zijn als u weet dat de aanvraag teksten niet schadelijk zijn voor uw toepassing.

   Als u deze optie uitschakelt, wordt alleen de hoofd tekst van de aanvraag gecontroleerd. De kopteksten en cookies blijven geïnspecteerd, tenzij afzonderlijke personen worden uitgesloten met de uitsluitings lijst functionaliteit.

- Maximale bestands grootte

   Door de bestands grootte voor uw WAF te beperken, kunt u de kans op aanvallen op uw webservers beperken. Doordat grote bestanden kunnen worden geüpload, neemt het risico van uw back-end toe. Het beperken van de bestands grootte voor een normale use-case voor uw toepassing is een andere manier om aanvallen te voor komen.

   > [!NOTE]
   > Als u weet dat voor uw app nooit een bestand moet worden geüpload boven een bepaalde grootte, kunt u dit beperken door een limiet in te stellen.

## <a name="firewall-metrics-waf_v1-only"></a>Metrics van de firewall (alleen WAF_v1)

Voor v1 Web Application firewalls zijn de volgende metrische gegevens nu beschikbaar in de portal: 

1. Aantal geblokkeerde aanvragen voor Web Application firewall het aantal verzoeken dat is geblokkeerd
2. Aantal geblokkeerde regels van de firewall voor webtoepassingen, alle regel die overeenkomen **en** de aanvraag is geblokkeerd
3. Totale regel distributie Web Application firewall alle regels die overeenkomen tijdens de evaluatie
     
Als u metrische gegevens wilt inschakelen, selecteert u het tabblad **metrische gegevens** in de portal en selecteert u een van de drie meet waarden.

## <a name="next-steps"></a>Volgende stappen

Zie [Web Application firewall op Application Gateway configureren voor meer informatie](tutorial-restrict-web-traffic-powershell.md).

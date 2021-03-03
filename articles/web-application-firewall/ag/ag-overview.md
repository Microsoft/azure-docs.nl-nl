---
title: Wat is Azure Web Application Firewall voor Azure Application Gateway?
titleSuffix: Azure Web Application Firewall
description: Dit artikel geeft een overzicht van Web Application Firewall (WAF) voor Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 12/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 48d4fe254f51dde932f04ef791e1109a0ced07ff
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701617"
---
# <a name="what-is-azure-web-application-firewall-on-azure-application-gateway"></a>Wat is Azure Web Application Firewall voor Azure Application Gateway?

Azure Web Application Firewall (WAF) voor Azure Application Gateway biedt gecentraliseerde beveiliging van uw webtoepassingen tegen veelvoorkomende aanvallen en beveiligingsproblemen. Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van algemeen bekende beveiligingsproblemen. SQL-injectie en cross-site scripting zijn twee van de meest voorkomende aanvallen.

WAF voor Application Gateway is gebaseerd op [Core Rule Set (CRS)](https://owasp.org/www-project-modsecurity-core-rule-set/) 3.1, 3.0 of 2.2.9 van het Open Web Application Security Project (OWASP). WAF wordt automatisch bijgewerkt om bescherming te bieden tegen nieuwe beveiligingsproblemen, zonder dat er aanvullende configuratie nodig is. 

Alle WAF-functies die hieronder worden vermeld, bevinden zich in een WAF-beleid. U kunt meerdere beleidsregels opstellen en deze kunnen worden gekoppeld aan een toepassingsgateway, aan individuele listeners of aan routeringsregels op een toepassingsgateway die op een pad zijn gebaseerd. Op deze manier kunt u indien nodig afzonderlijke beleidsregels hanteren voor elke site achter uw toepassingsgateway. Zie [Een WAF-beleid maken](create-waf-policy-ag.md)voor meer informatie over WAF-beleidsregels.

![WAF-diagram van Application Gateway](../media/ag-overview/waf1.png)

Application Gateway fungeert als een ADC-controller (Application Delivery controller). Application Gateway biedt TLS (Transport Layer Security), eerder bekend als SSL (Secure Sockets Layer), beëindiging, sessieaffiniteit op basis van cookies, round robin-taakverdeling, routering op basis van inhoud, de mogelijkheid om meerdere websites te hosten en beveiligingsverbeteringen.

Beveiligingsverbeteringen die door Application Gateway worden geboden, zijn onder andere TLS-beleidsbeheer en ondersteuning voor end-to-end TLS. De beveiliging van toepassingen wordt versterkt door integratie van WAF in Application Gateway. Met deze combinatie worden uw webtoepassingen beschermd tegen veelvoorkomende beveiligingsproblemen. En het biedt een eenvoudig te configureren centrale beheerlocatie.

## <a name="benefits"></a>Voordelen

In deze sectie worden de belangrijkste voordelen beschreven die WAF in Application Gateway biedt.

### <a name="protection"></a>Beveiliging

* Beveilig uw webtoepassing tegen kwetsbaarheden en aanvallen op het web zonder dat u de code voor de back-end hoeft aan te passen.

* Beveilig meerdere webtoepassingen tegelijk. Een instantie van Application Gateway kan maximaal 40 websites hosten die worden beveiligd door een Web Application Firewall.

* Stel aangepaste WAF-beleidsregels op voor verschillende sites achter dezelfde WAF. 

* Beveilig uw webtoepassingen tegen kwaadaardige bots met behulp van de regelset van IP Reputation (preview).

### <a name="monitoring"></a>Bewaking

* Controleer op aanvallen tegen webtoepassingen door een real-time logboek van WAF te gebruiken. Het logboek is geïntegreerd met [Azure Monitor](../../azure-monitor/overview.md) om waarschuwingen van WAF bij te houden en gemakkelijk trends te ontdekken.

* WAF van Application Gateway is geïntegreerd met Azure Security Center. Security Center biedt een centraal overzicht van de beveiligingsstatus van al uw Azure-resources.

### <a name="customization"></a>Aanpassing

* Pas regels en regelgroepen van WAF aan om deze te laten voldoen aan de toepassingsvereisten en om fout-positieven te elimineren.

* Koppel een WAF-beleid voor elke site achter uw WAF om sitespecifieke configuratie te bieden.

* Stel aangepaste regels op om te voldoen aan de behoeften van uw toepassing.

## <a name="features"></a>Functies

- Beveiliging tegen SQL-injectie.
- Beveiliging tegen cross-site scripting
- Beveiliging tegen andere veelvoorkomende aanvallen via het web, zoals opdrachtinjectie, het smokkelen van HTTP-aanvragen, het uitsplitsen van HTTP-antwoorden en het insluiten van externe bestanden.
- Beveiliging tegen schendingen van het HTTP-protocol.
- Beveiliging tegen afwijkingen van het HTTP-protocol, zoals het ontbreken van een gebruikersagent voor de host en Accept-headers.
- Beveiliging tegen crawlers en scanners.
- Detectie van veelvoorkomende onjuiste configuraties van toepassingen (bijvoorbeeld Apache en IIS).
- Configureerbare limieten voor grootte van aanvraag met boven- en ondergrenzen.
- Met uitsluitingslijsten kunt u bepaalde kenmerken van aanvragen weglaten uit een WAF-evaluatie. Een bekend voorbeeld is door Active Directory ingevoegde tokens die worden gebruikt voor verificatie- of wachtwoordvelden.
- Stel aangepaste regels op om te voldoen aan de specifieke behoeften van uw toepassingen.
- Pas geografische filters toe op verkeer om bepaalde landen/regio's al dan niet toegang te geven tot uw toepassingen. (preview)
- Beveilig uw toepassingen tegen bots met de regelset voor beperking voor bots. (preview)
- Controleer de JSON- en XML-code in de hoofdtekst van de aanvraag

## <a name="waf-policy-and-rules"></a>WAF-beleid en -regels

Als u een Web Application Firewall wilt inschakelen op Application Gateway, moet u een WAF-beleid maken. In dit beleid zijn alle beheerde regels, aangepaste regels, uitsluitingen en andere aanpassingen verzameld, zoals de uploadlimiet voor bestanden.

U kunt een WAF-beleid configureren en dit beleid aan een of meer toepassingsgateways koppelen voor beveiliging. Een WAF-beleid bestaat uit twee typen beveiligingsregels:

- Aangepaste regels die u kunt maken

- Beheerde regelsets; een verzameling door Azure beheerde, vooraf geconfigureerde set regels

Als beide typen regels aanwezig zijn, worden aangepaste regels eerst verwerkt en daarna de regels in een beheerde regelset. Een regel bestaat uit een voorwaarde voor overeenkomst, een prioriteit en een actie. Dit zijn de ondersteunde actietypen: ALLOW, BLOCK en LOG. U kunt een volledig aangepast beleid maken dat voldoet aan uw specifieke vereisten voor toepassingsbeveiliging door beheerde en aangepaste regels te combineren.

Regels in een beleid worden verwerkt in een prioriteitsvolgorde. Prioriteit is een uniek geheel getal dat de volgorde bepaalt van de te verwerken regels. Een lager geheel getal geeft een hogere prioriteit aan, en deze regels worden eerder geëvalueerd dan regels met een hoger geheel getal. Zodra een overeenkomst met een regel is gevonden, wordt op de aanvraag de actie toegepast die is gedefinieerd in de regel. Zodra een dergelijke overeenkomst is verwerkt, worden regels met lagere prioriteiten niet meer verwerkt.

Aan een door Application Gateway geleverde webtoepassing kan een WAF-beleid op globaal niveau gekoppeld zijn, maar dat kan ook per site of per URI.

### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway ondersteunt drie regelsets: CRS 3.1, CRS 3.0 en CRS 2.2.9. Met deze regels worden uw webtoepassingen beveiligd tegen schadelijke activiteiten.

Zie voor meer informatie [Web Application Firewall CRS rule groups and rules](application-gateway-crs-rulegroups-rules.md) (CRS-regelgroepen en -regels voor Web Application Firewall).

### <a name="custom-rules"></a>Aangepaste regels

Application Gateway biedt ook ondersteuning voor aangepaste regels. Met aangepaste regels kunt u uw eigen regels maken, die worden geëvalueerd voor elke aanvraag die via WAF wordt doorgegeven. Deze regels hebben een hogere prioriteit dan de rest van de regels in de beheerde regelsets. Als aan een set voorwaarden wordt voldaan, wordt er een actie uitgevoerd om een bewerking toe te staan of te blokkeren. 

De operator geomatch is nu beschikbaar in openbare preview voor aangepaste regels. Zie [Geomatch custom rule](custom-waf-rules-overview.md#geomatch-custom-rules-preview) (Aangepaste regels voor geografische overeenkomst) voor meer informatie.

> [!NOTE]
> De operator geomatch voor aangepaste regels is momenteel in openbare preview en wordt aangeboden met een preview-SLA. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zie [Custom Rules for Application Gateway](custom-waf-rules-overview.md) (Aangepaste regels voor Application Gateway) voor meer informatie over aangepaste regels.

### <a name="bot-mitigation-preview"></a>Beperkingen opleggen aan bots (preview-versie)

Naast de beheerde regelset, kan er een beheerde set met regels voor bescherming tegen bots worden ingeschakeld voor uw WAF om aanvragen van bekende schadelijke IP-adressen te blok keren of te registreren. De IP-adressen zijn afkomstig uit de feed Bedreigingsinformatie van Microsoft. Intelligent Security Graph voorziet de feed van gegevens en wordt gebruikt door verschillende services, waaronder Azure Security Center.

> [!NOTE]
> De set met regels voor beveiliging tegen bots is momenteel in openbare preview en wordt aangeboden met een preview-SLA. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als bot-beveiliging is ingeschakeld, worden inkomende aanvragen die overeenkomen met client-IP's van schadelijke bots vastgelegd in het firewalllogboek. Zie hieronder voor meer informatie. U kunt toegang krijgen tot WAF-logboeken vanuit het opslagaccount, Event Hub of Log Analytics. 

### <a name="waf-modes"></a>WAF-modi

In Application Gateway WAF kunnen de volgende twee modi worden geconfigureerd:

* **Detectiemodus**: Hiermee worden alle waarschuwingen voor bedreigingen gecontroleerd en vastgelegd. U kunt het vastleggen van diagnostische gegevens inschakelen voor Application Gateway via de sectie **Diagnostische gegevens**. U moet er ook voor zorgen dat het WAF-logboek is geselecteerd en ingeschakeld. In de detectiemodus worden binnenkomende verzoeken niet geblokkeerd door Web Application Firewall.
* **Preventiemodus**: Blokkeert indringers en aanvallen die door de regels zijn gedetecteerd. De aanvaller krijgt een uitzondering '403 onbevoegde toegang' en de verbinding wordt verbroken. In de preventiemodus worden dergelijke aanvallen vastgelegd in de WAF-logboeken.

> [!NOTE]
> Het is raadzaam om een nieuw geïmplementeerde WAF gedurende korte tijd in de detectiemodus uit te voeren in een productieomgeving. U hebt dan de mogelijkheid om [firewall-logboeken](../../application-gateway/application-gateway-diagnostics.md#firewall-log) te verzamelen, aan de hand waarvan u eventuele uitzonderingen of [aangepaste regels](./custom-waf-rules-overview.md) kunt aanpassen voordat u verdergaat in de preventiemodus. Hierdoor kan het volume onverwacht geblokkeerd verkeer worden verminderd.

### <a name="anomaly-scoring-mode"></a>Modus Anomaliescore

OWASP heeft twee modi om te bepalen of verkeer moet worden geblokkeerd: traditionele modus en Anomaliescore.

In de traditionele modus wordt verkeer dat voldoet aan een regel, als onafhankelijk beschouwd van andere regels waaraan wordt voldaan. Deze modus is eenvoudig te begrijpen. Maar het ontbreken van informatie over het aantal regels dat voldoet aan een specifieke aanvraag is een beperking. Daarom is de modus Anomaliescore geïntroduceerd. Dit is de standaard waarde voor OWASP 3.*x*.

In de modus Anomaliescore wordt verkeer dat voldoet aan een regel niet onmiddellijk geblokkeerd wanneer de firewall in de preventiemodus staat. Regels hebben een bepaalde ernst: *Kritiek*, *Fout*, *Waarschuwing* of *Kennisgeving*. Deze ernst is van invloed op een numerieke waarde voor de aanvraag, die de anomalie- of afwijkingsscore wordt genoemd. Een regel met de ernst *Waarschuwing* draagt bijvoorbeeld 3 bij aan de score. Eén overeenkomst met een *kritieke* regel betekent dat de score wordt verhoogd met 5.

|Severity  |Waarde  |
|---------|---------|
|Kritiek     |5|
|Fout        |4|
|Waarschuwing      |3|
|Kennisgeving       |2|

Als de anomaliescore een drempel van 5 heeft bereikt, wordt het verkeer geblokkeerd. Dus één overeenkomst met een regel van de ernst *Kritiek* is voldoende voor de Application Gateway WAF om een aanvraag te blokkeren, zelfs in de preventiemodus. Maar bij één overeenkomst met een regel met het ernstniveau *Waarschuwing*, wordt de score alleen met 3 verhoogd, wat niet voldoende is om het verkeer te blokkeren.

> [!NOTE]
> Het bericht dat wordt vastgelegd wanneer verkeer voldoet aan een WAF-regel bevat de actiewaarde 'Geblokkeerd'. Het verkeer wordt echter alleen daadwerkelijk geblokkeerd bij een anomaliescore van 5 of hoger. Raadpleeg [Problemen met Web Application Firewall (WAF) voor Azure Application Gateway oplossen](web-application-firewall-troubleshoot.md#understanding-waf-logs) voor meer informatie. 

### <a name="waf-monitoring"></a>Bewaking van WAF

Het bewaken van de status van uw toepassingsgateway is belangrijk. Het bewaken van de status van Web Application Firewall en van de toepassingen die ermee worden beveiligd, is mogelijk door integratie met de logboeken van Azure Security Center, Azure Monitor en Log Analytics.

![Diagram van diagnostische gegevens van Application Gateway WAF](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Logboeken van Application Gateway zijn geïntegreerd met [Azure Monitor](../../azure-monitor/overview.md). Dit maak het mogelijk om diagnostische gegevens bij te houden, met inbegrip van WAF-meldingen en logboeken. Ga hiervoor naar het tabblad **Diagnostische gegevens** van de resource Application Gateway in de portal, of rechtstreeks vanuit de service Azure Monitor. Zie [Diagnostische gegevens van Application Gateway](../../application-gateway/application-gateway-diagnostics.md) voor meer informatie over het inschakelen van logboeken.

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-introduction.md) helpt bij het voorkomen, detecteren en afhandelen van bedreigingen. De service biedt meer inzicht in en controle over de veiligheid van uw Azure-resources. Application Gateway is [geïntegreerd met Security Center](../../security-center/security-center-partner-integration.md#integrated-azure-security-solutions). Security Center scant uw omgeving op niet-beveiligde webtoepassingen. Indien nodig worden er aanbevelingen naar Application Gateway WAF gestuurd om deze kwetsbare resources te beschermen. U maakt de firewalls rechtstreeks vanuit Security Center. Deze instanties van WAF zijn geïntegreerd met Security Center. Ze sturen waarschuwingen en statusinformatie naar Security Center voor rapportagedoeleinden.

![Venster Overzicht van Security Center](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel is een schaalbaar, cloudoplossing voor Security Information Event Management (SIEM) en Security Orchestration Automated Response (SOAR). Azure Sentinel levert intelligente beveiligingsanalyses en bedreigingsinformatie voor de hele onderneming en is daardoor één oplossing voor waarschuwingsdetectie, zichtbaarheid van bedreigingen, proactieve opsporing en reactie op bedreigingen.

Met de ingebouwde werkmap voor firewallgebeurtenissen van Azure WAF kunt u een overzicht krijgen van de beveiligingsgebeurtenissen op uw WAF. Dit omvat gebeurtenissen, overeenkomende en geblokkeerde regels, en verder alles wat kan worden vastgelegd in de logboeken van de firewall. Hieronder vindt u meer informatie over logboekregistratie. 


![Werkmap voor Azure WAF-firewallgebeurtenissen](../media/ag-overview/sentinel.png)


#### <a name="azure-monitor-workbook-for-waf"></a>Azure Monitor-werkmap voor WAF

Met deze werkmap kunt u aangepaste visualisaties van WAF-gebeurtenissen met betrekking tot beveiliging voor verschillende filterbare deelvensters inschakelen. De werkmap werkt met alle WAF-typen, waaronder Application Gateway, Front Door en CDN, en kan worden gefilterd op basis van het WAF-type of een specifiek WAF-exemplaar. Importeren via ARM-sjabloon of galeriesjabloon. Zie [WAF-werkmap](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) als u deze werkmap wilt implementeren.

#### <a name="logging"></a>Logboekregistratie

Application Gateway WAF biedt gedetailleerde rapporten voor elke bedreiging die wordt gedetecteerd. Logboekregistratie is geïntegreerd met Azure Diagnostics-logboeken. Waarschuwingen worden vastgelegd in de JSON-indeling. Deze logboeken kunnen worden geïntegreerd met [Azure Monitor-logboeken](../../azure-monitor/insights/azure-networking-analytics.md).

![Vensters met diagnostische logboeken van Application Gateway](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Prijzen voor de Application Gateway WAF-voorraadeenheid

De prijsmodellen verschillen voor de SKU's WAF_v1 en WAF_v2. Ga naar de pagina [Prijzen voor Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) voor meer informatie. 

## <a name="whats-new"></a>Nieuwe functies

Zie [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=Web%20Application%20Firewall) om te ontdekken wat er nieuw is in Azure Web Application Firewall.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerde WAF-regels](application-gateway-crs-rulegroups-rules.md)
- Meer informatie over [aangepaste regels](custom-waf-rules-overview.md)
- Meer informatie over [Web Application Firewall in Azure Front Door](../afds/afds-overview.md)

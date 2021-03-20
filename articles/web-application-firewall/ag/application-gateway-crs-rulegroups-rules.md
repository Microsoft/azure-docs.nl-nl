---
title: CRS-regel groepen en-regels
titleSuffix: Azure Web Application Firewall
description: Deze pagina bevat informatie over Web Application Firewall CRS-regel groepen en-regels.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: e2c88091072921f1ca674868e401c34d354418de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746506"
---
# <a name="web-application-firewall-crs-rule-groups-and-rules"></a>CRS-regel groepen en-regels voor Web Application firewall

Application Gateway Web Application Firewall (WAF) beschermt webtoepassingen tegen veelvoorkomende beveiligings problemen en aanvallen. Dit doet u door regels die zijn gedefinieerd op basis van de OWASP core Rule sets 3,1, 3,0 of 2.2.9. Deze regels kunnen per regel afzonderlijk worden uitgeschakeld. Dit artikel bevat de huidige regels en regel sets die worden aangeboden.

## <a name="core-rule-sets"></a>Core Rule Sets

De Application Gateway WAF is standaard vooraf geconfigureerd met CRS 3,0. Maar u kunt in plaats daarvan kiezen voor het gebruik van CRS 3,1 of CRS 2.2.9. CRS 3,1 biedt nieuwe regel sets voor het beschermen tegen Java-infecties, een initiële set controles voor het uploaden van bestanden, vaste fout-positieven en meer. CRS 3,0 biedt gereduceerde valse positieven vergeleken met geautomatiseerd 2.2.9. U kunt ook [regels aanpassen aan uw behoeften](application-gateway-customize-waf-rules-portal.md).

> [!div class="mx-imgBorder"]
> ![Beheert regels](../media/application-gateway-crs-rulegroups-rules/managed-rules-01.png)

De WAF beveiligt tegen de volgende webproblemen:

- Aanvallen met SQL-injectie
- Aanvallen voor cross-site scripting
- Andere veelvoorkomende aanvallen, zoals het invoegen van opdrachten, HTTP-aanvragen smuggling, het splitsen van HTTP-antwoorden en het opnemen van externe bestanden
- Schendingen van het HTTP-protocol
- Afwijkingen van het HTTP-protocol, zoals de ontbrekende host gebruikers agent en headers accepteren
- Bots, crawlers en scanners
- Veelvoorkomende onjuiste configuraties van toepassingen (bijvoorbeeld Apache en IIS)

### <a name="owasp-crs-31"></a>OWASP CRS 3,1

CRS 3,1 bevat 13 regel groepen, zoals wordt weer gegeven in de volgende tabel. Elke groep bevat meerdere regels die kunnen worden uitgeschakeld.

> [!NOTE]
> CRS 3,1 is alleen beschikbaar op de WAF_v2 SKU.

|Regelgroep|Beschrijving|
|---|---|
|**[Algemeen](#general-31)**|Algemene groep|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-31)**|Vergrendelings methoden (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-31)**|Beveiligen tegen poort-en omgevings scanners|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-31)**|Beveiligen tegen protocol-en coderings problemen|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-31)**|Beveiligen tegen koptekst injectie, aanvragen van smuggling en het splitsen van antwoorden|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-31)**|Beveiligen tegen bestands-en pad-aanvallen|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-31)**|Beveiligen tegen RFI-aanvallen (Remote File insluiting)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-31)**|Beveiligings aanvallen van externe code opnieuw uitvoeren|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-31)**|Beveiligen tegen PHP-injectie aanvallen|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-31)**|Bescherming tegen cross-site scripting-aanvallen|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-31)**|Bescherming tegen SQL-injectie aanvallen|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-31)**|Bescherming tegen sessie-bindings aanvallen|
|**[AANVRAAG-944-TOEPASSING-AANVAL-SESSIE-JAVA](#crs944-31)**|Beveiligen tegen JAVA-aanvallen|

### <a name="owasp-crs-30"></a>OWASP CRS 3,0

CRS 3,0 bevat 12 regel groepen, zoals wordt weer gegeven in de volgende tabel. Elke groep bevat meerdere regels die kunnen worden uitgeschakeld.

|Regelgroep|Beschrijving|
|---|---|
|**[Algemeen](#general-30)**|Algemene groep|
|**[REQUEST-911-METHOD-ENFORCEMENT](#crs911-30)**|Vergrendelings methoden (PUT, PATCH)|
|**[REQUEST-913-SCANNER-DETECTION](#crs913-30)**|Beveiligen tegen poort-en omgevings scanners|
|**[REQUEST-920-PROTOCOL-ENFORCEMENT](application-gateway-crs-rulegroups-rules.md#crs920-30)**|Beveiligen tegen protocol-en coderings problemen|
|**[REQUEST-921-PROTOCOL-ATTACK](#crs921-30)**|Beveiligen tegen koptekst injectie, aanvragen van smuggling en het splitsen van antwoorden|
|**[REQUEST-930-APPLICATION-ATTACK-LFI](#crs930-30)**|Beveiligen tegen bestands-en pad-aanvallen|
|**[REQUEST-931-APPLICATION-ATTACK-RFI](#crs931-30)**|Beveiligen tegen RFI-aanvallen (Remote File insluiting)|
|**[REQUEST-932-APPLICATION-ATTACK-RCE](#crs932-30)**|Beveiligings aanvallen van externe code opnieuw uitvoeren|
|**[REQUEST-933-APPLICATION-ATTACK-PHP](#crs933-30)**|Beveiligen tegen PHP-injectie aanvallen|
|**[REQUEST-941-APPLICATION-ATTACK-XSS](#crs941-30)**|Bescherming tegen cross-site scripting-aanvallen|
|**[REQUEST-942-APPLICATION-ATTACK-SQLI](#crs942-30)**|Bescherming tegen SQL-injectie aanvallen|
|**[REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION](#crs943-30)**|Bescherming tegen sessie-bindings aanvallen|

### <a name="owasp-crs-229"></a>OWASP CRS 2.2.9

Geautomatiseerd 2.2.9 bevat tien regel groepen, zoals wordt weer gegeven in de volgende tabel. Elke groep bevat meerdere regels die kunnen worden uitgeschakeld.

|Regelgroep|Beschrijving|
|---|---|
|**[crs_20_protocol_violations](#crs20)**|Beveiligen tegen protocol schendingen (zoals ongeldige tekens of een GET met een aanvraag tekst)|
|**[crs_21_protocol_anomalies](#crs21)**|Beveiligen tegen onjuiste header gegevens|
|**[crs_23_request_limits](#crs23)**|Beveiligen tegen argumenten of bestanden die de beperkingen overschrijden|
|**[crs_30_http_policy](#crs30)**|Beveiligen tegen beperkte methoden, kopteksten en bestands typen|
|**[crs_35_bad_robots](#crs35)**|Beveiligen tegen webcrawlers en scanners|
|**[crs_40_generic_attacks](#crs40)**|Beveiligen tegen algemene aanvallen (zoals sessie-vastleg ging, insluiting van externe bestanden en PHP-injectie)|
|**[crs_41_sql_injection_attacks](#crs41sql)**|Bescherming tegen SQL-injectie aanvallen|
|**[crs_41_xss_attacks](#crs41xss)**|Bescherming tegen cross-site scripting-aanvallen|
|**[crs_42_tight_security](#crs42)**|Beveiligen tegen pad-traversal-aanvallen|
|**[crs_45_trojans](#crs45)**|Beschermen tegen Trojaanse paarden|

De volgende regel groepen en regels zijn beschikbaar wanneer Web Application firewall wordt gebruikt op Application Gateway.

# <a name="owasp-31"></a>[OWASP 3,1](#tab/owasp31)

## <a name="rule-sets"></a><a name="owasp31"></a> Regel sets

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-31"></a> <p x-ms-format-detection="none">Algemeen</p>

|RuleId|Beschrijving|
|---|---|
|200004|Mogelijke meerdelige niet-overeenkomende grens.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-31"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Beschrijving|
|---|---|
|911100|De methode is niet toegestaan door het beleid|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-31"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Beschrijving|
|---|---|
|913100|Gevonden User-Agent gekoppeld aan beveiligings scanner|
|913101|User-Agent gekoppeld aan scripting/algemene HTTP-client|
|913102|Gevonden User-Agent gekoppeld aan web-crawler/bot|
|913110|De aanvraag header is gevonden die is gekoppeld aan de beveiligings scanner|
|913120|Bestands naam/argument van aanvraag gevonden die is gekoppeld aan beveiligings scanner|


### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-31"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Beschrijving|
|---|---|
|920100|Ongeldige regel voor HTTP-aanvraag|
|920120|Poging tot meerdelige/formulier-data bypass|
|920121|Poging tot meerdelige/formulier-data bypass|
|920130|De aanvraag tekst kan niet worden geparseerd.|
|920140|Strikte validatie van de aanvraag tekst voor meerdelige is mislukt|
|920160|De HTTP-header van de content-length is niet numeriek.|
|920170|GET-of HEAD-aanvraag met hoofd inhoud.|
|920171|GET-of HEAD-aanvraag met overboeking-encoding.|
|920180|POST-aanvraag mist header content-length.|
|920190|Range = ongeldige waarde voor laatste byte.|
|920200|Range = te veel velden (6 of meer)|
|920201|Range = te veel velden voor PDF-aanvraag (35 of meer)|
|920202|Range = te veel velden voor PDF-aanvraag (6 of meer)|
|920210|Er zijn meerdere/conflicterende header gegevens gevonden.|
|920220|Poging tot misbruik van URL-code ring|
|920230|Meerdere URL-code ring gedetecteerd|
|920240|Poging tot misbruik van URL-code ring|
|920250|Poging tot misbruik van UTF8-code ring|
|920260|Volledige/halve breedte van Unicode misbruik van aanvallen|
|920270|Ongeldig teken in aanvraag (null-teken)|
|920271|Ongeldig teken in de aanvraag (niet-afdruk bare tekens)|
|920272|Ongeldig teken in de aanvraag (buiten afdruk bare tekens onder ASCII 127)|
|920273|Ongeldig teken in aanvraag (buiten strikt ingesteld)|
|920274|Ongeldig teken in de aanvraag headers (buiten een zeer strikte set)|
|920280|Aanvraag voor ontbrekende host-header|
|920290|Lege host-header|
|920300|Aanvraag waarbij een Accept-header ontbreekt|
|920310|De aanvraag bevat een lege Accept-header|
|920311|De aanvraag bevat een lege Accept-header|
|920320|Ontbrekende koptekst gebruikers agent|
|920330|Lege koptekst gebruikers agent|
|920340|Aanvraag met inhoud, maar ontbrekende content-type-header|
|920341|Voor een aanvraag met inhoud is een content-type-header vereist|
|920350|Host-header is een numeriek IP-adres|
|920360|Argument naam is te lang|
|920370|Argument waarde te lang|
|920380|De aanvraag heeft te veel argumenten|
|920390|Totale grootte van argumenten is overschreden|
|920400|Geüploade bestands grootte te groot|
|920410|Totale grootte van geüploade bestanden te groot|
|920420|Het inhouds type van de aanvraag is niet toegestaan door het beleid|
|920430|De versie van het HTTP-protocol wordt niet toegestaan door het beleid|
|920440|De extensie van het URL-bestand wordt beperkt door het beleid|
|920450|De HTTP-header wordt beperkt door het beleid (% @ {MATCHED_VAR})|
|920460|Abnormale escape tekens|
|920470|Ongeldige content-type-header|
|920480|Charset-para meter binnen de content-type-header beperken|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-31"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Beschrijving|
|---|---|
|921110|HTTP-aanvraag smuggling-aanval|
|921120|HTTP-antwoord voor het splitsen van een aanval|
|921130|HTTP-antwoord voor het splitsen van een aanval|
|921140|Aanval via kopteksten via HTTP-headers|
|921150|HTTP-header-injectie aanval via Payload (CR/LF)|
|921151|HTTP-header-injectie aanval via Payload (CR/LF)|
|921160|HTTP-header-injectie aanval via Payload (CR/LF en header-name gedetecteerd)|
|921170|HTTP-parameter vervuiling|
|921180|HTTP-parameter vervuiling (% {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-31"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Beschrijving|
|---|---|
|930100|Pad overs passage (/. /)|
|930110|Pad overs passage (/. /)|
|930120|Poging tot toegang tot besturings systeem bestand|
|930130|Poging tot beperkte bestands toegang|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-31"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Beschrijving|
|---|---|
|931100|Mogelijke RFI-aanval (Remote File Include) = URL-para meter met IP-adres|
|931110|Mogelijke RFI-aanval (Remote File Include) = algemene RFI-kwets bare parameter naam die wordt gebruikt met de URL-nettolading|
|931120|Mogelijke RFI-aanval (Remote File Include) = URL Payload gebruikt met het vraag teken (?)|
|931130|Mogelijke RFI-aanval (Remote File Include) = Off-Domain referentie/koppeling|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-31"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Beschrijving|
|---|---|
|932100|Externe opdrachten uitvoeren: UNIX-opdracht injectie|
|932105|Externe opdrachten uitvoeren: UNIX-opdracht injectie|
|932106|Externe opdrachten uitvoeren: UNIX-opdracht injectie|
|932110|Externe uitvoering van opdracht: Windows-opdracht injectie|
|932115|Externe uitvoering van opdracht: Windows-opdracht injectie|
|932120|Externe opdracht uitvoering = Windows Power shell-opdracht gevonden|
|932130|Externe opdracht uitvoering = Unix-shell-expressie gevonden|
|932140|Externe opdracht uitvoering = Windows voor/als opdracht gevonden|
|932150|Externe opdrachten uitvoeren: direct UNIX-opdrachten uitvoeren|
|932160|Externe opdracht uitvoering = Unix shell-code gevonden|
|932170|Uitvoering van externe opdracht = Shellshock (CVE-2014-6271)|
|932171|Uitvoering van externe opdracht = Shellshock (CVE-2014-6271)|
|932180|Beperkte poging om bestanden te uploaden|
|932190|Externe uitvoering van opdracht: Joker teken techniek bypass-poging|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-31"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Beschrijving|
|---|---|
|933100|PHP-injectie aanval = openen/sluiten tag gevonden|
|933110|PHP-injectie aanval = PHP-script bestand gevonden|
|933111|Inbreker voor PHP-injectie: PHP-script bestand gevonden|
|933120|PHP-injectie aanval = configuratie-instructie gevonden|
|933130|PHP-injectie aanval = variabelen gevonden|
|933131|PHP-injectie aanval: variabelen gevonden|
|933140|PHP-injectie aanval: I/O-stroom gevonden|
|933150|PHP-injectie aanval = High-Risk PHP-functie naam gevonden|
|933151|PHP-injectie aanval: Medium-Risk PHP-functie naam gevonden|
|933160|PHP-injectie aanval = High-Risk PHP-functie aanroep gevonden|
|933161|PHP-injectie aanval: Low-Value PHP-functie aanroep gevonden|
|933170|Een PHP-injectie aanval: geserialiseerde object injectie|
|933180|PHP-injectie aanval = variabele functie aanroep gevonden|
|933190|PHP-injectie aanval: PHP-afsluit code gevonden|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-31"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Beschrijving|
|---|---|
|941100|XSS-aanval gedetecteerd via libinjection|
|941101|XSS-aanval gedetecteerd via libinjection|
|941110|XSS-filter-categorie 1 = script label vector|
|941130|XSS-filter-categorie 3 = kenmerk vector|
|941140|XSS-filter-categorie 4 = java script-URI-vector|
|941150|XSS-filter-categorie 5 = niet-toegestane HTML-kenmerken|
|941160|NoScript XSS InjectionChecker: HTML-injectie|
|941170|NoScript XSS InjectionChecker: kenmerk injectie|
|941180|Node-Validator blokkerings lijst-tref woorden|
|941190|XSS met Style Sheets|
|941200|XSS met behulp van VML-frames|
|941210|XSS met verborgen java script|
|941220|XSS met behulp van een verborgen VB-script|
|941230|XSS met behulp van de tag embed|
|941240|XSS met het kenmerk ' Import ' of ' implementatie '|
|941250|IE-XSS-filters-aanval gedetecteerd|
|941260|XSS met de tag ' meta '|
|941270|XSS met ' link ' href|
|941280|XSS met ' basis code '|
|941290|XSS met de tag ' applet '|
|941300|XSS met de tag object|
|941310|US-ASCII-verkeerd ingedeelde code ring XSS-filter-aanval gedetecteerd.|
|941320|Mogelijke XSS-aanval gedetecteerd-HTML-label-handler|
|941330|IE-XSS-filters-aanval gedetecteerd.|
|941340|IE-XSS-filters-aanval gedetecteerd.|
|941350|UTF-7-code ring IE XSS-aanval gedetecteerd.|


### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-31"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Beschrijving|
|---|---|
|942100|SQL-injectie aanval gedetecteerd via libinjection|
|942110|SQL-injectie aanval: gemeen schappelijke injectie tests gedetecteerd|
|942120|SQL-injectie aanval: SQL-operator gedetecteerd|
|942130|SQL-injectie aanval: SQL-Tautology gedetecteerd.|
|942140|SQL-injectie aanval = common DB-namen gedetecteerd|
|942150|SQL-injectie aanval|
|942160|Detecteert blind sqli tests met behulp van de slaap stand () of Bench Mark ().|
|942170|Detecteert SQL-benchmark-en slaapstandtoets-injectie pogingen, inclusief voorwaardelijke query's|
|942180|Detecteert eenvoudige SQL-verificatie pogingen 1/3|
|942190|Detecteert pogingen voor het uitvoeren van MSSQL-code en het verzamelen van informatie|
|942200|MySQL-commentaar gedetecteerd-/Space-obfuscated-injecties en apostroffen-beëindiging|
|942210|Detecteert geketende SQL-injectie pogingen 1/2|
|942220|Als u op zoek bent naar geheeltallige overflow-aanvallen, worden deze opgehaald van skipfish, met uitzonde ring van 3.0.00738585072|
|942230|Detecteert voorwaardelijke SQL-injectie pogingen|
|942240|MySQL-tekenset en MSSQL DoS-pogingen detecteren|
|942250|Detecteert overeenkomst met, samen VOEGen en uitvoeren van onmiddellijke injecties|
|942251|Detecteert of er injecties zijn|
|942260|Detecteert eenvoudige SQL-verificatie pogingen 2/3|
|942270|Er wordt gezocht naar eenvoudige SQL-injectie. Veelvoorkomende aanvals teken reeks voor mysql Oracle en andere|
|942280|Detecteert post gres pg_sleep injectie, waitfor-vertragings aanvallen en pogingen tot afsluiten van data base|
|942290|Hiermee vindt u eenvoudige MongoDB SQL-injectie pogingen|
|942300|Detecteert MySQL-opmerkingen, voor waarden en CH (a) r-injecties|
|942310|Detecteert geketende SQL-injectie pogingen 2/2|
|942320|Detecteert MySQL en opgeslagen procedure/functie-injecties van PostgreSQL|
|942330|Detecteert de klassieke SQL-injectie in protestes 1/2|
|942340|Detecteert eenvoudige SQL-verificatie pogingen 3/3|
|942350|Detecteert MySQL UDF-injectie en andere pogingen tot het manipuleren van gegevens/structuren|
|942360|Detecteert aaneengeschakelde Basic SQL-injectie en SQLLFI-pogingen|
|942361|Detecteert basis van SQL-injectie op basis van het sleutel woord Alter of Union|
|942370|Detecteert de klassieke SQL-injectie in protestes 2/2|
|942380|SQL-injectie aanval|
|942390|SQL-injectie aanval|
|942400|SQL-injectie aanval|
|942410|SQL-injectie aanval|
|942420|Beperkte afwijkings detectie van SQL-tekens (cookies): aantal speciale tekens is overschreden (8)|
|942421|Beperkte afwijkings detectie van SQL-tekens (cookies): aantal speciale tekens is overschreden (3)|
|942430|Beperkte afwijkings detectie voor SQL-tekens (argumenten): aantal speciale tekens is overschreden (12)|
|942431|Beperkte afwijkings detectie van SQL-tekens (argumenten): aantal speciale tekens is overschreden (6)|
|942432|Beperkte afwijkings detectie voor SQL-tekens (argumenten): aantal speciale tekens is overschreden (2)|
|942440|SQL-commentaar reeks gedetecteerd.|
|942450|SQL hex-code ring geïdentificeerd|
|942460|Waarschuwing voor detectie van afwijkings Meta-Character-terugkerende niet-woord tekens|
|942470|SQL-injectie aanval|
|942480|SQL-injectie aanval|
|942490|Detecteert de klassieke SQL-injectie in protestes 3/3|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-31"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Beschrijving|
|---|---|
|943100|Mogelijke sessie-bindings aanval = cookie waarden instellen in HTML|
|943110|Mogelijke sessie-bindings aanval = SessionID-parameter naam met Off-Domain verwijzings-id|
|943120|Mogelijke sessie-bindings aanval = SessionID-parameter naam zonder verwijzing|

### <a name="p-x-ms-format-detectionnonerequest-944-application-attack-session-javap"></a><a name="crs944-31"></a> <p x-ms-format-detection="none">AANVRAAG-944-TOEPASSING-AANVAL-SESSIE-JAVA</p>

|RuleId|Beschrijving|
|---|---|
|944120|Mogelijke Payload-uitvoering en uitvoering van externe opdrachten|
|944130|Verdachte Java-klassen|
|944200|Exploiting van Java-deserialisatie Apache Commons|

# <a name="owasp-30"></a>[OWASP 3,0](#tab/owasp30)

## <a name="rule-sets"></a><a name="owasp30"></a> Regel sets

### <a name="p-x-ms-format-detectionnonegeneralp"></a><a name="general-30"></a> <p x-ms-format-detection="none">Algemeen</p>

|RuleId|Beschrijving|
|---|---|
|200004|Mogelijke meerdelige niet-overeenkomende grens.|

### <a name="p-x-ms-format-detectionnonerequest-911-method-enforcementp"></a><a name="crs911-30"></a> <p x-ms-format-detection="none">REQUEST-911-METHOD-ENFORCEMENT</p>

|RuleId|Beschrijving|
|---|---|
|911100|De methode is niet toegestaan door het beleid|


### <a name="p-x-ms-format-detectionnonerequest-913-scanner-detectionp"></a><a name="crs913-30"></a> <p x-ms-format-detection="none">REQUEST-913-SCANNER-DETECTION</p>

|RuleId|Beschrijving|
|---|---|
|913100|Gevonden User-Agent gekoppeld aan beveiligings scanner|
|913110|De aanvraag header is gevonden die is gekoppeld aan de beveiligings scanner|
|913120|Bestands naam/argument van aanvraag gevonden die is gekoppeld aan beveiligings scanner|
|913101|User-Agent gekoppeld aan scripting/algemene HTTP-client|
|913102|Gevonden User-Agent gekoppeld aan web-crawler/bot|

### <a name="p-x-ms-format-detectionnonerequest-920-protocol-enforcementp"></a><a name="crs920-30"></a> <p x-ms-format-detection="none">REQUEST-920-PROTOCOL-ENFORCEMENT</p>

|RuleId|Beschrijving|
|---|---|
|920100|Ongeldige regel voor HTTP-aanvraag|
|920130|De aanvraag tekst kan niet worden geparseerd.|
|920140|Strikte validatie van de aanvraag tekst voor meerdelige is mislukt|
|920160|De HTTP-header van de content-length is niet numeriek.|
|920170|GET-of HEAD-aanvraag met hoofd inhoud.|
|920180|POST-aanvraag mist header content-length.|
|920190|Range = ongeldige waarde voor laatste byte.|
|920210|Er zijn meerdere/conflicterende header gegevens gevonden.|
|920220|Poging tot misbruik van URL-code ring|
|920240|Poging tot misbruik van URL-code ring|
|920250|Poging tot misbruik van UTF8-code ring|
|920260|Volledige/halve breedte van Unicode misbruik van aanvallen|
|920270|Ongeldig teken in aanvraag (null-teken)|
|920280|Aanvraag voor ontbrekende host-header|
|920290|Lege host-header|
|920310|De aanvraag bevat een lege Accept-header|
|920311|De aanvraag bevat een lege Accept-header|
|920330|Lege koptekst gebruikers agent|
|920340|Aanvraag met inhoud, maar ontbrekende content-type-header|
|920350|Host-header is een numeriek IP-adres|
|920380|De aanvraag heeft te veel argumenten|
|920360|Argument naam is te lang|
|920370|Argument waarde te lang|
|920390|Totale grootte van argumenten is overschreden|
|920400|Geüploade bestands grootte te groot|
|920410|Totale grootte van geüploade bestanden te groot|
|920420|Het inhouds type van de aanvraag is niet toegestaan door het beleid|
|920430|De versie van het HTTP-protocol wordt niet toegestaan door het beleid|
|920440|De extensie van het URL-bestand wordt beperkt door het beleid|
|920450|De HTTP-header wordt beperkt door het beleid (% @ {MATCHED_VAR})|
|920200|Range = te veel velden (6 of meer)|
|920201|Range = te veel velden voor PDF-aanvraag (35 of meer)|
|920230|Meerdere URL-code ring gedetecteerd|
|920300|Aanvraag waarbij een Accept-header ontbreekt|
|920271|Ongeldig teken in de aanvraag (niet-afdruk bare tekens)|
|920320|Ontbrekende koptekst gebruikers agent|
|920272|Ongeldig teken in de aanvraag (buiten afdruk bare tekens onder ASCII 127)|
|920202|Range = te veel velden voor PDF-aanvraag (6 of meer)|
|920273|Ongeldig teken in aanvraag (buiten strikt ingesteld)|
|920274|Ongeldig teken in de aanvraag headers (buiten een zeer strikte set)|
|920460|Abnormale escape tekens|

### <a name="p-x-ms-format-detectionnonerequest-921-protocol-attackp"></a><a name="crs921-30"></a> <p x-ms-format-detection="none">REQUEST-921-PROTOCOL-ATTACK</p>

|RuleId|Beschrijving|
|---|---|
|921100|HTTP-aanvraag smuggling-aanval.|
|921110|HTTP-aanvraag smuggling-aanval|
|921120|HTTP-antwoord voor het splitsen van een aanval|
|921130|HTTP-antwoord voor het splitsen van een aanval|
|921140|Aanval via kopteksten via HTTP-headers|
|921150|HTTP-header-injectie aanval via Payload (CR/LF)|
|921160|HTTP-header-injectie aanval via Payload (CR/LF en header-name gedetecteerd)|
|921151|HTTP-header-injectie aanval via Payload (CR/LF)|
|921170|HTTP-parameter vervuiling|
|921180|HTTP-parameter vervuiling (% @ {TX. 1})|

### <a name="p-x-ms-format-detectionnonerequest-930-application-attack-lfip"></a><a name="crs930-30"></a> <p x-ms-format-detection="none">REQUEST-930-APPLICATION-ATTACK-LFI</p>

|RuleId|Beschrijving|
|---|---|
|930100|Pad overs passage (/. /)|
|930110|Pad overs passage (/. /)|
|930120|Poging tot toegang tot besturings systeem bestand|
|930130|Poging tot beperkte bestands toegang|

### <a name="p-x-ms-format-detectionnonerequest-931-application-attack-rfip"></a><a name="crs931-30"></a> <p x-ms-format-detection="none">REQUEST-931-APPLICATION-ATTACK-RFI</p>

|RuleId|Beschrijving|
|---|---|
|931100|Mogelijke RFI-aanval (Remote File Include) = URL-para meter met IP-adres|
|931110|Mogelijke RFI-aanval (Remote File Include) = algemene RFI-kwets bare parameter naam die wordt gebruikt met de URL-nettolading|
|931120|Mogelijke RFI-aanval (Remote File Include) = URL Payload gebruikt met het vraag teken (?)|
|931130|Mogelijke RFI-aanval (Remote File Include) = Off-Domain referentie/koppeling|

### <a name="p-x-ms-format-detectionnonerequest-932-application-attack-rcep"></a><a name="crs932-30"></a> <p x-ms-format-detection="none">REQUEST-932-APPLICATION-ATTACK-RCE</p>

|RuleId|Beschrijving|
|---|---|
|932120|Externe opdracht uitvoering = Windows Power shell-opdracht gevonden|
|932130|Externe opdracht uitvoering = Unix-shell-expressie gevonden|
|932140|Externe opdracht uitvoering = Windows voor/als opdracht gevonden|
|932160|Externe opdracht uitvoering = Unix shell-code gevonden|
|932170|Uitvoering van externe opdracht = Shellshock (CVE-2014-6271)|
|932171|Uitvoering van externe opdracht = Shellshock (CVE-2014-6271)|

### <a name="p-x-ms-format-detectionnonerequest-933-application-attack-phpp"></a><a name="crs933-30"></a> <p x-ms-format-detection="none">REQUEST-933-APPLICATION-ATTACK-PHP</p>

|RuleId|Beschrijving|
|---|---|
|933100|PHP-injectie aanval = openen/sluiten tag gevonden|
|933110|PHP-injectie aanval = PHP-script bestand gevonden|
|933120|PHP-injectie aanval = configuratie-instructie gevonden|
|933130|PHP-injectie aanval = variabelen gevonden|
|933150|PHP-injectie aanval = High-Risk PHP-functie naam gevonden|
|933160|PHP-injectie aanval = High-Risk PHP-functie aanroep gevonden|
|933180|PHP-injectie aanval = variabele functie aanroep gevonden|
|933151|PHP-injectie aanval = Medium-Risk PHP-functie naam gevonden|
|933131|PHP-injectie aanval = variabelen gevonden|
|933161|PHP-injectie aanval = Low-Value PHP-functie aanroep gevonden|
|933111|PHP-injectie aanval = PHP-script bestand gevonden|

### <a name="p-x-ms-format-detectionnonerequest-941-application-attack-xssp"></a><a name="crs941-30"></a> <p x-ms-format-detection="none">REQUEST-941-APPLICATION-ATTACK-XSS</p>

|RuleId|Beschrijving|
|---|---|
|941100|XSS-aanval gedetecteerd via libinjection|
|941110|XSS-filter-categorie 1 = script label vector|
|941130|XSS-filter-categorie 3 = kenmerk vector|
|941140|XSS-filter-categorie 4 = java script-URI-vector|
|941150|XSS-filter-categorie 5 = niet-toegestane HTML-kenmerken|
|941180|Node-Validator blokkerings lijst-tref woorden|
|941190|XSS met Style Sheets|
|941200|XSS met behulp van VML-frames|
|941210|XSS met verborgen java script|
|941220|XSS met behulp van een verborgen VB-script|
|941230|XSS met behulp van de tag embed|
|941240|XSS met het kenmerk ' Import ' of ' implementatie '|
|941260|XSS met de tag ' meta '|
|941270|XSS met ' link ' href|
|941280|XSS met ' basis code '|
|941290|XSS met de tag ' applet '|
|941300|XSS met de tag object|
|941310|US-ASCII-verkeerd ingedeelde code ring XSS-filter-aanval gedetecteerd.|
|941330|IE-XSS-filters-aanval gedetecteerd.|
|941340|IE-XSS-filters-aanval gedetecteerd.|
|941350|UTF-7-code ring IE XSS-aanval gedetecteerd.|
|941320|Mogelijke XSS-aanval gedetecteerd-HTML-label-handler|

### <a name="p-x-ms-format-detectionnonerequest-942-application-attack-sqlip"></a><a name="crs942-30"></a> <p x-ms-format-detection="none">REQUEST-942-APPLICATION-ATTACK-SQLI</p>

|RuleId|Beschrijving|
|---|---|
|942100|SQL-injectie aanval gedetecteerd via libinjection|
|942110|SQL-injectie aanval: gemeen schappelijke injectie tests gedetecteerd|
|942130|SQL-injectie aanval: SQL-Tautology gedetecteerd.|
|942140|SQL-injectie aanval = common DB-namen gedetecteerd|
|942160|Detecteert blind sqli tests met behulp van de slaap stand () of Bench Mark ().|
|942170|Detecteert SQL-benchmark-en slaapstandtoets-injectie pogingen, inclusief voorwaardelijke query's|
|942190|Detecteert pogingen voor het uitvoeren van MSSQL-code en het verzamelen van informatie|
|942200|MySQL-commentaar gedetecteerd-/Space-obfuscated-injecties en apostroffen-beëindiging|
|942230|Detecteert voorwaardelijke SQL-injectie pogingen|
|942260|Detecteert eenvoudige SQL-verificatie pogingen 2/3|
|942270|Er wordt gezocht naar eenvoudige SQL-injectie. Veelvoorkomende aanvals teken reeks voor mysql Oracle en andere.|
|942290|Hiermee vindt u eenvoudige MongoDB SQL-injectie pogingen|
|942300|Detecteert MySQL-opmerkingen, voor waarden en CH (a) r-injecties|
|942310|Detecteert geketende SQL-injectie pogingen 2/2|
|942320|Detecteert MySQL en opgeslagen procedure/functie-injecties van PostgreSQL|
|942330|Detecteert de klassieke SQL-injectie in protestes 1/2|
|942340|Detecteert eenvoudige SQL-verificatie pogingen 3/3|
|942350|Detecteert MySQL UDF-injectie en andere pogingen tot het manipuleren van gegevens/structuren|
|942360|Detecteert aaneengeschakelde Basic SQL-injectie en SQLLFI-pogingen|
|942370|Detecteert de klassieke SQL-injectie in protestes 2/2|
|942150|SQL-injectie aanval|
|942410|SQL-injectie aanval|
|942430|Beperkte afwijkings detectie voor SQL-tekens (argumenten): aantal speciale tekens is overschreden (12)|
|942440|SQL-commentaar reeks gedetecteerd.|
|942450|SQL hex-code ring geïdentificeerd|
|942251|Detecteert of er injecties zijn|
|942460|Waarschuwing voor detectie van afwijkings Meta-Character-terugkerende niet-woord tekens|

### <a name="p-x-ms-format-detectionnonerequest-943-application-attack-session-fixationp"></a><a name="crs943-30"></a> <p x-ms-format-detection="none">REQUEST-943-APPLICATION-ATTACK-SESSION-FIXATION</p>

|RuleId|Beschrijving|
|---|---|
|943100|Mogelijke sessie-bindings aanval = cookie waarden instellen in HTML|
|943110|Mogelijke sessie-bindings aanval = SessionID-parameter naam met Off-Domain verwijzings-id|
|943120|Mogelijke sessie-bindings aanval = SessionID-parameter naam zonder verwijzing|

# <a name="owasp-229"></a>[OWASP 2.2.9](#tab/owasp2)

## <a name="rule-sets"></a><a name="owasp229"></a> Regel sets

### <a name="crs_20_protocol_violations"></a><a name="crs20"></a> crs_20_protocol_violations

|RuleId|Beschrijving|
|---|---|
|960911|Ongeldige regel voor HTTP-aanvraag|
|981227|Apache-fout = ongeldige URI in aanvraag.|
|960912|De aanvraag tekst kan niet worden geparseerd.|
|960914|Strikte validatie van de aanvraag tekst voor meerdelige is mislukt|
|960915|De meerdelige parser heeft een mogelijke niet-overeenkomende grens gedetecteerd.|
|960016|De HTTP-header van de content-length is niet numeriek.|
|960011|GET-of HEAD-aanvraag met hoofd inhoud.|
|960012|POST-aanvraag mist header content-length.|
|960902|Ongeldig gebruik van id-code ring.|
|960022|De verwachte header is niet toegestaan voor HTTP 1,0.|
|960020|Voor de Pragma-header is Cache-Control header vereist voor HTTP/1.1-aanvragen.|
|958291|Het veld Range = bestaat en begint met 0.|
|958230|Range = ongeldige waarde voor laatste byte.|
|958295|Er zijn meerdere/conflicterende header gegevens gevonden.|
|950107|Poging tot misbruik van URL-code ring|
|950109|Meerdere URL-code ring gedetecteerd|
|950108|Poging tot misbruik van URL-code ring|
|950801|Poging tot misbruik van UTF8-code ring|
|950116|Volledige/halve breedte van Unicode misbruik van aanvallen|
|960901|Ongeldig teken in de aanvraag|
|960018|Ongeldig teken in de aanvraag|

### <a name="crs_21_protocol_anomalies"></a><a name="crs21"></a> crs_21_protocol_anomalies

|RuleId|Beschrijving|
|---|---|
|960008|Aanvraag voor ontbrekende host-header|
|960007|Lege host-header|
|960015|Aanvraag waarbij een Accept-header ontbreekt|
|960021|De aanvraag bevat een lege Accept-header|
|960009|Aanvraag voor ontbrekende header van gebruikers agent|
|960006|Lege koptekst gebruikers agent|
|960904|Aanvraag met inhoud, maar ontbrekende content-type-header|
|960017|Host-header is een numeriek IP-adres|

### <a name="crs_23_request_limits"></a><a name="crs23"></a> crs_23_request_limits

|RuleId|Beschrijving|
|---|---|
|960209|Argument naam is te lang|
|960208|Argument waarde te lang|
|960335|De aanvraag heeft te veel argumenten|
|960341|Totale grootte van argumenten is overschreden|
|960342|Geüploade bestands grootte te groot|
|960343|Totale grootte van geüploade bestanden te groot|

### <a name="crs_30_http_policy"></a><a name="crs30"></a> crs_30_http_policy

|RuleId|Beschrijving|
|---|---|
|960032|De methode is niet toegestaan door het beleid|
|960010|Het inhouds type van de aanvraag is niet toegestaan door het beleid|
|960034|De versie van het HTTP-protocol wordt niet toegestaan door het beleid|
|960035|De extensie van het URL-bestand wordt beperkt door het beleid|
|960038|De HTTP-header wordt beperkt door het beleid|

### <a name="crs_35_bad_robots"></a><a name="crs35"></a> crs_35_bad_robots

|RuleId|Beschrijving|
|---|---|
|990002|Aanvraag geeft aan dat een beveiligings scanner de site heeft gescand|
|990901|Aanvraag geeft aan dat een beveiligings scanner de site heeft gescand|
|990902|Aanvraag geeft aan dat een beveiligings scanner de site heeft gescand|
|990012|Webcrawler voor Rogue web site|

### <a name="crs_40_generic_attacks"></a><a name="crs40"></a> crs_40_generic_attacks

|RuleId|Beschrijving|
|---|---|
|960024|Waarschuwing voor detectie van afwijkings Meta-Character-terugkerende niet-woord tekens|
|950008|Injectie van niet-gedocumenteerde ColdFusion-Tags|
|950010|LDAP-injectie aanval|
|950011|SSI-injectie aanval|
|950018|URL van Universal PDF XSS gedetecteerd.|
|950019|Aanval via e-mail injecteren|
|950012|HTTP-aanvraag smuggling-aanval.|
|950910|HTTP-antwoord voor het splitsen van een aanval|
|950911|HTTP-antwoord voor het splitsen van een aanval|
|950117|Aanval op externe bestands opname|
|950118|Aanval op externe bestands opname|
|950119|Aanval op externe bestands opname|
|950120|Mogelijke RFI-aanval (Remote File Include) = Off-Domain referentie/koppeling|
|981133|Regel 981133|
|981134|Regel 981134|
|950009|Aanval op sessie binding|
|950003|Sessie-vastleg ging|
|950000|Sessie-vastleg ging|
|950005|Poging tot externe bestands toegang|
|950002|Toegang tot de systeem opdracht|
|950006|Systeem opdracht injectie|
|959151|PHP-injectie aanval|
|958976|PHP-injectie aanval|
|958977|PHP-injectie aanval|

### <a name="crs_41_sql_injection_attacks"></a><a name="crs41sql"></a> crs_41_sql_injection_attacks

|RuleId|Beschrijving|
|---|---|
|981231|SQL-commentaar reeks gedetecteerd.|
|981260|SQL hex-code ring geïdentificeerd|
|981320|SQL-injectie aanval = common DB-namen gedetecteerd|
|981300|Regel 981300|
|981301|Regel 981301|
|981302|Regel 981302|
|981303|Regel 981303|
|981304|Regel 981304|
|981305|Regel 981305|
|981306|Regel 981306|
|981307|Regel 981307|
|981308|Regel 981308|
|981309|Regel 981309|
|981310|Regel 981310|
|981311|Regel 981311|
|981312|Regel 981312|
|981313|Regel 981313|
|981314|Regel 981314|
|981315|Regel 981315|
|981316|Regel 981316|
|981317|Waarschuwing voor afwijkings detectie van de SQL-instructie SELECT|
|950007|Aanval op een blinde SQL-injectie|
|950001|SQL-injectie aanval|
|950908|SQL-injectie aanval.|
|959073|SQL-injectie aanval|
|981272|Detecteert blind sqli tests met behulp van de slaap stand () of Bench Mark ().|
|981250|Detecteert SQL-benchmark-en slaapstandtoets-injectie pogingen, inclusief voorwaardelijke query's|
|981241|Detecteert voorwaardelijke SQL-injectie pogingen|
|981276|Er wordt gezocht naar eenvoudige SQL-injectie. Veelvoorkomende aanvals teken reeks voor mysql Oracle en andere.|
|981270|Hiermee vindt u eenvoudige MongoDB SQL-injectie pogingen|
|981253|Detecteert MySQL en opgeslagen procedure/functie-injecties van PostgreSQL|
|981251|Detecteert MySQL UDF-injectie en andere pogingen tot het manipuleren van gegevens/structuren|

### <a name="crs_41_xss_attacks"></a><a name="crs41xss"></a> crs_41_xss_attacks

|RuleId|Beschrijving|
|---|---|
|973336|XSS-filter-categorie 1 = script label vector|
|973338|XSS-filter-categorie 3 = java script-URI-vector|
|981136|Regel 981136|
|981018|Regel 981018|
|958016|XSS-aanvallen (cross-site scripting)|
|958414|XSS-aanvallen (cross-site scripting)|
|958032|XSS-aanvallen (cross-site scripting)|
|958026|XSS-aanvallen (cross-site scripting)|
|958027|XSS-aanvallen (cross-site scripting)|
|958054|XSS-aanvallen (cross-site scripting)|
|958418|XSS-aanvallen (cross-site scripting)|
|958034|XSS-aanvallen (cross-site scripting)|
|958019|XSS-aanvallen (cross-site scripting)|
|958013|XSS-aanvallen (cross-site scripting)|
|958408|XSS-aanvallen (cross-site scripting)|
|958012|XSS-aanvallen (cross-site scripting)|
|958423|XSS-aanvallen (cross-site scripting)|
|958002|XSS-aanvallen (cross-site scripting)|
|958017|XSS-aanvallen (cross-site scripting)|
|958007|XSS-aanvallen (cross-site scripting)|
|958047|XSS-aanvallen (cross-site scripting)|
|958410|XSS-aanvallen (cross-site scripting)|
|958415|XSS-aanvallen (cross-site scripting)|
|958022|XSS-aanvallen (cross-site scripting)|
|958405|XSS-aanvallen (cross-site scripting)|
|958419|XSS-aanvallen (cross-site scripting)|
|958028|XSS-aanvallen (cross-site scripting)|
|958057|XSS-aanvallen (cross-site scripting)|
|958031|XSS-aanvallen (cross-site scripting)|
|958006|XSS-aanvallen (cross-site scripting)|
|958033|XSS-aanvallen (cross-site scripting)|
|958038|XSS-aanvallen (cross-site scripting)|
|958409|XSS-aanvallen (cross-site scripting)|
|958001|XSS-aanvallen (cross-site scripting)|
|958005|XSS-aanvallen (cross-site scripting)|
|958404|XSS-aanvallen (cross-site scripting)|
|958023|XSS-aanvallen (cross-site scripting)|
|958010|XSS-aanvallen (cross-site scripting)|
|958411|XSS-aanvallen (cross-site scripting)|
|958422|XSS-aanvallen (cross-site scripting)|
|958036|XSS-aanvallen (cross-site scripting)|
|958000|XSS-aanvallen (cross-site scripting)|
|958018|XSS-aanvallen (cross-site scripting)|
|958406|XSS-aanvallen (cross-site scripting)|
|958040|XSS-aanvallen (cross-site scripting)|
|958052|XSS-aanvallen (cross-site scripting)|
|958037|XSS-aanvallen (cross-site scripting)|
|958049|XSS-aanvallen (cross-site scripting)|
|958030|XSS-aanvallen (cross-site scripting)|
|958041|XSS-aanvallen (cross-site scripting)|
|958416|XSS-aanvallen (cross-site scripting)|
|958024|XSS-aanvallen (cross-site scripting)|
|958059|XSS-aanvallen (cross-site scripting)|
|958417|XSS-aanvallen (cross-site scripting)|
|958020|XSS-aanvallen (cross-site scripting)|
|958045|XSS-aanvallen (cross-site scripting)|
|958004|XSS-aanvallen (cross-site scripting)|
|958421|XSS-aanvallen (cross-site scripting)|
|958009|XSS-aanvallen (cross-site scripting)|
|958025|XSS-aanvallen (cross-site scripting)|
|958413|XSS-aanvallen (cross-site scripting)|
|958051|XSS-aanvallen (cross-site scripting)|
|958420|XSS-aanvallen (cross-site scripting)|
|958407|XSS-aanvallen (cross-site scripting)|
|958056|XSS-aanvallen (cross-site scripting)|
|958011|XSS-aanvallen (cross-site scripting)|
|958412|XSS-aanvallen (cross-site scripting)|
|958008|XSS-aanvallen (cross-site scripting)|
|958046|XSS-aanvallen (cross-site scripting)|
|958039|XSS-aanvallen (cross-site scripting)|
|958003|XSS-aanvallen (cross-site scripting)|
|973300|Mogelijke XSS-aanval gedetecteerd-HTML-label-handler|
|973301|XSS-aanval gedetecteerd|
|973302|XSS-aanval gedetecteerd|
|973303|XSS-aanval gedetecteerd|
|973304|XSS-aanval gedetecteerd|
|973305|XSS-aanval gedetecteerd|
|973306|XSS-aanval gedetecteerd|
|973307|XSS-aanval gedetecteerd|
|973308|XSS-aanval gedetecteerd|
|973309|XSS-aanval gedetecteerd|
|973311|XSS-aanval gedetecteerd|
|973313|XSS-aanval gedetecteerd|
|973314|XSS-aanval gedetecteerd|
|973331|IE-XSS-filters-aanval gedetecteerd.|
|973315|IE-XSS-filters-aanval gedetecteerd.|
|973330|IE-XSS-filters-aanval gedetecteerd.|
|973327|IE-XSS-filters-aanval gedetecteerd.|
|973326|IE-XSS-filters-aanval gedetecteerd.|
|973346|IE-XSS-filters-aanval gedetecteerd.|
|973345|IE-XSS-filters-aanval gedetecteerd.|
|973324|IE-XSS-filters-aanval gedetecteerd.|
|973323|IE-XSS-filters-aanval gedetecteerd.|
|973348|IE-XSS-filters-aanval gedetecteerd.|
|973321|IE-XSS-filters-aanval gedetecteerd.|
|973320|IE-XSS-filters-aanval gedetecteerd.|
|973318|IE-XSS-filters-aanval gedetecteerd.|
|973317|IE-XSS-filters-aanval gedetecteerd.|
|973329|IE-XSS-filters-aanval gedetecteerd.|
|973328|IE-XSS-filters-aanval gedetecteerd.|

### <a name="crs_42_tight_security"></a><a name="crs42"></a> crs_42_tight_security

|RuleId|Beschrijving|
|---|---|
|950103|Inbreuk op pad|

### <a name="crs_45_trojans"></a><a name="crs45"></a> crs_45_trojans

|RuleId|Beschrijving|
|---|---|
|950110|Back-uptoegang|
|950921|Back-uptoegang|
|950922|Back-uptoegang|

---

## <a name="next-steps"></a>Volgende stappen

- [De firewall regels voor web-apps aanpassen met behulp van de Azure Portal](application-gateway-customize-waf-rules-portal.md)

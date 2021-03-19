---
title: Regels aanpassen met behulp van portal-Azure Web Application firewall
description: Dit artikel bevat informatie over het aanpassen van de firewall regels voor webtoepassingen in Application Gateway met de Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: c4635333614ee1c0fd0322c29a659380fb4315c9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74048379"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>De firewall regels voor web-apps aanpassen met behulp van de Azure Portal

De Azure-toepassing gateway Web Application firewall (WAF) biedt beveiliging voor webtoepassingen. Deze beveiligingen worden verzorgd door de OWASP (open Web Application Security project) kern regelset (CRS). Sommige regels kunnen valse positieven veroorzaken en werkelijk verkeer blok keren. Daarom biedt Application Gateway de mogelijkheid om regel groepen en regels aan te passen. Zie voor meer informatie over de specifieke regel groepen en regels de [lijst met CRS-regel groepen en-regels voor Web Application firewall](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> Als uw toepassings gateway geen gebruik maakt van de WAF-laag, wordt de optie voor het bijwerken van de toepassings gateway naar de laag WAF weer gegeven in het rechterdeel venster. 

![WAF inschakelen][fig1]

## <a name="view-rule-groups-and-rules"></a>Regel groepen en-regels weer geven

**Regel groepen en-regels weer geven**
1. Blader naar de Application Gateway en selecteer **Web Application firewall**.  
2. Selecteer uw **WAF-beleid**.
2. Selecteer **beheerde regels**.

   In deze weer gave ziet u een tabel op de pagina van alle regel groepen die zijn opgegeven met de gekozen regelset. Alle selectie vakjes van de regel zijn geselecteerd.

## <a name="disable-rule-groups-and-rules"></a>Regel groepen en regels uitschakelen

> [!IMPORTANT]
> Wees voorzichtig met het uitschakelen van regel groepen of regels. Dit kan u bloot stellen aan verhoogde beveiligings Risico's.

Wanneer u regels uitschakelt, kunt u een volledige regel groep of specifieke regels onder een of meer regel groepen uitschakelen. 

**Regel groepen of specifieke regels uitschakelen**

   1. Zoek naar de regels of regel groepen die u wilt uitschakelen.
   2. Schakel de selectie vakjes in voor de regels die u wilt uitschakelen. 
   3. Selecteer de actie boven aan de pagina (inschakelen/uitschakelen) voor de geselecteerde regels.
   2. Selecteer **Opslaan**. 

![Wijzigingen opslaan][3]

## <a name="mandatory-rules"></a>Regels verplicht

De volgende lijst bevat voor waarden die ervoor zorgen dat de WAF de aanvraag in de modus voor preventie blokkeert. In de detectie modus worden ze geregistreerd als uitzonde ringen.

Deze kunnen niet worden geconfigureerd of uitgeschakeld:

* Fout bij het parseren van de aanvraag tekst leidt ertoe dat de aanvraag wordt geblokkeerd, tenzij de hoofd inspectie is uitgeschakeld (XML-, JSON-, formulier gegevens)
* De gegevens lengte van de aanvraag tekst (zonder bestanden) is groter dan de geconfigureerde limiet
* De aanvraag tekst (inclusief bestanden) is groter dan de limiet
* Er is een interne fout opgetreden in de WAF-engine

CRS 3. x specifiek:

* Drempel waarde voor binnenkomende afwijkings Score overschreden

## <a name="next-steps"></a>Volgende stappen

Nadat u de uitgeschakelde regels hebt geconfigureerd, kunt u meer informatie over het weer geven van uw WAF-logboeken bekijken. Zie [Application Gateway Diagnostics (diagnostische](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)gegevens) voor meer informatie.

[fig1]: ../media/application-gateway-customize-waf-rules-portal/1.png
[3]: ../media/application-gateway-customize-waf-rules-portal/figure3.png

---
title: Regels aanpassen met behulp van de portal - Azure Web Application Firewall
description: Dit artikel bevat informatie over het aanpassen van Web Application Firewall regels in Application Gateway met de Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 04/21/2021
ms.author: victorh
ms.topic: article
ms.openlocfilehash: 0ab122d178e5390a53e5a3a39f1b7763b298dc6d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878322"
---
# <a name="customize-web-application-firewall-rules-using-the-azure-portal"></a>Pas Web Application Firewall regels aan met behulp van de Azure Portal

De Azure Application Gateway Web Application Firewall (WAF) biedt beveiliging voor webtoepassingen. Deze beveiligingen worden geboden door de Open Web Application Security Project (OWASP) Core Rule Set (CRS). Sommige regels kunnen fout-positieven veroorzaken en echt verkeer blokkeren. Daarom biedt Application Gateway de mogelijkheid om regelgroepen en regels aan te passen. Zie List of Web Application Firewall CRS rule groups and rules (Lijst met crs-regelgroepen en -regels) voor meer informatie over de specifieke [regelgroepen en regels.](application-gateway-crs-rulegroups-rules.md)

>[!NOTE]
> Als uw toepassingsgateway niet gebruik maakt van de WAF-laag, wordt de optie voor het upgraden van de toepassingsgateway naar de WAF-laag weergegeven in het rechterdeelvenster. 

:::image type="content" source="../media/application-gateway-customize-waf-rules-portal/1.png" alt-text="WAF inschakelen"::: 

## <a name="view-rule-groups-and-rules"></a>Regelgroepen en regels weergeven

**Regelgroepen en regels weergeven**
1. Blader naar de toepassingsgateway en selecteer vervolgens **Web Application Firewall.**  
2. Selecteer uw **WAF-beleid.**
2. Selecteer **Beheerde regels.**

   In deze weergave ziet u een tabel op de pagina van alle regelgroepen die zijn opgegeven met de gekozen regelset. Alle selectievakjes van de regel zijn geselecteerd.

## <a name="disable-rule-groups-and-rules"></a>Regelgroepen en regels uitschakelen

> [!IMPORTANT]
> Wees voorzichtig bij het uitschakelen van regelgroepen of regels. Dit kan leiden tot verhoogde beveiligingsrisico's.

**Regelgroepen of specifieke regels uitschakelen**

   1. Zoek naar de regels of regelgroepen die u wilt uitschakelen.
   2. Schakel de selectievakjes in voor de regels die u wilt uitschakelen. 
   3. Selecteer de actie boven aan de pagina (in-/uitschakelen) voor de geselecteerde regels.
   2. Selecteer **Opslaan**.
    :::image type="content" source="../media/application-gateway-customize-waf-rules-portal/figure3.png" alt-text="Uitgeschakelde regels opslaan"::: 

## <a name="mandatory-rules"></a>Verplichte regels

De volgende lijst bevat voorwaarden die ervoor zorgen dat de WAF de aanvraag blokkeert in de preventiemodus. In de detectiemodus worden ze geregistreerd als uitzonderingen.

Deze kunnen niet worden geconfigureerd of uitgeschakeld:

* Als de aanvraag niet kan worden geparseerd, wordt de aanvraag geblokkeerd, tenzij de controle van de body is uitgeschakeld (XML, JSON, formuliergegevens)
* De gegevenslengte van de aanvraag body (zonder bestanden) is groter dan de geconfigureerde limiet
* Aanvraag body (inclusief bestanden) is groter dan de limiet
* Er is een interne fout opgetreden in de WAF-engine

CRS 3.x specifiek:

* Inkomende anomaliescore heeft drempelwaarde overschreden

## <a name="next-steps"></a>Volgende stappen

Nadat u de uitgeschakelde regels hebt geconfigureerd, kunt u leren hoe u uw WAF-logboeken kunt weergeven. Zie diagnostische Application Gateway [voor meer informatie.](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)
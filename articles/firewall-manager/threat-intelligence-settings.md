---
title: Configuratie van Azure Firewall Threat Intelligence
description: Meer informatie over het configureren van op Threat Intelligence gebaseerde filtering voor uw Azure Firewall-beleid om verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa leren en weigeren.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651715"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Configuratie van Azure Firewall Threat Intelligence

Op bedreigingen gebaseerd filteren kan worden geconfigureerd voor uw Azure Firewall-beleid om verkeer van en naar bekende schadelijke IP-adressen en domeinen te Signa lering en te weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) voorziet in micro soft Threat Intelligence en wordt gebruikt door meerdere services, waaronder Azure Security Center.<br>

Als u op bedreigingen gebaseerd filteren hebt geconfigureerd, worden de bijbehorende regels verwerkt vóór een van de NAT-regels, netwerk regels of toepassings regels.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Beleid voor bedreigings informatie":::

## <a name="threat-intelligence-mode"></a>Threat Intelligence-modus

U kunt Threat Intelligence configureren in een van de drie modi die in de volgende tabel worden beschreven. Op bedreigingen gebaseerd filteren is standaard ingeschakeld in de waarschuwings modus.

|Modus |Description  |
|---------|---------|
|`Off`     | De functie voor bedreigings informatie is niet ingeschakeld voor uw firewall. |
|`Alert only`     | U ontvangt waarschuwingen met hoge betrouw baarheid voor verkeer dat via uw firewall wordt verzonden naar of van bekende schadelijke IP-adressen en domeinen. |
|`Alert and deny`     | Verkeer wordt geblokkeerd en u ontvangt waarschuwingen met hoge betrouw baarheid wanneer er verkeer wordt gedetecteerd, waarbij wordt geprobeerd uw firewall door te lopen naar of van bekende schadelijke IP-adressen en domeinen. |

> [!NOTE]
> De modus voor bedreigings informatie wordt overgenomen van het bovenliggende beleid naar het onderliggende beleid. Een onderliggend beleid moet worden geconfigureerd met dezelfde of een strengere modus dan het bovenliggende beleid.

## <a name="allowlist-addresses"></a>Allowlist-adressen

Bedreigings informatie kan fout-positieven activeren en het verkeer blok keren dat daad werkelijk geldig is. U kunt een lijst met toegestane IP-adressen configureren zodat bedreigings informatie geen van de adressen, bereiken of subnetten die u opgeeft, filtert.  

![Allowlist-adressen](media/threat-intelligence-settings/allow-list.png)

U kunt de allowlist met meerdere vermeldingen tegelijk bijwerken door een CSV-bestand te uploaden. Het CSV-bestand kan alleen IP-adressen en-bereiken bevatten. Het bestand mag geen koppen bevatten.

> [!NOTE]
> Allowlist-adressen van bedreigings informatie worden overgenomen van het bovenliggende beleid naar het onderliggende beleid. Elk IP-adres of bereik dat is toegevoegd aan een bovenliggend beleid, is ook van toepassing op alle onderliggende beleids regels.

## <a name="logs"></a>Logboeken

Het volgende logboek bestand bevat een geactiveerde regel voor uitgaand verkeer naar een schadelijke site:

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testen

- **Uitgaande tests** : waarschuwingen voor uitgaand verkeer moeten een zeldzame gebeurtenis zijn, omdat het betekent dat uw omgeving is aangetast. Er is een test-FQDN gemaakt waarmee een waarschuwing wordt geactiveerd om te voor komen dat uitgaande waarschuwingen worden uitgevoerd. Gebruik **testmaliciousdomain.eastus.cloudapp.Azure.com** voor uw uitgaande tests.

- **Inkomend testen** : u kunt verwachten dat er waarschuwingen worden weer geven voor binnenkomend verkeer als DNAT-regels zijn geconfigureerd op de firewall. Dit geldt ook als alleen specifieke bronnen zijn toegestaan in de DNAT-regel en verkeer anderszins wordt geweigerd. Azure Firewall geen waarschuwing voor alle bekende poort scanners. alleen op scanners waarvan bekend is dat ze ook schadelijke activiteiten kunnen uitvoeren.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg het [micro soft Security Intelligence-rapport](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)

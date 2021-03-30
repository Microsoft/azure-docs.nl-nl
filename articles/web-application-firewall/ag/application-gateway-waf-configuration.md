---
title: Grootte limieten en uitsluitings lijsten voor Web Application firewall in Azure-toepassing gateway-Azure Portal
description: In dit artikel vindt u informatie over de maximale grootte van de Web Application firewall-aanvraag en de configuratie van uitsluitings lijsten in Application Gateway met de Azure Portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 2d34641fdecfe334e84347efe1a2f64482cae74b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93040246"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Maximale grootte van de Web Application firewall-aanvraag en uitsluitings lijsten

De Azure-toepassing gateway Web Application firewall (WAF) biedt beveiliging voor webtoepassingen. In dit artikel worden de configuratie van de WAF-aanvraag grootte en de uitsluitings lijsten beschreven. Deze instellingen bevinden zich in het WAF-beleid dat aan uw Application Gateway is gekoppeld. Voor meer informatie over WAF-beleid raadpleegt u [Azure Web Application firewall op Azure-toepassing gateway](ag-overview.md) en [maakt u Web Application firewall-beleid voor Application Gateway](create-waf-policy-ag.md)

## <a name="waf-exclusion-lists"></a>WAF uitsluitings lijsten

![Limieten voor aanvraag grootte](../media/application-gateway-waf-configuration/waf-policy.png)

Met WAF-uitsluitings lijsten kunt u bepaalde kenmerken van aanvragen van een WAF-evaluatie weglaten. Een gemeen schappelijk voor beeld is Active Directory ingevoegde tokens die worden gebruikt voor verificatie-of wachtwoord velden. Dergelijke kenmerken zijn gevoelig voor speciale tekens die een onjuiste positieve waarde van de WAF-regels kunnen veroorzaken. Zodra een kenmerk is toegevoegd aan de uitsluitings lijst WAF, wordt dit niet door de geconfigureerde en actieve WAF-regel beschouwd. Uitsluitings lijsten zijn globaal binnen het bereik.

De volgende kenmerken kunnen worden toegevoegd aan de uitsluitings lijsten op naam. De waarden van het gekozen veld worden niet geëvalueerd op basis van WAF-regels, maar hun namen zijn nog steeds (Zie voor beeld 1 hieronder wordt de waarde van de User-Agent header uitgesloten van WAF-evaluatie). De uitsluitings lijsten verwijderen de inspectie van de veld waarde.

* Aanvraag headers
* Cookies aanvragen
* De naam van het aanvraag kenmerk (args) kan worden toegevoegd als een uitsluitings element, zoals:

   * Naam van het formulier veld
   * JSON-entiteit
   * Query teken reeks argumenten voor URL

U kunt een exacte overeenkomst voor de aanvraag header, hoofd tekst, cookie of query teken reeks kenmerken opgeven.  Of u kunt eventueel gedeeltelijke overeenkomsten opgeven. Uitsluitings regels zijn globaal in bereik en gelden voor alle pagina's en alle regels.

De volgende Opera tors worden ondersteund:

- **Is gelijk aan**: deze operator wordt gebruikt voor een exacte overeenkomst. Als voor beeld voor het selecteren van een header met de naam **bearerToken**, gebruikt u de operator equals met de selector ingesteld als **bearerToken**.
- **Begint met**: deze operator komt overeen met alle velden die beginnen met de opgegeven selector-waarde.
- **Eindigt op**: deze operator komt overeen met alle aanvraag velden die eindigen op de opgegeven selector-waarde.
- **Contains**: deze operator komt overeen met alle aanvraag velden die de opgegeven selector-waarde bevatten.
- **Is gelijk aan**: deze operator komt overeen met alle aanvraag velden. * de waarde selector is.

In alle gevallen die overeenkomen, is hoofdletter gevoelig en reguliere expressies niet toegestaan als selecters.

> [!NOTE]
> Zie [WAF Troubleshooting](web-application-firewall-troubleshoot.md)(Engelstalig) voor meer informatie en hulp bij het oplossen van problemen.

### <a name="examples"></a>Voorbeelden

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In de volgende voor beelden ziet u hoe u uitsluitingen kunt gebruiken.

#### <a name="example-1"></a>Voorbeeld 1

In dit voor beeld wilt u de header van de User-agent uitsluiten. De header van de aanvraag van de gebruikers agent bevat een karakteristieke teken reeks waarmee het netwerk protocol peers het toepassings type, het besturings systeem, de software leverancier of de software versie van de aanvragende software-gebruikers agent kunnen identificeren. Zie [User-agent](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)(Engelstalig) voor meer informatie.

Er kunnen verschillende redenen zijn om de evaluatie van deze koptekst uit te scha kelen. Er kan een teken reeks zijn die de WAF ziet en ervan uitgaat dat deze schadelijk is. Bijvoorbeeld, de klassieke SQL-aanval x = x in een teken reeks. In sommige gevallen kan dit een betrouwbaar verkeer zijn. Daarom moet u deze koptekst mogelijk uitsluiten van de evaluatie van WAF.

Met de volgende Azure PowerShell cmdlet wordt de header van de agent van de gebruiker uitgesloten van de evaluatie:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Voorbeeld 2

In dit voor beeld wordt de waarde in de *gebruikers* parameter die in de aanvraag is door gegeven via de URL, uitgesloten. Stel dat het gebruikers veld in uw omgeving gebruikelijk is om een teken reeks te bevatten die de WAF weergeeft als schadelijke inhoud, zodat deze wordt geblokkeerd.  U kunt de para meter gebruiker in dit geval uitsluiten, zodat de WAF niets in het veld kan evalueren.

Met de volgende Azure PowerShell cmdlet wordt de para meter van de gebruiker uitgesloten van de evaluatie:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Dus als de URL `http://www.contoso.com/?user%281%29=fdafdasfda` wordt door gegeven aan de WAF, wordt de teken reeks **fdafdasfda** niet geëvalueerd, maar wordt nog wel de parameter naam **gebruiker %281 %29** geëvalueerd. 

## <a name="waf-request-size-limits"></a>Limieten voor WAF-aanvraag grootte



Met Web Application firewall kunt u limieten voor aanvraag grootte configureren binnen lagere en bovengrens. De volgende twee limieten voor de grootte zijn beschikbaar:

- Het veld maximale grootte van aanvraag hoofdtekst wordt opgegeven in kilo bytes en bepaalt de totale limiet voor aanvraag grootte, exclusief het uploaden van bestanden. Dit veld heeft een minimum waarde van 1 KB en een maximum waarde van 128 KB. De standaard waarde voor de grootte van de aanvraag hoofdtekst is 128 KB.
- Het veld upload limiet is opgegeven in MB en de Maxi maal toegestane grootte voor het uploaden van bestanden wordt bepaald. Dit veld kan een minimum waarde van 1 MB en de volgende maximum waarden hebben:

   - 100 MB voor v1-media met WAF-gateways
   - 500 MB voor v1 grote WAF-gateways
   - 750 MB voor v2 WAF-gateways 

 De standaard waarde voor de upload limiet voor bestanden is 100 MB.

WAF biedt ook een Configureer bare knop om de controle van de hoofd tekst van de aanvraag in of uit te scha kelen. De controle van de hoofd tekst van de aanvraag is standaard ingeschakeld. Als de controle van de hoofd tekst van de aanvraag is uitgeschakeld, wordt de inhoud van de HTTP-bericht tekst niet geëvalueerd door WAF. In dergelijke gevallen blijft WAF WAF regels afdwingen voor headers, cookies en URI. Als de controle van de hoofd tekst van de aanvraag is uitgeschakeld, is de maximum grootte van het aanvraag hoofdtekst veld niet van toepassing en kan het niet worden ingesteld. Als u de controle van de hoofd tekst van de aanvraag uitschakelt, kunnen berichten die groter zijn dan 128 KB worden verzonden naar WAF, maar de bericht tekst niet wordt gecontroleerd op beveiligings problemen.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw WAF-instellingen hebt geconfigureerd, kunt u leren hoe u uw WAF-Logboeken weergeeft. Zie [Application Gateway Diagnostics (diagnostische](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)gegevens) voor meer informatie.

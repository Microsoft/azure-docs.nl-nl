---
title: Automatisch schalen en zone-redundantie in Application Gateway v2
description: In dit artikel wordt de Azure-toepassing Standard_v2 en WAF_v2 SKU geïntroduceerd, waaronder automatisch schalen en zones redundante functies.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: fad6e27c4ee7e8c10237cb3face5cfab9329b2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059718"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatisch schalen en zone-redundantie in Application Gateway v2 

Application Gateway is beschikbaar in een Standard_v2 SKU. Web Application firewall (WAF) is beschikbaar in een WAF_v2 SKU. De v2-SKU biedt prestatie verbeteringen en voegt ondersteuning toe voor essentiële nieuwe functies, zoals automatisch schalen, zone redundantie en ondersteuning voor statische Vip's. Bestaande functies onder de Standard-en WAF SKU worden nog steeds ondersteund in de nieuwe v2-SKU, met een paar uitzonde ringen die worden vermeld in het gedeelte [vergelijking](#differences-from-v1-sku) .

De nieuwe v2-SKU bevat de volgende verbeteringen:

- Automatisch **schalen**: Application Gateway-of WAF-implementaties in de SKU voor automatische schaling kunnen worden uitgeschaald of op basis van het wijzigen van de verkeers laad patronen. Automatisch schalen heft ook de vereiste op om tijdens het inrichten een implementatiegrootte of het aantal instanties te kiezen. Deze SKU biedt echte elasticiteit. In de Standard_v2 en WAF_v2 SKU kunnen Application Gateway zowel in vaste capaciteit (automatisch schalen uitgeschakeld) als in de modus voor automatisch schalen worden uitgevoerd. De modus vaste capaciteit is handig voor scenario's met consistente en voorspel bare werk belastingen. De modus voor automatisch schalen is handig voor toepassingen die verschillen in toepassings verkeer bekijken.
- **Zone redundantie**: een Application Gateway-of WAF-implementatie kan meerdere Beschikbaarheidszones omvatten, waardoor de nood zaak om afzonderlijke Application Gateway instanties in elke zone met een Traffic Manager te inrichten, wordt verwijderd. U kunt kiezen uit één zone of meerdere zones waar Application Gateway instanties worden geïmplementeerd, waardoor de zone storing moeilijker wordt. De back-end-groep voor toepassingen kan net zo worden gedistribueerd over beschikbaarheids zones.

  Zone redundantie is alleen beschikbaar wanneer Azure-zones beschikbaar zijn. In andere regio's worden alle andere functies ondersteund. Zie [regio's en Beschikbaarheidszones in azure](../availability-zones/az-overview.md) voor meer informatie.
- **Statische VIP**: Application Gateway v2-SKU ondersteunt alleen het statische VIP-type. Dit zorgt ervoor dat het VIP dat is gekoppeld aan de toepassings gateway niet wordt gewijzigd voor de levens cyclus van de implementatie, zelfs na het opnieuw opstarten.  Er is geen statische VIP in v1. Daarom moet u de URL van de toepassings gateway gebruiken in plaats van het IP-adres voor de route ring van domein namen naar App Services via de Application Gateway.
- **Header herschrijven**: met Application Gateway kunt u HTTP-aanvraag-en reactie headers toevoegen, verwijderen of bijwerken met v2-SKU. Zie [HTTP-headers herschrijven met Application Gateway](rewrite-http-headers.md) voor meer informatie
- **Key Vault-integratie**: Application Gateway v2 ondersteunt de integratie met Key Vault voor server certificaten die zijn gekoppeld aan LISTENERS waarvoor https is ingeschakeld. Zie [TLS-beëindiging met Key Vault-certificaten](key-vault-certs.md) voor meer informatie.
- **Azure Kubernetes service ingangs controller**: Application Gateway de Azure-toepassing gateway kan worden gebruikt als binnenkomend verkeer voor een Azure Kubernetes service (AKS), ook wel AKS-cluster genoemd. Zie [Wat is Application Gateway ingress-controller?](ingress-controller-overview.md)voor meer informatie.
- **Prestatie verbeteringen**: de v2-SKU biedt tot 5X betere TLS-offload-prestaties, vergeleken met de Standard-WAF SKU.
- **Snellere implementatie en update tijd** De v2-SKU biedt een snellere implementatie en tijd voor het bijwerken in vergelijking met Standard/WAF SKU. Dit omvat ook wijzigingen in de configuratie van WAF.

![Diagram van de zone voor automatisch schalen.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Ondersteunde regio’s

De Standard_v2-en WAF_v2 SKU is beschikbaar in de volgende regio's: Noord-Centraal VS, Zuid-Centraal VS, VS-West, VS-West 2, VS-Oost, VS-Oost 2, centraal VS, Europa-noord, Europa-west, Zuidoost-Azië, Frankrijk-centraal, UK-west, Japan-Oost, Japan-West, Australië-Oost, Australië-Zuidoost, Brazilië-zuid, Canada-centraal, Canada-oost, Azië-oost, Korea-centraal, Korea-Zuid , UK-zuid, Centraal-India, West-India, India-zuid.

## <a name="pricing"></a>Prijzen

Met de v2-SKU wordt het prijs model aangestuurd door verbruik en niet meer gekoppeld aan het aantal exemplaren of de grootte van het exemplaar. De v2-SKU-prijzen hebben twee onderdelen:

- **Vaste prijs** : dit is elk uur (of een gedeeltelijke uur) prijs om een Standard_v2 of WAF_v2 gateway in te richten. 0 extra minimum aantal exemplaren garandeert nog steeds een hoge Beschik baarheid van de service, die altijd is opgenomen in een vaste prijs.
- **Eenheids prijs van capaciteit** : dit is een kosten op basis van verbruik die naast de vaste kosten worden berekend. De kosten voor de capaciteits eenheid worden ook elk uur of gedeeltelijk per uur berekend. Er zijn drie dimensies tot capaciteits eenheid: reken eenheid, permanente verbindingen en door voer. Rekeneenheid is een meting van de verbruikte processorcapaciteit. Factoren die de rekeneenheid beïnvloeden, zijn: TLS-verbindingen per seconde, berekeningen voor het herschrijven van URL's en de verwerking van WAF-regels. Permanente verbinding is een meting van de tot stand gebrachte TCP-verbindingen met de toepassings gateway in een bepaald facturerings interval. De door Voer is het gemiddelde aantal megabits per seconde dat door het systeem wordt verwerkt in een opgegeven facturerings interval.  De facturering vindt plaats op het niveau van de capaciteits eenheid voor alle boven het gereserveerde exemplaar aantal.

Elke capaciteits eenheid bestaat uit Maxi maal: 1 reken eenheid, 2500 permanente verbindingen en door Voer van 2,22-Mbps.

Zie voor meer informatie [over prijzen](understanding-pricing.md).

## <a name="scaling-application-gateway-and-waf-v2"></a>Application Gateway en WAF v2 schalen

Application Gateway en WAF kunnen worden geconfigureerd om in twee modi te schalen:

- Automatisch **schalen** : als automatische schaling is ingeschakeld, worden de Application Gateway-en WAF v2-sku's omhoog of omlaag geschaald op basis van de vereisten voor toepassings verkeer. Deze modus biedt een betere elasticiteit voor uw toepassing en elimineert de nood zaak om de grootte van de toepassings gateway of het aantal instanties te raden. Met deze modus kunt u ook kosten besparen door te voor komen dat de gateway op piek ingerichte capaciteit wordt uitgevoerd voor de verwachte maximale belasting van het verkeer. U moet een minimum en optioneel maximum aantal exemplaren opgeven. De minimale capaciteit zorgt ervoor dat Application Gateway en WAF v2 niet onder het opgegeven minimum aantal instanties vallen, zelfs als er geen verkeer is. Elk exemplaar is ongeveer gelijk aan 10 extra gereserveerde capaciteits eenheden. Nul geeft geen gereserveerde capaciteit aan en is uitsluitend automatisch geschaald. U kunt eventueel ook een maximum aantal exemplaren opgeven, waardoor het Application Gateway niet groter wordt dan het opgegeven aantal exemplaren. Er worden alleen kosten in rekening gebracht voor de hoeveelheid verkeer die door de gateway wordt geleverd. Het aantal exemplaren kan variëren van 0 tot en met 125. De standaard waarde voor het maximum aantal exemplaren is 20 als deze niet is opgegeven.
- **Hand matig** : u kunt ook hand matige modus kiezen waarbij de gateway niet automatisch wordt geschaald. Als er in deze modus meer verkeer is dan wat Application Gateway of WAF kan verwerken, kan dit leiden tot verlies van verkeer. Bij hand matige modus wordt het aantal exemplaren opgegeven dat verplicht is. Aantal exemplaren kan variëren van 1 tot 125 exemplaren.

## <a name="autoscaling-and-high-availability"></a>Automatisch schalen en hoge Beschik baarheid

Azure-toepassing gateways worden altijd op een Maxi maal beschik bare manier geïmplementeerd. De service is gemaakt met meerdere exemplaren die zijn gemaakt als geconfigureerd (als automatisch schalen is uitgeschakeld) of die vereist zijn voor het laden van de toepassing (als automatisch schalen is ingeschakeld). Houd er rekening mee dat u in het oogpunt van de gebruiker niet per se de afzonderlijke instanties kunt zien, maar alleen in de Application Gateway Service als geheel. Als er een probleem is met een bepaald exemplaar en er wordt gestaakt, wordt door Azure-toepassing gateway transparant een nieuw exemplaar gemaakt.

Houd er rekening mee dat zelfs als u automatisch schalen configureert met een minimum aantal instanties, de service nog steeds Maxi maal beschikbaar is, wat altijd is opgenomen in de vaste prijs.

Het maken van een nieuw exemplaar kan echter ongeveer zes of zeven minuten duren. Als u geen gebruik wilt maken van deze downtime, kunt u dus een minimum aantal exemplaren van 2 configureren, in het ideale geval met ondersteuning voor de beschikbaarheids zone. Op deze manier hebt u onder normale omstandigheden ten minste twee exemplaren in uw Azure-toepassing-gateway, dus als er een probleem is met de andere, wordt er tijdens het moment dat er een nieuwe instantie wordt gemaakt, met het verkeer gecommuniceerd. Houd er rekening mee dat een instantie van de Azure-toepassing-gateway ongeveer 10 capaciteits eenheden kan ondersteunen, dus afhankelijk van hoeveel verkeer u normaal gesp roken wilt, kunt u de instelling voor de minimale automatische schaal aanpassing configureren op een waarde hoger dan 2.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Vergelijking van functies tussen v1 SKU en v2 SKU

De volgende tabel vergelijkt de functies die beschikbaar zijn voor elke SKU.

| Functie                                           | v1-SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatisch schalen                                       |          | &#x2713; |
| Zoneredundantie                                   |          | &#x2713; |
| Statisch VIP                                        |          | &#x2713; |
| Azure Kubernetes service (AKS) ingangs controller |          | &#x2713; |
| Integratie van Azure Sleutelkluis                       |          | &#x2713; |
| HTTP (S)-headers opnieuw schrijven                           |          | &#x2713; |
| URL-gebaseerde routering                                 | &#x2713; | &#x2713; |
| Hosting van meerdere sites                             | &#x2713; | &#x2713; |
| Omleiding van verkeer                               | &#x2713; | &#x2713; |
| Web Application Firewall (WAF)                    | &#x2713; | &#x2713; |
| Aangepaste WAF-regels                                  |          | &#x2713; |
| Beëindiging van Transport Layer Security (TLS)/Secure Sockets Layer (SSL)            | &#x2713; | &#x2713; |
| End-to-end TLS-versleuteling                         | &#x2713; | &#x2713; |
| Sessieaffiniteit                                  | &#x2713; | &#x2713; |
| Aangepaste foutpagina's                                | &#x2713; | &#x2713; |
| Ondersteuning voor WebSocket                                 | &#x2713; | &#x2713; |
| Ondersteuning voor HTTP/2                                    | &#x2713; | &#x2713; |
| Verwerkingsstop voor verbindingen                               | &#x2713; | &#x2713; |

> [!NOTE]
> De v2 SKU voor automatisch schalen ondersteunt nu [standaard status tests](application-gateway-probe-overview.md#default-health-probe) om de status van alle resources in de back-end-pool te controleren en de back-end-leden te markeren die als slecht worden beschouwd. De standaard status test wordt automatisch geconfigureerd voor back-ends die geen aangepaste test configuratie hebben. Zie [Health tests in Application Gateway](application-gateway-probe-overview.md)voor meer informatie.

## <a name="differences-from-v1-sku"></a>Verschillen van v1 SKU

In deze sectie worden de functies en beperkingen beschreven van de v2-SKU die verschilt van de V1-SKU.

|Verschil|Details|
|--|--|
|Verificatie certificaat|Wordt niet ondersteund.<br>Zie [overzicht van end-to-end-TLS met Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku)voor meer informatie.|
|Standard_v2 en standaard Application Gateway op hetzelfde subnet mengen|Niet ondersteund|
|User-Defined route (UDR) op Application Gateway subnet|Ondersteund (specifieke scenario's). In de preview-versie.<br> Zie [Application Gateway configuratie-overzicht](configuration-infrastructure.md#supported-user-defined-routes)voor meer informatie over ondersteunde scenario's.|
|NSG voor binnenkomend poort bereik| -65200 tot 65535 voor Standard_v2 SKU<br>-65503 tot 65534 voor standaard-SKU.<br>Raadpleeg de [Veelgestelde vragen](application-gateway-faq.yml#are-network-security-groups-supported-on-the-application-gateway-subnet)voor meer informatie.|
|Prestatie Logboeken in azure Diagnostics|Wordt niet ondersteund.<br>De metrische gegevens van Azure moeten worden gebruikt.|
|Billing|De facturering is gepland om te beginnen op 1 juli 2019.|
|FIPS-modus|Deze worden momenteel niet ondersteund.|
|Modus alleen ILB|Dit wordt momenteel niet ondersteund. De open bare en ILB modus samen worden ondersteund.|
|Netwerk-Watcher-integratie|Wordt niet ondersteund.|
|Azure Security Center-integratie|Nog niet beschikbaar.

## <a name="migrate-from-v1-to-v2"></a>Migreren van v1 naar v2

Een Azure PowerShell script is beschikbaar in de Power shell Gallery, waarmee u kunt migreren van uw v1-Application Gateway/WAF naar de v2-SKU voor automatisch schalen. Met dit script kunt u de configuratie van uw v1-gateway kopiëren. Verkeer migratie is nog steeds uw verantwoordelijkheid. Zie [Azure-toepassing gateway migreren van v1 naar v2](migrate-v1-v2.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Quickstart: Webverkeer omleiden met Azure Application Gateway - Azure Portal](quick-create-portal.md)
- [Een automatische schaling maken, een zone redundante toepassings gateway met een gereserveerd virtueel IP-adres met behulp van Azure PowerShell](tutorial-autoscale-ps.md)
- Meer informatie over [Application Gateway](overview.md).
- Meer informatie over [Azure Firewall](../firewall/overview.md).

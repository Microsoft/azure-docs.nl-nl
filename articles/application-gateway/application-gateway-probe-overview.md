---
title: Overzicht van status controle voor de Azure-toepassing-gateway
description: Azure-toepassing gateway controleert de status van alle resources in de back-end-pool en verwijdert automatisch alle bronnen die worden beschouwd als een slechte status van de groep.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649056"
---
# <a name="application-gateway-health-monitoring-overview"></a>Overzicht van Application Gateway status controle

Azure-toepassing gateway controleert standaard de status van alle resources in de back-end-groep en verwijdert automatisch alle bronnen die worden beschouwd als een slechte status van de groep. Application Gateway blijft de exemplaren met slechte status bewaken en voegt deze weer aan de goede back-end-adrespool toe zodra ze beschikbaar komen en beantwoorden aan de statuscontroles. Standaard verzendt Application Gateway de status tests met dezelfde poort die is gedefinieerd in de back-end-HTTP-instellingen. Een aangepaste test poort kan worden geconfigureerd met behulp van een aangepaste status test.

Het bron-IP-adres Application Gateway gebruikt voor status tests is afhankelijk van de back-end-groep:
 
- Als het server adres in de back-endadresgroep een openbaar eind punt is, is het bron adres het open bare frontend-front-end-IP-adres van de toepassings gateway.
- Als het adres van de server in de back-endadresgroep een persoonlijk eind punt is, is het bron-IP-adres van de privé IP-adres ruimte van het subnet van de toepassings gateway.

![test voorbeeld van Application Gateway][1]

Naast het gebruik van de standaard controle van de status test, kunt u ook de status test aanpassen aan de vereisten van uw toepassing. In dit artikel worden zowel standaard als aangepaste status controles behandeld.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Standaard status test

Een toepassings gateway configureert automatisch een standaard status test wanneer u geen aangepaste test configuratie instelt. Het bewakings gedrag werkt door een HTTP GET-aanvraag naar de IP-adressen of FQDN te maken die is geconfigureerd in de back-end-pool. Voor standaard tests als de back-end-http-instellingen voor HTTPS zijn geconfigureerd, gebruikt de test HTTPS om de status van de back-endservers te testen.

Bijvoorbeeld: u configureert uw toepassings gateway voor het gebruik van back-endservers A, B en C om HTTP-netwerk verkeer te ontvangen op poort 80. De standaard status bewaking test de drie servers om de 30 seconden voor een gezonde HTTP-reactie met een time-out van 30 seconden voor elke aanvraag. Een gezond HTTP-antwoord heeft een [status code](https://msdn.microsoft.com/library/aa287675.aspx) tussen 200 en 399. In dit geval ziet de HTTP GET-aanvraag voor de status test er als volgt uit http://127.0.0.1/ .

Als de standaard controle van de test voor Server A mislukt, stopt de toepassings gateway met het door sturen van aanvragen naar deze server. De standaard test blijft altijd elke 30 seconden controleren op server. Als server A reageert op een aanvraag van een standaard status test, wordt de aanvraag door Application Gateway opnieuw door sturen naar de server.

### <a name="default-health-probe-settings"></a>Standaard instellingen voor status controle

| Probe-eigenschap | Waarde | Beschrijving |
| --- | --- | --- |
| Test-URL |\<protocol\>://127.0.0.1:\<port\>/ |Het protocol en de poort worden overgenomen van de back-end-HTTP-instellingen waaraan de test is gekoppeld |
| Interval |30 |De hoeveelheid tijd in seconden die moet worden gewacht voordat de volgende status test wordt verzonden.|
| Time-out |30 |De hoeveelheid tijd in seconden die de toepassings gateway wacht op een test reactie voordat de test wordt gemarkeerd als beschadigd. Als een test wordt geretourneerd als in orde, wordt de bijbehorende back-end direct gemarkeerd als in orde.|
| Drempelwaarde voor beschadigde status |3 |Bepaalt hoeveel tests er moeten worden verzonden als er een fout is opgetreden van de normale status test. In v1 SKU worden deze aanvullende Health-tests snel achter elkaar verzonden om de status van de back-end snel te bepalen en te wachten op het test interval. In het geval van v2 SKU wordt het interval gewacht op de status. De back-endserver is gemarkeerd wanneer het aantal opeenvolgende test fouten de drempel waarde voor de onjuiste status bereikt. |

De standaard test zoekt alleen op \<protocol\> ://127.0.0.1: \<port\> om de status te bepalen. Als u de status test moet configureren om naar een aangepaste URL te gaan of andere instellingen te wijzigen, moet u aangepaste tests gebruiken. Zie [overzicht van TLS-beëindiging en end-to-end-TLS met Application Gateway](ssl-overview.md#for-probe-traffic)voor meer informatie over HTTPS-tests.

### <a name="probe-intervals"></a>Sonde-intervallen

Alle exemplaren van Application Gateway testen de back-end onafhankelijk van elkaar. Dezelfde test configuratie is van toepassing op elk Application Gateway-exemplaar. Als de test configuratie bijvoorbeeld elke 30 seconden status controles verzendt en de toepassings gateway twee exemplaren heeft, worden beide instanties elke 30 seconden de status test verzonden.

Als er meerdere listeners zijn, controleert elke listener de back-end onafhankelijk van elkaar. Als er bijvoorbeeld twee listeners zijn die verwijzen naar dezelfde back-end-groep op twee verschillende poorten (geconfigureerd met twee back-end-http-instellingen), wordt de dezelfde back-end onafhankelijk door elke listener gecontroleerd. In dit geval zijn er twee tests van elk toepassings gateway-exemplaar voor de twee listeners. Als er in dit scenario twee exemplaren van de toepassings gateway zijn, ziet u op de virtuele machine van de back-end vier tests volgens het geconfigureerde test interval.

## <a name="custom-health-probe"></a>Aangepaste status test

Met aangepaste tests kunt u de controle over de status controle nauw keuriger maken. Wanneer u aangepaste tests gebruikt, kunt u een aangepaste hostnaam, een URL-pad, een test interval en het aantal mislukte antwoorden accepteren voordat het exemplaar van de back-end-pool wordt gemarkeerd als beschadigd, enzovoort.

### <a name="custom-health-probe-settings"></a>Instellingen voor aangepaste Health probe

De volgende tabel bevat definities voor de eigenschappen van een aangepaste status test.

| Probe-eigenschap | Beschrijving |
| --- | --- |
| Naam |De naam van de test. Deze naam wordt gebruikt om de test in back-end-HTTP-instellingen te identificeren en te raadplegen. |
| Protocol |Het protocol dat wordt gebruikt om de test te verzenden. Dit moet overeenkomen met het protocol dat is gedefinieerd in de back-end HTTP-instellingen waaraan het is gekoppeld|
| Host |De hostnaam waarmee de test wordt verzonden. In v1 SKU wordt deze waarde alleen gebruikt voor de host-header van de test aanvraag. In v2 SKU wordt het gebruikt als host-header, evenals SNI |
| Pad |Het relatieve pad van de test. Er begint een geldig pad met '/' |
| Poort |Indien gedefinieerd, wordt dit gebruikt als de doel poort. Anders wordt dezelfde poort gebruikt als de HTTP-instellingen waaraan deze is gekoppeld. Deze eigenschap is alleen beschikbaar in de v2-SKU
| Interval |Test interval in seconden. Deze waarde is het tijds interval tussen twee opeenvolgende tests |
| Time-out |Time-out van de test (in seconden). Als er binnen deze time-outperiode geen geldig antwoord wordt ontvangen, wordt de test als mislukt gemarkeerd  |
| Drempelwaarde voor beschadigde status |Aantal nieuwe pogingen testen. De back-endserver is gemarkeerd wanneer het aantal opeenvolgende test fouten de drempel waarde voor onjuiste status bereikt |

### <a name="probe-matching"></a>Testen vergelijken

Standaard wordt een HTTP (S)-antwoord met de status code tussen 200 en 399 als gezond beschouwd. Aangepaste Health-tests ondersteunen daarnaast twee overeenkomende criteria. Overeenkomende criteria kunnen worden gebruikt voor het aanpassen van de standaard interpretatie van wat een gezonde reactie doet.

Hier volgen de overeenkomende criteria: 

- **Status code overeenkomst van http-antwoord** : test matching criterium voor het accepteren van door de gebruiker opgegeven HTTP-antwoord code of het bereik van antwoord codes. Afzonderlijke door komma's gescheiden antwoord status codes of een bereik met status code wordt ondersteund.
- **Overeenkomst voor de hoofd tekst van het HTTP-antwoord die overeenkomt** met het criterium van de HTTP-reactie en overeenkomt met een door de gebruiker opgegeven teken reeks. De overeenkomst zoekt alleen naar de aanwezigheid van de door de gebruiker opgegeven teken reeks in de tekst van het antwoord en is geen volledige reguliere expressie.

Er kunnen match criteria worden opgegeven met behulp van de `New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet.

Bijvoorbeeld:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Zodra de match criteria zijn opgegeven, kan deze worden gekoppeld aan de test configuratie met behulp `-Match` van een para meter in Power shell.

## <a name="nsg-considerations"></a>NSG overwegingen

U moet binnenkomend Internet verkeer toestaan op TCP-poorten 65503-65534 voor de Application Gateway v1-SKU en TCP-poorten 65200-65535 voor de v2-SKU met het doel-subnet als **enige** en bron als **GatewayManager** -service label. Dit poort bereik is vereist voor de communicatie van Azure-infra structuur.

Daarnaast kan uitgaande internet connectiviteit niet worden geblokkeerd en moet binnenkomend verkeer dat afkomstig is van het label **AzureLoadBalancer** , worden toegestaan.

Zie [Overzicht van Application Gateway-configuratie](configuration-infrastructure.md#network-security-groups) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Nadat u hebt gebruikgemaakt van Application Gateway status controle, kunt u een [aangepaste Health probe](application-gateway-create-probe-portal.md) in de Azure portal of een [aangepaste status test](application-gateway-create-probe-ps.md) configureren met behulp van power shell en het Azure Resource Manager-implementatie model.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png

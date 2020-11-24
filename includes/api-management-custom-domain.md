---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 1858317d40efa59b188ce894534be93a1f11b287
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95557138"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Hoe APIM Proxy Server reageert met SSL-certificaten in de TLS-handshake

### <a name="clients-calling-with-sni-header"></a>Clientoproepen met SNI-header
Als de klant een of meer aangepaste domeinen heeft geconfigureerd voor Proxy, kan APIM reageren op HTTP-aanvragen van de aangepaste domeinen (bijvoorbeeld contoso.com) en van het standaarddomein (bijvoorbeeld apim-service-name.azure-api.net). Op basis van de informatie in de SNI-header (servernaamindicatie) reageert APIM met het juiste servercertificaat.

### <a name="clients-calling-without-sni-header"></a>Clientoproepen zonder SNI-header
Als de klant een client gebruikt, waardoor de [SNI](https://tools.ietf.org/html/rfc6066#section-3)-header niet wordt verzonden, maakt APIM antwoorden op basis van de volgende logica:

* Als met de service slechts één aangepast domein is geconfigureerd voor Proxy, is het standaardcertificaat het certificaat dat is verleend aan het aangepaste Proxy-domein.
* Als met de service meerdere aangepaste domeinen zijn geconfigureerd voor Proxy (ondersteund in de lagen **Ontwikkelaar** en **Proxy**), kan de klant bepalen welk certificaat het standaardcertificaat is. Als u het standaardcertificaat wilt instellen, moet de eigenschap [defaultSslBinding](/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) zijn ingesteld op Waar ("defaultSslBinding":"true"). Als de klant de eigenschap niet instelt, is het standaardcertificaat het certificaat dat is verleend aan het standaard-Proxy-domein dat wordt gehost op *.azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Ondersteuning voor PUT/POST-aanvragen met grote nettolading

De APIM Proxy-server biedt ondersteuning voor grote nettoladingen, bij het gebruik van certificaten aan de clientzijde in HTTPS (bijvoorbeeld nettolading > 40 kB). Als u wilt voorkomen dat de aanvraag van de server blijft hangen, kunnen klanten de eigenschap ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) instellen op de Proxy-hostnaam. Als de eigenschap is ingesteld op Waar, wordt het clientcertificaat aangevraagd op de tijd van de SSL/TLS-verbinding, vóór HTTP-aanvragen worden uitgewisseld. Omdat de instelling van toepassing is op het niveau **Proxy-hostnaam**, vragen alle verbindingsaanvragen om het clientcertificaat. Klanten kunnen maximaal 20 aangepaste domeinen voor Proxy configureren (alleen ondersteund in de laag **Premium**) en omzeilen deze beperking.
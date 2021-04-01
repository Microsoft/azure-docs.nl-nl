---
title: Azure DNS integreren met uw Azure-resources-Azure DNS
description: In dit artikel vindt u informatie over het gebruik van Azure DNS in combi natie met DNS voor uw Azure-resources.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 4d8af5815e544698ab833001e5ce6d0f4a30a264
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487395"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Azure DNS gebruiken om aangepaste domein instellingen op te geven voor een Azure-service

Azure DNS biedt DNS voor een aangepast domein voor uw Azure-resources die aangepaste domeinen ondersteunen of die een Fully Qualified Domain Name (FQDN) hebben. Een voor beeld is een Azure-web-app en u wilt dat uw gebruikers er toegang toe hebben door gebruik te maken van contoso.com of www \. contoso.com als FQDN. Dit artikel helpt u bij het configureren van uw Azure-service met Azure DNS voor het gebruik van aangepaste domeinen.

## <a name="prerequisites"></a>Vereisten

Als u Azure DNS voor uw aangepaste domein wilt gebruiken, moet u uw domein eerst delegeren naar Azure DNS. Ga naar [een domein delegeren naar Azure DNS](./dns-delegate-domain-azure-dns.md) voor instructies over het configureren van uw naam servers voor delegering. Zodra uw domein is overgedragen aan uw Azure DNS zone, kunt u de benodigde DNS-records configureren.

U kunt een Vanity of aangepast domein configureren voor [Azure function-apps](#azure-function-app), [open bare IP-adressen](#public-ip-address), [app service (Web apps)](#app-service-web-apps), [Blob Storage](#blob-storage)en [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Azure Function-app

Als u een aangepast domein wilt configureren voor Azure function-apps, wordt er een CNAME-record gemaakt, evenals de configuratie van de functie-app zelf.
 
Navigeer naar **functie-app** en selecteer uw functie-app. Klik op **platform functies** en klik onder **netwerken** op **aangepaste domeinen**.

![Blade functie-app](./media/dns-custom-domain/functionapp.png)

Let op de huidige URL op de Blade **aangepaste domeinen** . dit adres wordt gebruikt als de alias voor de DNS-record die is gemaakt.

![Blade aangepaste domein](./media/dns-custom-domain/functionshostname.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende gegevens in op de Blade **recordset toevoegen** en klik op **OK** om deze te maken.

|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Name     | myfunctionapp        | Deze waarde samen met het domein naam label is de FQDN voor de aangepaste domein naam.        |
|Type     | CNAME        | Voor het gebruik van een CNAME-record wordt een alias gebruikt.        |
|TTL     | 1        | 1 wordt voor 1 uur gebruikt        |
|TTL-eenheid     | Tijden        | Uren worden gebruikt als meet tijd         |
|Alias     | adatumfunction.azurewebsites.net        | De DNS-naam waarvoor u de alias maakt, in dit voor beeld is de DNS-naam adatumfunction.azurewebsites.net die standaard wordt verschaft voor de functie-app.        |

Ga terug naar de functie-app, klik op **platform functies** en klik onder **netwerken** op **aangepaste domeinen** en klik onder **aangepaste hostnamen** op **+ hostnaam toevoegen**.

Voer op de Blade **hostname toevoegen** de CNAME-record in het tekst veld **hostnaam** in en klik op **valideren**. Als de record wordt gevonden, wordt de knop **hostnaam toevoegen** weer gegeven. Klik op **hostnaam toevoegen** om de alias toe te voegen.

![functie-apps-Blade hostnaam toevoegen](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Openbaar IP-adres

Als u een aangepast domein wilt configureren voor services die gebruikmaken van een open bare IP-adres resource, zoals Application Gateway, Load Balancer, Cloud service, Resource Manager-Vm's en, klassieke Vm's, wordt een record gebruikt.

Navigeer naar   >  het **open bare IP-adres** van netwerken, selecteer de open bare IP-resource en klik op **configuratie**. Het IP-adres dat wordt weer gegeven.

![open bare IP-Blade](./media/dns-custom-domain/publicip.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende gegevens in op de Blade **recordset toevoegen** en klik op **OK** om deze te maken.


|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Name     | mywebserver        | Deze waarde samen met het domein naam label is de FQDN voor de aangepaste domein naam.        |
|Type     | A        | Gebruik een A-record als de resource een IP-adres is.        |
|TTL     | 1        | 1 wordt voor 1 uur gebruikt        |
|TTL-eenheid     | Tijden        | Uren worden gebruikt als meet tijd         |
|IP-adres     | `<your ip address>`       | Het open bare IP-adres.|

![een A-record maken](./media/dns-custom-domain/arecord.png)

Zodra de A-record is gemaakt, voert `nslookup` u uit om de record te valideren.

![open bare IP-DNS zoeken](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Voer de volgende stappen uit om een aangepast domein te configureren voor een app service-Web-app.

Navigeer naar **app service** en selecteer de resource die u wilt configureren van een aangepaste domein naam en klik op **aangepaste domeinen**.

Let op de huidige URL op de Blade **aangepaste domeinen** . dit adres wordt gebruikt als de alias voor de DNS-record die is gemaakt.

![Blade aangepaste domeinen](./media/dns-custom-domain/url.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende gegevens in op de Blade **recordset toevoegen** en klik op **OK** om deze te maken.


|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Name     | mywebserver        | Deze waarde samen met het domein naam label is de FQDN voor de aangepaste domein naam.        |
|Type     | CNAME        | Voor het gebruik van een CNAME-record wordt een alias gebruikt. Als de resource een IP-adres heeft gebruikt, wordt er een record gebruikt.        |
|TTL     | 1        | 1 wordt voor 1 uur gebruikt        |
|TTL-eenheid     | Tijden        | Uren worden gebruikt als meet tijd         |
|Alias     | webserver.azurewebsites.net        | De DNS-naam waarvoor u de alias maakt, in dit voor beeld is de DNS-naam van de webserver.azurewebsites.net die standaard wordt verschaft voor de web-app.        |


![een CNAME-record maken](./media/dns-custom-domain/createcnamerecord.png)

Ga terug naar de app service die is geconfigureerd voor de aangepaste domein naam. Klik op **aangepaste domeinen** en klik vervolgens op **hostnamen**. Klik op **+ hostnaam toevoegen** om de CNAME-record toe te voegen die u hebt gemaakt.

![Scherm afbeelding met de knop voor het toevoegen van de hostnaam.](./media/dns-custom-domain/figure1.png)

Zodra het proces is voltooid, voert u **nslookup** uit om de naam omzetting te valideren.

![afbeelding 1](./media/dns-custom-domain/finalnslookup.png)

Ga voor meer informatie over het toewijzen van een aangepast domein aan App Service naar [een bestaande aangepaste DNS-naam toewijzen aan Azure web apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Zie [een actieve DNS-naam migreren naar Azure app service](../app-service/manage-custom-dns-migrate-domain.md)voor meer informatie over het migreren van een actieve DNS-naam.

Als u een aangepast domein wilt aanschaffen, kunt u [een aangepaste domein naam voor Azure web apps kopen voor](../app-service/manage-custom-dns-buy-domain.md) meer informatie over app service domeinen.

## <a name="blob-storage"></a>Blob Storage

Voer de volgende stappen uit om een CNAME-record voor een Blob Storage-account te configureren met de methode asverify. Deze methode zorgt ervoor dat er geen uitval tijd is.

Navigeer naar **opslag**  >  **accounts**, selecteer uw opslag account en klik op **aangepast domein**. De FQDN onder stap 2, deze waarde wordt gebruikt om de eerste CNAME-record te maken

![aangepast domein voor Blob Storage](./media/dns-custom-domain/blobcustomdomain.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende gegevens in op de Blade **recordset toevoegen** en klik op **OK** om deze te maken.


|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Name     | asverify. mystorageaccount        | Deze waarde samen met het domein naam label is de FQDN voor de aangepaste domein naam.        |
|Type     | CNAME        | Voor het gebruik van een CNAME-record wordt een alias gebruikt.        |
|TTL     | 1        | 1 wordt voor 1 uur gebruikt        |
|TTL-eenheid     | Tijden        | Uren worden gebruikt als meet tijd         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | De DNS-naam waarvoor u de alias maakt, in dit voor beeld is de DNS-naam van de asverify.adatumfunctiona9ed.blob.core.windows.net die standaard wordt gegeven aan het opslag account.        |

Ga terug naar uw opslag account door te klikken op **opslag**  >  **accounts**, selecteer uw opslag account en klik op **aangepast domein**. Typ de alias die u hebt gemaakt zonder het voor voegsel asverify in het tekstvak, schakel de optie **indirecte CNAME-validatie gebruiken** in en klik op **Opslaan**. Nadat deze stap is voltooid, gaat u terug naar uw DNS-zone en maakt u een CNAME-record zonder het asverify-voor voegsel.  Na dat moment kunt u de CNAME-record met het voor voegsel cdnverify verwijderen.

![Scherm opname van de pagina met het aangepaste domein.](./media/dns-custom-domain/indirectvalidate.png)

DNS-omzetting valideren door uit te voeren `nslookup`

Voor meer informatie over het toewijzen van een aangepast domein aan een Blob Storage-eind punt gaat u naar [een aangepaste domein naam configureren voor het eind punt van de Blob-opslag](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Voer de volgende stappen uit om een CNAME-record voor een CDN-eind punt te configureren met de methode cdnverify. Deze methode zorgt ervoor dat er geen uitval tijd is.

Navigeer naar   >  **CDN-profielen** voor netwerken en selecteer uw CDN-profiel.

Selecteer het eind punt waarmee u werkt en klik op **+ aangepast domein**. Noteer de **hostnaam van het eind punt** , aangezien deze waarde de record is waarnaar de CNAME-record verwijst.

![Aangepast CDN-domein](./media/dns-custom-domain/endpointcustomdomain.png)

Navigeer naar uw DNS-zone en klik op **+ Recordset**. Vul de volgende gegevens in op de Blade **recordset toevoegen** en klik op **OK** om deze te maken.

|Eigenschap  |Waarde  |Beschrijving  |
|---------|---------|---------|
|Name     | cdnverify. mycdnendpoint        | Deze waarde samen met het domein naam label is de FQDN voor de aangepaste domein naam.        |
|Type     | CNAME        | Voor het gebruik van een CNAME-record wordt een alias gebruikt.        |
|TTL     | 1        | 1 wordt voor 1 uur gebruikt        |
|TTL-eenheid     | Tijden        | Uren worden gebruikt als meet tijd         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | De DNS-naam waarvoor u de alias maakt, in dit voor beeld is de DNS-naam van de cdnverify.adatumcdnendpoint.azureedge.net die standaard wordt gegeven aan het opslag account.        |

Ga terug naar uw CDN-eind punt door te klikken op **netwerk**  >  **CDN-profielen** en selecteer uw CDN-profiel. Klik op **+ aangepast domein** en voer uw CNAME-record alias in zonder het cdnverify-voor voegsel en klik op **toevoegen**.

Nadat deze stap is voltooid, gaat u terug naar uw DNS-zone en maakt u een CNAME-record zonder het cdnverify-voor voegsel.  Na dat moment kunt u de CNAME-record met het voor voegsel cdnverify verwijderen. Voor meer informatie over CDN en het configureren van een aangepast domein zonder de tussentijdse registratie stap gaat u naar [Azure CDN inhoud toewijzen aan een aangepast domein](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van omgekeerde DNS voor services die worden gehost in azure](dns-reverse-dns-for-azure-services.md).

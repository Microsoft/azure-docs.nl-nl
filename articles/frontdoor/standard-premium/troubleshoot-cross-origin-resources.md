---
title: Azure front deur Standard/Premium gebruiken met cross-Origin resource delen
description: Meer informatie over het gebruik van de Azure front-deur (AFD) voor het delen van cross-Origin-resources (CORS).
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ee8f19aca62d2e331fcf59551d47c2dac93783b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098838"
---
# <a name="using-azure-front-door-standardpremium-with-cross-origin-resource-sharing-cors"></a>Azure front deur Standard/Premium gebruiken met cross-Origin Resource Sharing (CORS)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

## <a name="what-is-cors"></a>Wat is CORS?

CORS (Cross-Origin Resource Sharing) is een HTTP-functie waarmee een webtoepassing die wordt uitgevoerd onder één domein, toegang kan krijgen tot resources in een ander domein. Om de kans op cross-site scripting aanvallen te verminderen, implementeren alle moderne webbrowsers een beveiligings beperking die bekend staat als [hetzelfde-Origin-beleid](https://www.w3.org/Security/wiki/Same_Origin_Policy). Hiermee wordt voor komen dat een webpagina Api's in een ander domein aanroept.  CORS biedt een veilige manier om één oorsprong (het oorspronkelijke domein) toe te staan om Api's in een andere oorsprong aan te roepen.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="how-it-works"></a>Uitleg

Er zijn twee typen CORS-aanvragen, *eenvoudige aanvragen* en *complexe aanvragen.*

### <a name="for-simple-requests"></a>Voor eenvoudige aanvragen:

1. De browser verzendt de CORS-aanvraag met een andere HTTP-aanvraag header van de **bron** . De waarde van deze header is de oorsprong die de bovenliggende pagina heeft geleverd. deze is gedefinieerd als de combi natie van *protocol,* *domein* en *poort.*  Wanneer een pagina van HTTPS \: //www.contoso.com probeert toegang te krijgen tot de gegevens van een gebruiker in de fabrikam.com-oorsprong, wordt de volgende aanvraag header verzonden naar fabrikam.com:

   `Origin: https://www.contoso.com`

2. De server reageert mogelijk met een van de volgende:

   * Een ' **Access-Control-Allow-Origin** -header in het antwoord dat aangeeft welke oorspronkelijke site is toegestaan. Bijvoorbeeld:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Een HTTP-fout code zoals 403 als de server de cross-Origin-aanvraag niet toestaat na het controleren van de oorspronkelijke header

   * Een ' **Access-Control-Allow-Origin** -header met een Joker teken waarmee alle oorsprongen worden toegestaan:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Voor complexe aanvragen:

Een complexe aanvraag is een CORS-aanvraag waarbij de browser een *Preflight-aanvraag* (dat wil zeggen een oriënterende test) moet verzenden voordat de daad werkelijke CORS-aanvraag wordt verzonden. De Preflight-aanvraag vraagt de server machtiging aan als de oorspronkelijke CORS-aanvraag kan door gaan en een `OPTIONS` aanvraag naar dezelfde URL is.

> [!TIP]
> Voor meer informatie over CORS-stromen en veelvoorkomende Valk uilen, raadpleegt [u de hand leiding voor cors voor rest api's](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Joker tekens of scenario's met één oorsprong
CORS op Azure front deur werkt automatisch zonder extra configuratie wanneer de header **Access-Control-Allow-Origin** is ingesteld op Joker teken (*) of één oorsprong.  Het eerste antwoord wordt in de cache opgeslagen en de aanvragen worden gebruikt voor dezelfde header.

Als er al aanvragen zijn gedaan voor de Azure-deur voordat CORS op uw oorsprong is ingesteld, moet u de inhoud van de eindpunt inhoud verwijderen om de inhoud opnieuw te laden met de header **Access-Control-Allow-Origin** .

## <a name="multiple-origin-scenarios"></a>Scenario's met meerdere oorsprong
Als u wilt toestaan dat een specifieke lijst met oorsprongen voor CORS wordt toegestaan, zijn de dingen wat gecompliceerder. Het probleem treedt op wanneer de CDN het **toegangs beheer-allow-Origin** -header voor de eerste CORS-oorsprong in cache opslaat.  Wanneer een andere CORS-oorsprong een andere aanvraag maakt, wordt het CDN de in cache opgeslagen **Access-Control-Allow-Origin** -header, die niet overeenkomt. Er zijn verschillende manieren om dit probleem op te lossen.

### <a name="azure-front-door-rule-set"></a>Regel set voor Azure-front-deur

In azure front-deur kunt u een regel maken in de regels voor de Azure-front-deur die zijn [ingesteld](concept-rule-set.md) om de **oorspronkelijke** header van de aanvraag te controleren. Als het een geldige oorsprong is, wordt met de regel de header **Access-Control-Allow-Origin** ingesteld met de juiste waarde. In dit geval wordt de header **Access-Control-Allow-Origin** van de oorspronkelijke server van het bestand genegeerd en worden de toegestane CORS-oorsprong volledig beheerd door de engine van de afd.

:::image type="content" source="../media/troubleshooting-cross-origin-resource-sharing/cross-origin-resource.png" alt-text="Scherm opname van regels voor beeld met regelset.":::

> [!TIP]
> U kunt extra acties aan uw regel toevoegen om extra antwoord headers te wijzigen, zoals **Access-Control-Allow-methoden**.
> 

## <a name="next-steps"></a>Volgende stappen

* Lees hoe u [een Front Door maakt](create-front-door-portal.md).

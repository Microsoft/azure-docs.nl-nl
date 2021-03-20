---
title: Aangepaste domein naam voor Azure API Management-exemplaar configureren
titleSuffix: Azure API Management
description: In dit onderwerp wordt beschreven hoe u een aangepaste domein naam configureert voor uw Azure API Management-exemplaar.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: a7032c64efa486c65830e013373239647a368540
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92311141"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Een aangepaste domein naam configureren voor uw Azure API Management-exemplaar

Wanneer u een Azure API Management service-exemplaar maakt, wijst Azure hieraan een subdomein van toe `azure-api.net` (bijvoorbeeld `apim-service-name.azure-api.net` ). U kunt uw API Management-eind punten echter beschikbaar maken met uw eigen aangepaste domein naam, zoals **contoso.com**. Deze zelf studie laat zien hoe u een bestaande aangepaste DNS-naam toewijst aan eind punten die door een API Management-exemplaar worden weer gegeven.

> [!IMPORTANT]
> API Management accepteert alleen aanvragen met de waarden van de [host-header](https://tools.ietf.org/html/rfc2616#section-14.23) die overeenkomen met de standaard domein naam of een van de geconfigureerde aangepaste domein namen.

> [!WARNING]
> Klanten die een certificaat willen gebruiken om de beveiliging van hun toepassingen te verbeteren, moeten een aangepaste domein naam en een certificaat gebruiken die ze beheren, niet het standaard certificaat. Klanten die het standaard certificaat in plaats daarvan vastmaken, nemen een vaste afhankelijkheid op de eigenschappen van het certificaat die ze niet beheren. Dit is geen aanbevolen procedure.

## <a name="prerequisites"></a>Vereisten

Voor het uitvoeren van de stappen die in dit artikel worden beschreven, hebt u het volgende nodig:

-   Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   Een API Management-exemplaar. Zie [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.
-   Een aangepaste domein naam die eigendom is van u of uw organisatie. In dit onderwerp vindt u geen instructies voor het aanschaffen van een aangepaste domein naam.
-   Een CNAME-record die wordt gehost op een DNS-server die de aangepaste domein naam toewijst aan de standaard domein naam van uw API Management-exemplaar. In dit onderwerp vindt u geen instructies voor het hosten van een CNAME-record.
-   U moet een geldig certificaat met een open bare en persoonlijke sleutel hebben (. PFX). Het onderwerp of de alternatieve naam voor het onderwerp (SAN) moet overeenkomen met de domein naam (dit maakt het mogelijk dat API Management exemplaar veilig Url's weergeeft via TLS).

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>De Azure Portal gebruiken om een aangepaste domein naam in te stellen

1. Navigeer naar uw API Management-exemplaar in de [Azure Portal](https://portal.azure.com/).
1. Selecteer **aangepaste domeinen**.

    Er zijn een aantal eind punten waaraan u een aangepaste domein naam kunt toewijzen. Momenteel zijn de volgende eind punten beschikbaar:

    - **Gateway** (standaard is: `<apim-service-name>.azure-api.net` ),
    - **Ontwikkelaars Portal (verouderd)** (standaard is: `<apim-service-name>.portal.azure-api.net` ),
    - **Ontwikkelaars Portal** (standaard is: `<apim-service-name>.developer.azure-api.net` ).
    - **Beheer** (standaard is: `<apim-service-name>.management.azure-api.net` ),
    - **SCM** (standaard is: `<apim-service-name>.scm.azure-api.net` ),

    > [!NOTE]
    > Alleen het **Gateway** -eind punt is beschikbaar voor configuratie in de laag verbruik.
    > U kunt alle eind punten of een aantal hiervan bijwerken. Klanten updaten **Gateway** (deze URL wordt meestal gebruikt voor het aanroepen van de API die wordt weer gegeven via API Management) en de **Portal** (de URL van de ontwikkelaars Portal).
    > **Beheer** -en **SCM** -eind punten worden intern gebruikt door de eigen aren van het API Management-exemplaar en zijn dus minder vaak toegewezen aan een aangepaste domein naam.
    > De **Premium** -laag biedt ondersteuning voor het instellen van meerdere hostnamen voor het **Gateway** -eind punt.

1. Selecteer het eind punt dat u wilt bijwerken.
1. Klik in het venster aan de rechter kant op **aangepast**.

    - Geef in de naam van het **aangepaste domein** de naam op die u wilt gebruiken. Bijvoorbeeld `api.contoso.com`.
    - Selecteer in het **certificaat** een certificaat van Key Vault. U kunt ook een geldige uploaden. PFX-bestand en geef het **wacht woord** op als het certificaat is beveiligd met een wacht woord.

    > [!NOTE]
    > Domein namen met Joker tekens, zoals `*.contoso.com` worden ondersteund in alle lagen, behalve de laag verbruik.

    > [!TIP]
    > U kunt het beste [Azure Key Vault gebruiken voor het beheren van certificaten](../key-vault/certificates/about-certificates.md) en het instellen ervan op autorenew.
    > Als u Azure Key Vault gebruikt om het TLS/SSL-certificaat van het aangepaste domein te beheren, moet u ervoor zorgen dat het certificaat wordt ingevoegd in Key Vault [als een _certificaat_](/rest/api/keyvault/createcertificate/createcertificate), niet als een _geheim_.
    >
    > Als u een TLS/SSL-certificaat wilt ophalen, moet API Management de lijst hebben en geheimen machtigingen krijgen voor de Azure Key Vault met het certificaat. Wanneer u Azure Portal gebruikt, worden alle benodigde configuratie stappen automatisch voltooid. Wanneer u opdracht regel Programma's of beheer-API gebruikt, moeten deze machtigingen hand matig worden verleend. Dit gebeurt in twee stappen. Gebruik eerst Managed Identities pagina op uw API Management-exemplaar om er zeker van te zijn dat de beheerde identiteit is ingeschakeld en noteer de principal-id die op die pagina wordt weer gegeven. Ten tweede geeft u de machtigingen lijst op en krijgt u een geheimen aan deze principal-id op het Azure Key Vault met het certificaat.
    >
    > Als het certificaat is ingesteld op autorenew, neemt API Management de nieuwste versie automatisch op zonder uitval tijd voor de service (als uw API Management laag SLA-i. e heeft in alle lagen, behalve de laag voor ontwikkel aars).

1. Klik op Toepassen.

    > [!NOTE]
    > Het proces voor het toewijzen van het certificaat kan 15 minuten of langer duren, afhankelijk van de grootte van de implementatie. Ontwikkel aars-SKU heeft downtime, Basic-en hogere Sku's hebben geen downtime.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS-configuratie

Bij het configureren van DNS voor uw aangepaste domein naam hebt u twee opties:

-   Configureer een CNAME-record die verwijst naar het eind punt van uw geconfigureerde aangepaste domein naam.
-   Configureer een A-record die verwijst naar het IP-adres van uw API Management Gateway.

> [!NOTE]
> Hoewel het IP-adres van de API Management-instantie statisch is, kan dit in een paar scenario's worden gewijzigd. Daarom is het raadzaam om CNAME te gebruiken bij het configureren van een aangepast domein. Neem hierbij rekening mee bij het kiezen van een DNS-configuratie methode. Meer informatie vindt u in het [artikel over IP-documentatie](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) en de [API Management Veelgestelde vragen](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services).

## <a name="next-steps"></a>Volgende stappen

[Uw service bijwerken en schalen](upgrade-and-scale.md)

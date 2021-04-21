---
title: Protocolondersteuning voor HTTP-headers in Azure Front Door | Microsoft Docs
description: In dit artikel worden HTTP-headerprotocollen beschreven die Front Door ondersteunen.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2020
ms.author: duau
ms.openlocfilehash: 2ad97656b822bc5ffc957469842436ec84d9e812
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785753"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door"></a>Protocolondersteuning voor HTTP-headers in Azure Front Door
In dit artikel wordt het protocol beschreven dat Front Door ondersteunt met delen van het aanroeppad (zie afbeelding). De volgende secties bevatten meer informatie over HTTP-headers die worden ondersteund door Front Door.

:::image type="content" source="./media/front-door-http-headers-protocol/front-door-protocol-summary.png" alt-text="Azure Front Door HTTP-headerprotocol":::

>[!IMPORTANT]
>Front Door certificeert geen HTTP-headers die hier niet worden beschreven.

## <a name="client-to-front-door"></a>Client naar Front Door
Front Door accepteert de meeste headers voor de binnenkomende aanvraag zonder deze te wijzigen. Sommige gereserveerde headers worden verwijderd uit de binnenkomende aanvraag als ze worden verzonden, inclusief headers met het voorvoegsel X-FD-*.

## <a name="front-door-to-backend"></a>Front Door naar back-end

Front Door bevat headers voor een binnenkomende aanvraag, tenzij ze vanwege beperkingen worden verwijderd. Front Door voegt ook de volgende headers toe:

| Header  | Voorbeeld en beschrijving |
| ------------- | ------------- |
| Via |  *Via: 1.1 Azure* </br> Front Door voegt de HTTP-versie van de client gevolgd *door Azure* toe als de waarde voor de Via-header. Deze header geeft de HTTP-versie van de client aan en Front Door een tussenliggende ontvanger was voor de aanvraag tussen de client en de back-end.  |
| X-Azure-ClientIP | *X-Azure-ClientIP: 127.0.0.1* </br> Vertegenwoordigt het IP-adres van de client dat is gekoppeld aan de aanvraag die wordt verwerkt. Een aanvraag die afkomstig is van een proxy kan bijvoorbeeld de header X-Forwarded-For toevoegen om het IP-adres van de oorspronkelijke aanroeper aan te geven. |
| X-Azure-SocketIP |  *X-Azure-SocketIP: 127.0.0.1* </br> Vertegenwoordigt het IP-adres van de socket dat is gekoppeld aan de TCP-verbinding waar de huidige aanvraag van afkomstig is. Het IP-adres van de client van een aanvraag is mogelijk niet gelijk aan het ip-adres van de socket, omdat het client-IP-adres willekeurig kan worden overschreven door een gebruiker.|
| X-Azure-Ref | *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Een unieke referentiereeks die een aanvraag identificeert die door de Front Door. Het wordt gebruikt om toegangslogboeken te doorzoeken en essentieel voor het oplossen van problemen.|
| X-Azure-RequestChain | *X-Azure-RequestChain: hops=1* </br> Een header die Front Door gebruikt om aanvraaglussen te detecteren en gebruikers mogen er geen afhankelijkheid van hebben. |
| X-Azure-FDID | *X-Azure-FDID: 55ce4ed1-4b06-4bf1-b40e-4638452104da* <br/> Een referentiereeks die de aanvraag identificeert, is afkomstig van een Front Door resource. De waarde kan worden gezien in de Azure Portal of worden opgehaald met behulp van de beheer-API. U kunt deze header gebruiken in combinatie met IP-ACL's om uw eindpunt te vergrendelen om alleen aanvragen van een specifieke resource Front Door accepteren. Zie de Veelgestelde vragen [voor meer informatie](front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
| X-Forwarded-For | *X-Forwarded-For: 127.0.0.1* </br> Het veld X-Forwarded-For (XFF) HTTP-header identificeert vaak het oorspronkelijke IP-adres van een client die verbinding maakt met een webserver via een HTTP-proxy of load balancer. Als er een bestaande XFF-header is, voegt Front Door het IP-adres van de clientsockocke eraan toe of voegt u de XFF-header toe met het IP-adres van de clientsocke. |
| X-Forwarded-Host | *X-Forwarded-Host: contoso.azurefd.net* </br> Het veld X-Forwarded-Host HTTP header is een algemene methode die wordt gebruikt voor het identificeren van de oorspronkelijke host die is aangevraagd door de client in de Host HTTP-aanvraagheader. Dit komt doordat de hostnaam van Front Door kan verschillen voor de back-endserver die de aanvraag afhandelt. |
| X-Forwarded-Proto | *X-Forwarded-Proto: http* </br> Het veld X-Forwarded-Proto HTTP-header wordt vaak gebruikt om het oorspronkelijke protocol van een HTTP-aanvraag te identificeren. Front Door op basis van configuratie kan communiceren met de back-end via HTTPS. Dit geldt zelfs als de aanvraag voor de omgekeerde proxy HTTP is. |
| X-FD-HealthProbe | Het X-FD-HealthProbe HTTP-headerveld wordt gebruikt om de statustest van de Front Door. Als deze header is ingesteld op 1, is de aanvraag statustest. U kunt gebruiken wanneer u strikte toegang wilt vanaf bepaalde Front Door met het veld X-Forwarded-Host-header. |
| X-Azure-FDID | *X-Azure-FDID-header: 437c82cd-360a-4a54-94c3-5ff707647783* </br> Dit veld bevat frontdoorID die kan worden gebruikt om te bepalen Front Door de binnenkomende aanvraag afkomstig is. Dit veld wordt ingevuld door Front Door service. | 

## <a name="front-door-to-client"></a>Front Door naar client

Headers die vanuit de back-Front Door worden verzonden, worden ook doorgegeven aan de client. Hieronder worden headers verzonden van Front Door naar clients.

| Header  | Voorbeeld en beschrijving |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Dit is een unieke referentiereeks die een aanvraag identificeert die door Front Door wordt bediend. Dit is essentieel voor het oplossen van problemen, omdat deze wordt gebruikt om toegangslogboeken te doorzoeken.|
| X-Cache | *X-Cache: TCP_HIT* </br> Deze header beschrijft de cachestatus van de aanvraag, waarmee u kunt bepalen of de antwoordinhoud wordt bediend vanuit de cache van de Front Door. |

U moet de aanvraagheader 'X-Azure-DebugInfo: 1' verzenden om de volgende optionele antwoordheaders in te kunnenschakelen.

| Header  | Voorbeeld en beschrijving |
| ------------- | ------------- |
| X-Azure-OriginStatusCode |  *X-Azure-OriginStatusCode: 503* </br> Deze header bevat de HTTP-statuscode die wordt geretourneerd door de back-end. Met behulp van deze header kunt u de HTTP-statuscode identificeren die wordt geretourneerd door de toepassing die wordt uitgevoerd in uw back-end zonder door back-endlogboeken te gaan. Deze statuscode kan verschillen van de HTTP-statuscode in het antwoord dat naar de client is verzonden door Front Door. Met deze header kunt u bepalen of de back-Front Door werkt of dat het probleem zich voordeed. |
| X-Azure-InternalError | Deze header bevat de foutcode die wordt Front Door bij het verwerken van de aanvraag. Deze fout geeft aan dat het probleem intern is voor Front Door service/infrastructuur. Meld het probleem bij de ondersteuning.  |
| X-Azure-ExternalError | *X-Azure-ExternalError: 0x830c1011 is de certificeringsinstantie onbekend.* </br> Deze header toont de foutcode die Front Door servers tegenkomt tijdens het tot stand brengen van connectiviteit met de back-endserver om een aanvraag te verwerken. Deze header helpt bij het identificeren van problemen in de verbinding tussen Front Door en de back-endtoepassing. Deze header bevat een gedetailleerd foutbericht om u te helpen bij het identificeren van verbindingsproblemen met uw back-end (bijvoorbeeld DNS-resolutie, ongeldig certificaat, etc.). |

## <a name="next-steps"></a>Volgende stappen

- [Een Front Door](quickstart-create-front-door.md)
- [Hoe Front Door werkt](front-door-routing-architecture.md)

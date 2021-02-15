---
title: Algemene fout codes voor Azure Instance Metadata Service (IMDS)
titleSuffix: Azure Load Balancer
description: Overzicht van algemene fout codes en bijbehorende beperkende methoden voor Azure Instance Metadata Service (IMDS)
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519181"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Fout codes: algemene fout codes wanneer u IMDS gebruikt om load balancer gegevens op te halen

In dit artikel worden veelvoorkomende implementatie fouten beschreven en wordt uitgelegd hoe u deze fouten oplost tijdens het gebruik van Azure Instance Metadata Service (IMDS).

## <a name="error-codes"></a>Foutcodes

| Foutcode | Foutbericht | Details en risico beperking |
| --- | ---------- | ----------------- |
| 400 | De vereiste para meter ontbreekt \<ParameterName> . Los de aanvraag op en probeer het opnieuw. | De fout code geeft aan dat er een ontbrekende para meter is. </br> Zie [Load Balancer meta gegevens ophalen met de Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response)voor meer informatie over het toevoegen van de ontbrekende para meter.
| 400 | De parameter waarde is niet toegestaan of de parameter waarde \<ParameterValue> is niet toegestaan voor de para meter-para meter. Los de aanvraag op en probeer het opnieuw. | De fout code geeft aan dat de aanvraag indeling niet juist is geconfigureerd. </br> Zie [Load Balancer meta gegevens ophalen met behulp van de Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) om de aanvraag tekst op te lossen en een nieuwe poging uit te voeren voor meer informatie. |
| 400 | Onverwachte aanvraag. Controleer de query parameters en probeer het opnieuw. | De fout code geeft aan dat de aanvraag indeling niet juist is geconfigureerd. </br> Zie [Load Balancer meta gegevens ophalen met behulp van de Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) om de aanvraag tekst op te lossen en een nieuwe poging uit te voeren voor meer informatie. |
| 404 | Er zijn geen load balancer meta gegevens gevonden. Controleer of uw virtuele machine gebruikmaakt van een niet-basis SKU load balancer en probeer het later opnieuw. | De fout code geeft aan dat uw virtuele machine niet is gekoppeld aan een load balancer of dat de load balancer Basic SKU is in plaats van Standard. </br> Voor meer informatie raadpleegt u [Quick Start: een open bare Load Balancer maken om taken te verdelen over vm's met behulp van de Azure Portal](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) om een standaard Load Balancer te implementeren.|
| 404 | API is niet gevonden: pad = " \<UrlPath> ", methode = " \<Method> " | De fout code geeft een onjuiste configuratie van het pad aan. </br> Zie [Load Balancer meta gegevens ophalen met behulp van de Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) om de aanvraag tekst op te lossen en een nieuwe poging uit te voeren voor meer informatie.|
| 405 | Http-methode is niet toegestaan: pad = " \<UrlPath> ", methode = " \<Method> " | De fout code geeft aan dat er een niet-ondersteunde HTTP-term wordt weer gegeven. </br> Zie [Azure instance metadata service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs) voor ondersteunde werk woorden voor meer informatie. |
| 429 | Te veel aanvragen | De fout code geeft aan dat er een frequentie limiet is. </br> Zie [Azure instance metadata service (IMDS) (Engelstalig)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting)voor meer informatie over de frequentie beperking.|
| 400 | De aanvraag tekst is groter dan MaxBodyLength:... | De fout code geeft aan dat er een aanvraag is die groter is dan de MaxBodyLength. </br> Zie [Load Balancer meta gegevens ophalen met behulp van de Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response)voor meer informatie over de lengte van de hoofd tekst.|
| 400 | De lengte van de parameter sleutel is groter dan MaxParameterKeyLength:... | De fout code geeft aan dat de lengte van de parameter sleutel groter is dan de MaxParameterKeyLength. </br> Zie [Load Balancer meta gegevens ophalen met behulp van de Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response)voor meer informatie over de lengte van de hoofd tekst. |
| 400 | De lengte van de parameter waarde is groter dan MaxParameterValueLength:... | De fout code geeft aan dat de lengte van de parameter sleutel groter is dan de MaxParameterValueLength. </br> Zie [Load Balancer meta gegevens ophalen met behulp van de Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response)voor meer informatie over de lengte van de waarde.|
| 400 | De lengte van de parameter waarde voor de para meter is groter dan MaxHeaderValueLength:... | De fout code geeft aan dat de waarde van de para meter-header groter is dan de MaxHeaderValueLength. </br> Zie [Load Balancer meta gegevens ophalen met behulp van de Azure instance metadata service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response)voor meer informatie over de lengte van de waarde.|
| 404 | Load Balancer-API voor meta gegevens is nu niet beschikbaar. Probeer het later opnieuw | De fout code geeft aan dat de API kan worden ingericht. Probeer de aanvraag later opnieuw. |
| 404 | /metadata/loadbalancer is momenteel niet beschikbaar | De fout code geeft aan dat de API de voortgang van de activering is. Probeer de aanvraag later opnieuw. |
| 503 | De interne service is niet beschikbaar. Probeer het later opnieuw  | De fout code geeft aan dat de API tijdelijk niet beschikbaar is. Probeer de aanvraag later opnieuw. |
|  |  |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure instance metadata service](../virtual-machines/windows/instance-metadata-service.md)


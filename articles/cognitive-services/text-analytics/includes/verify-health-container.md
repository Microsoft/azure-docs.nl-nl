---
title: De status container instantie controleren
titleSuffix: Azure Cognitive Services
description: Meer informatie over het controleren van de Health container-instantie.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009862"
---
### <a name="verify-that-a-container-is-running"></a>Controleren of een container wordt uitgevoerd

1. Selecteer het tabblad **overzicht** en kopieer het IP-adres.
1. Open een nieuw browser tabblad en voer het IP-adres in. Voer bijvoorbeeld in `http://<IP-address>:5000 (http://55.55.55.55:5000` ). De start pagina van de container wordt weer gegeven, waarin u kunt zien dat de container wordt uitgevoerd.

    ![De start pagina van de container weer geven om te controleren of deze wordt uitgevoerd](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecteer de koppeling **Service-API-beschrijving** om naar de Swagger-pagina van de container te gaan.

1. Kies een van de **post** -api's en selecteer **try-out**. De para meters worden weer gegeven, inclusief voorbeeld invoer.

Er zijn verschillende Url's die u ook kunt gebruiken om te controleren of de container wordt uitgevoerd.

|Aanvraag|Doel|
|--|--|
|`http://localhost:5000/`|De container bevat een startpagina.|
|`http://localhost:5000/ready`|Met GET heeft dit een verificatie dat de container gereed is voor het accepteren van een query op het model. Deze aanvraag kan worden gebruikt voor Kubernetes- [en gereedheids tests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/status`|Met GET, zoals het/Ready-eind punt, wordt gecontroleerd of de container wordt uitgevoerd zonder dat er een query voor het model wordt gemaakt. Deze aanvraag kan worden gebruikt voor Kubernetes- [en gereedheids tests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/).|
|`http://localhost:5000/swagger`|Via deze URL biedt de container een volledige set documentatie voor de eind punten en een `Try it now` functie. Met deze functie kunt u uw instellingen invoeren in een HTML-formulier op het web en de query maken zonder dat u code hoeft te schrijven. Nadat de query is geretourneerd, wordt een voor beeld van een krul opdracht weer gegeven om te demonstreren welke HTTP-headers en hoofdtekst indeling vereist zijn. |
|`http://localhost:5000/demo`| Deze functie is aangevraagd via een browser en biedt een interactieve visualisatie van de resultaten van query's van invoer tekst voorbeelden of een die u opgeeft.  |

Gebruik deze aanvraag-URL `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` om een query naar de container te verzenden.

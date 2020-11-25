---
author: IEvangelist
ms.author: dapine
ms.date: 08/22/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 8b3d856d255968b4a6736db908ce3999cbd56193
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996403"
---
## <a name="validate-that-a-container-is-running"></a>Controleren of een container wordt uitgevoerd 

Er zijn verschillende manieren om te controleren of de container wordt uitgevoerd. Zoek het *externe IP-* adres en de weer gegeven poort van de betreffende container en open uw favoriete webbrowser. Gebruik de onderstaande aanvraag-Url's om te controleren of de container wordt uitgevoerd. De onderstaande voorbeeld aanvraag-Url's zijn `http://localhost:5000` , maar uw specifieke container kan variëren. Houd er rekening mee dat u afhankelijk bent van het *externe IP-* adres van uw container en de weer gegeven poort.

| Aanvraag-URL | Doel |
|--|--|
| `http://localhost:5000/` | De container bevat een startpagina. |
| `http://localhost:5000/ready` | Met GET heeft dit een verificatie dat de container gereed is voor het accepteren van een query op het model.  Deze aanvraag kan worden gebruikt voor Kubernetes- [en gereedheids tests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://localhost:5000/status` | Daarnaast wordt met GET gevraagd of de API-sleutel die wordt gebruikt om de container te starten, geldig is zonder dat dit een eindpunt query veroorzaakt. Deze aanvraag kan worden gebruikt voor Kubernetes- [en gereedheids tests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/). |
| `http://localhost:5000/swagger` | De container bevat een volledige set met documentatie voor de eindpunten en een functie **Uitproberen**. Met deze functie kunt u uw instellingen invoeren in een HTML-formulier op het web en de query maken zonder dat u code hoeft te schrijven. Nadat de query is geretourneerd, wordt een voor beeld van een krul opdracht weer gegeven om te demonstreren welke HTTP-headers en hoofdtekst indeling vereist zijn. |

![Start pagina van container](./media/cognitive-services-containers-api-documentation/container-webpage.png)

---
title: Gegevenslimieten voor de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Gegevenslimieten voor de Text Analytics-API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: c60adb09da05ba945bcf6ccb55e71c395f064211
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965099"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Gegevens- en frequentielimieten voor de Text Analytics-API
<a name="data-limits"></a>

Lees dit artikel voor informatie over de limieten voor de grootte en de frequenties waarmee u gegevens naar de Text Analytics-API kunt verzenden. 

## <a name="data-limits"></a>Gegevenslimieten

> [!NOTE]
> * Als u grotere documenten moet analyseren dan volgens de limiet is toegestaan, kunt u de tekst opdelen in kleinere tekstfragmenten voordat u ze naar de API kunt verzenden. 
> * Een document bestaat uit één tekenreeks van teksttekens.  

| Limiet | Waarde |
|------------------------|---------------|
| Maximale grootte van één document | 5\.120 tekens, gemeten volgens [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Geldt ook voor Text Analytics voor status. |
| Maximale grootte van één document (`/analyze` eindpunt)  | 125 K tekens, gemeten volgens [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Geldt niet voor Text Analytics voor status. |
| Maximale grootte van de hele aanvraag | 1 MB. Geldt ook voor Text Analytics voor status. |

Het maximumaantal documenten dat u in één aanvraag kunt verzenden, is afhankelijk van de API-versie en functie die u gebruikt. Het eindpunt `/analyze` weigert de hele aanvraag als een of meer van de documenten de maximale grootte overschrijdt (125 K tekens)

#### <a name="version-3"></a>[Versie 3](#tab/version-3)

De volgende limieten gelden voor de huidige v3 API. Als u de onderstaande limieten overschrijdt, wordt een HTTP 400-foutcode gegenereerd.


| Functie | Max. aantal documenten per aanvraag | 
|----------|-----------|
| Taaldetectie | 1000 |
| Sentimentanalyse | 10 |
| Meninganalyse | 10 |
| Sleuteltermextractie | 10 |
| Herkenning van benoemde entiteiten | 5 |
| Entiteiten koppelen | 5 |
| Text Analytics voor status  | 10 voor de web-API, 1000 voor de container. |
| Eindpunt analyseren | 25 voor alle bewerkingen. |

#### <a name="version-2"></a>[Versie 2](#tab/version-2)

| Functie | Max. aantal documenten per aanvraag | 
|----------|-----------|
| Taaldetectie | 1000 |
| Sentimentanalyse | 1000 |
| Sleuteltermextractie | 1000 |
| Herkenning van benoemde entiteiten | 1000 |
| Entiteiten koppelen | 1000 |

---

## <a name="rate-limits"></a>Frequentielimieten

Uw frequentielimiet hangt af van uw [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Deze limieten gelden voor beide versies van de API. Deze frequentielimieten zijn niet van toepassing op de Text Analytics voor de statuscontainer, die geen ingestelde frequentielimiet heeft.

| Laag          | Aanvragen per seconde | Aanvragen per minuut |
|---------------|---------------------|---------------------|
| S/multi-service | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Het aantal aanvragen wordt voor elke Text Analytics-functie afzonderlijk gemeten. U kunt het maximumaantal aanvragen voor uw prijscategorie tegelijkertijd naar elke functie verzenden. Als u zich bijvoorbeeld in de categorie `S` bevindt en 1000 aanvragen tegelijk verzendt, kunt u gedurende 59 seconden hierna geen aanvragen verzenden.


## <a name="see-also"></a>Zie ook

* [Wat is de Text Analytics-API?](../overview.md)
* [Prijsdetails](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)

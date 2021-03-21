---
title: Ondersteunde categorieën voor herkenning van benoemde entiteiten
titleSuffix: Azure Cognitive Services
description: Meer informatie over de ondersteunde entiteits categorieën vindt u in de Text Analytics-API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 8b596a5e54c0b59c4c0b49aa5cdc4fd6477d46dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599301"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>Ondersteunde entiteits categorieën in de Text Analytics-API v3

In dit artikel vindt u informatie over de entiteits categorieën die kunnen worden geretourneerd door [named entity Recognition](how-tos/text-analytics-how-to-entity-linking.md) (ner). NER voert een voorspellend model uit voor het identificeren en categoriseren van benoemde entiteiten uit een invoer document.

Er is ook een preview van NER v 3.1 beschikbaar, waaronder de mogelijkheid om persoonlijke ( `PII` ) en status gegevens te detecteren `PHI` . Klik vervolgens op het tabblad **status** om een lijst met ondersteunde categorieën in Text Analytics voor de status weer te geven. 

In de [migratie handleiding](migration-guide.md?tabs=named-entity-recognition) vindt u een lijst met typen die worden geretourneerd door versie 2,1

## <a name="entity-categories"></a>Entiteits Categorieën

#### <a name="general"></a>[Algemeen](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[VERZAMELD](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[Gezondheidszorg](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>Volgende stappen

* [Benoemde entiteits herkenning gebruiken in Text Analytics](how-tos/text-analytics-how-to-entity-linking.md)

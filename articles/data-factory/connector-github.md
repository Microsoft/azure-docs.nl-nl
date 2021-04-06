---
title: Verbinding maken met GitHub
description: Gebruik GitHub om uw algemene gegevens model entiteit verwijzingen op te geven
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372277"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Gebruik GitHub om veelvoorkomende gegevens model entiteit verwijzingen te lezen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De GitHub-connector in Azure Data Factory wordt alleen gebruikt voor het ontvangen van het verwijzings schema van de entiteit voor de [gemeen schappelijke gegevens model](format-common-data-model.md) indeling in gegevens stroom toewijzen.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde GitHub-service.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **github**. | ja
| userName | GitHub-gebruikers naam | ja |
| wachtwoord | GitHub-wacht woord | ja |

## <a name="next-steps"></a>Volgende stappen

Maak een [bron-gegevensset](data-flow-source.md) in gegevens stroom toewijzen.
---
title: Regels voor het benoemen van Azure Data Factory entiteiten-versie 1
description: Hierin worden de naamgevings regels voor Data Factory v1-entiteiten beschreven.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 0ef997f54deafc56f1b3af603071c0e650be40d4
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495392"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Regels voor het benoemen van Azure Data Factory entiteiten
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u de [naamgevings regels in Data Factory](../naming-rules.md).

De volgende tabel bevat naamgevings regels voor Data Factory artefacten.

| Naam | Unieke naam | Validatie controles |
|:--- |:--- |:--- |
| Data Factory |Uniek in Microsoft Azure. Namen zijn niet hoofdletter gevoelig, dat wil zeggen, `MyDF` en `mydf` verwijzen naar dezelfde Data Factory. |<ul><li>Elk data factory is gekoppeld aan precies één Azure-abonnement.</li><li>Object namen moeten beginnen met een letter of een cijfer en mogen alleen letters, cijfers en het koppel teken (-) bevatten.</li><li>Elk streepje (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. Opeenvolgende streepjes zijn niet toegestaan in container namen.</li><li>De naam kan 3-63 tekens lang zijn.</li></ul> |
| Gekoppelde services/tabellen/pijp lijnen |Uniek met in een data factory. Namen zijn niet hoofdlettergevoelig. |<ul><li>Maximum aantal tekens in de naam van de tabel: 260.</li><li>Object namen moeten beginnen met een letter, cijfer of onderstrepings teken (_).</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ' \\ , ': ', ' '</li></ul> |
| Resourcegroep |Uniek in Microsoft Azure. Namen zijn niet hoofdlettergevoelig. |<ul><li>Maximum aantal tekens: 1000.</li><li>De naam mag letters, cijfers en de volgende tekens bevatten: '-', ' _ ', ', ' en '. '</li></ul> |


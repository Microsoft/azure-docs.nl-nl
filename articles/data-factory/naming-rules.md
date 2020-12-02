---
title: Regels voor het benoemen van Azure Data Factory entiteiten
description: Hierin worden de naamgevings regels voor Data Factory entiteiten beschreven.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 21adf26c2dbaca4507a4c925e3dae3b99c9d53ba
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497517"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory naamgevings regels

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

De volgende tabel bevat naamgevings regels voor Data Factory artefacten.

| Naam | Unieke naam | Validatie controles |
|:--- |:--- |:--- |
| Data Factory | Uniek in Microsoft Azure. Namen zijn niet hoofdletter gevoelig, dat wil zeggen, `MyDF` en `mydf` verwijzen naar dezelfde Data Factory. |<ul><li>Elk data factory is gekoppeld aan precies één Azure-abonnement.</li><li>Object namen moeten beginnen met een letter of een cijfer en mogen alleen letters, cijfers en het koppel teken (-) bevatten.</li><li>Elk streepje (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. Opeenvolgende streepjes zijn niet toegestaan in container namen.</li><li>De naam kan 3-63 tekens lang zijn.</li></ul> |
| Gekoppelde services/data sets/pijp lijnen/gegevens stromen | Uniek met in een data factory. Namen zijn niet hoofdlettergevoelig. |<ul><li>Object namen moeten beginnen met een letter.</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ', ': ', ' \\ '</li><li>Streepjes ('-') zijn niet toegestaan in de namen van gekoppelde services, gegevens stromen en data sets.</li></ul>  |
| Integration Runtime |Uniek met in een data factory. Namen zijn niet hoofdlettergevoelig. |<ul><li>De naam van de Integration runtime mag alleen letters, cijfers en het koppel teken (-) bevatten.</li><li>De eerste en laatste tekens moeten een letter of cijfer zijn. Elk streepje (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer.</li><li>Opeenvolgende streepjes zijn niet toegestaan in de naam van de Integration runtime. </li></ul> |
| Gegevensstroomtransformaties | Uniek zijn binnen een gegevens stroom. Namen zijn niet hoofdletter gevoelig | <ul><li>Data flow-transformatie namen mogen alleen letters en cijfers bevatten</li><li>Het eerste teken moet een letter zijn. </li></ul> |
| Resourcegroep |Uniek in Microsoft Azure. Namen zijn niet hoofdlettergevoelig. | Zie [Azure-naamgevings regels en-beperkingen](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)voor meer informatie. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het maken van gegevens fabrieken met behulp van stapsgewijze instructies in [Quick Start: een Data Factory](quickstart-create-data-factory-powershell.md) -artikel maken. 

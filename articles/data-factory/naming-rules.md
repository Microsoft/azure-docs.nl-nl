---
title: Regels voor het benoemen van Azure Data Factory entiteiten
description: Hierin worden de naamgevings regels voor Data Factory entiteiten beschreven.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: a1a0622c0736bad5f6c205fab01f4405577ed67a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783350"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory naamgevings regels

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

De volgende tabel bevat naamgevings regels voor Data Factory artefacten.

| Name | Unieke naam | Validatie controles |
|:--- |:--- |:--- |
| Data Factory | Uniek in Microsoft Azure. Namen zijn niet hoofdletter gevoelig, dat wil zeggen, `MyDF` en `mydf` verwijzen naar dezelfde Data Factory. |<ul><li>Elk data factory is gekoppeld aan precies één Azure-abonnement.</li><li>Object namen moeten beginnen met een letter of een cijfer en mogen alleen letters, cijfers en het koppel teken (-) bevatten.</li><li>Elk streepje (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer. Opeenvolgende streepjes zijn niet toegestaan in container namen.</li><li>De naam kan 3-63 tekens lang zijn.</li></ul> |
| Gekoppelde services/data sets/pijp lijnen/gegevens stromen | Uniek binnen een data factory. Namen zijn niet hoofdlettergevoelig. |<ul><li>Object namen moeten beginnen met een letter.</li><li>De volgende tekens zijn niet toegestaan: '. ', ' + ', '? ', '/', ' < ', ' > ', ' * ', '% ', ' & ', ': ', ' \\ '</li><li>Streepjes ('-') zijn niet toegestaan in de namen van gekoppelde services, gegevens stromen en data sets.</li></ul>  |
| Integration Runtime |Uniek binnen een data factory. Namen zijn niet hoofdlettergevoelig. |<ul><li>De naam van de Integration runtime mag alleen letters, cijfers en het koppel teken (-) bevatten.</li><li>De eerste en laatste tekens moeten een letter of cijfer zijn. Elk streepje (-) moet direct worden voorafgegaan en gevolgd door een letter of cijfer.</li><li>Opeenvolgende streepjes zijn niet toegestaan in de naam van de Integration runtime. </li></ul> |
| Gegevensstroomtransformaties | Uniek zijn binnen een gegevens stroom. Namen zijn niet hoofdletter gevoelig | <ul><li>Data flow-transformatie namen mogen alleen letters en cijfers bevatten</li><li>Het eerste teken moet een letter zijn. </li></ul> |
| Resourcegroep |Uniek in Microsoft Azure. Namen zijn niet hoofdlettergevoelig. | Zie [Azure-naamgevings regels en-beperkingen](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)voor meer informatie. |
| Pijplijn parameters & variabele  |Uniek binnen de pijp lijn. Namen zijn niet hoofdlettergevoelig. | <ul><li>Validatie controle op parameter namen en namen van variabelen is beperkt tot uniekheid vanwege compatibiliteit met eerdere versies.</li><li>Bij gebruik van para meters of variabelen om te verwijzen naar entiteits namen, bijvoorbeeld gekoppelde service, zijn de naamgevings regels voor entiteiten van toepassing.</li><li>Het is een goed idee om de naamgevings regels voor de gegevens stroom transformatie te volgen om uw pijplijn parameters en variabelen een naam te geven.</li></ul> |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van gegevens fabrieken met behulp van stapsgewijze instructies in [Quick Start: een Data Factory](quickstart-create-data-factory-powershell.md) -artikel maken. 

---
title: Variabele activity toevoegen in Azure Data Factory
description: Meer informatie over het instellen van de activiteit variabele toevoegen om een waarde toe te voegen aan een bestaande matrix variabele die in een Data Factory pijp lijn is gedefinieerd
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.date: 10/09/2018
ms.openlocfilehash: 1ca58fc208bb02d137b977e0b18857e8c87a5440
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783843"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Variabele activity toevoegen in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Gebruik de activiteit variabele toevoegen om een waarde toe te voegen aan een bestaande matrix variabele die in een Data Factory pijp lijn is gedefinieerd.

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
naam | De naam van de activiteit in de pijp lijn | Yes
beschrijving | Tekst die beschrijft wat de activiteit doet | nee
type | Type activiteit is AppendVariable | ja
waarde | Letterlijke teken reeks of expressie object waarde die wordt gebruikt om toe te voegen aan de opgegeven variabele | ja
variableName | Naam van de variabele die wordt gewijzigd door activiteit, de variabele moet van het type matrix zijn | ja

## <a name="next-steps"></a>Volgende stappen
Meer informatie over een gerelateerde controle stroom activiteit die wordt ondersteund door Data Factory: 

- [Activiteit variabele instellen](control-flow-set-variable-activity.md)

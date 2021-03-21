---
title: Variabele activity toevoegen in Azure Data Factory
description: Meer informatie over het instellen van de activiteit variabele toevoegen om een waarde toe te voegen aan een bestaande matrix variabele die in een Data Factory pijp lijn is gedefinieerd
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.date: 10/09/2018
ms.openlocfilehash: 5a9ed44e05c371460ae3ceab721f2236f6ec7fd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383405"
---
# <a name="append-variable-activity-in-azure-data-factory"></a>Variabele activity toevoegen in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Gebruik de activiteit variabele toevoegen om een waarde toe te voegen aan een bestaande matrix variabele die in een Data Factory pijp lijn is gedefinieerd.

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Vereist
-------- | ----------- | --------
naam | De naam van de activiteit in de pijp lijn | Ja
beschrijving | Tekst die beschrijft wat de activiteit doet | nee
type | Type activiteit is AppendVariable | ja
waarde | Letterlijke teken reeks of expressie object waarde die wordt gebruikt om toe te voegen aan de opgegeven variabele | ja
variableName | Naam van de variabele die wordt gewijzigd door activiteit, de variabele moet van het type matrix zijn | ja

## <a name="next-steps"></a>Volgende stappen
Meer informatie over een gerelateerde controle stroom activiteit die wordt ondersteund door Data Factory: 

- [Activiteit variabele instellen](control-flow-set-variable-activity.md)

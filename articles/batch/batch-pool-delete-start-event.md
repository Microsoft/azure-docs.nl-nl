---
title: Gebeurtenis Azure Batch groep verwijderen
description: Verwijzing voor de gebeurtenis voor het verwijderen van een batch-pool. Deze gebeurtenis wordt verzonden als het verwijderen van een groep is gestart.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803745"
---
# <a name="pool-delete-start-event"></a>Gebeurtenis pool verwijderen starten

 Deze gebeurtenis wordt verzonden als het verwijderen van een groep is gestart. Omdat het verwijderen van de groep een asynchrone gebeurtenis is, kunt u verwachten dat de groep verwijderen voltooid moet worden verzonden zodra de bewerking delete is voltooid.

 In het volgende voor beeld ziet u de hoofd tekst van de gebeurtenis groep verwijderen (start).

```
{
   "id": "myPool1"
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van de pool.|

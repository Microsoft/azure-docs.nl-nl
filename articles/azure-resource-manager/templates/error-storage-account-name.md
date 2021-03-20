---
title: Fout in de naam van het opslag account
description: Hierin worden de fouten beschreven die u kunt tegen komen wanneer u de naam van een opslag account opgeeft.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76153969"
---
# <a name="resolve-errors-for-storage-account-names"></a>Fouten voor de namen van opslag accounts oplossen

In dit artikel worden naam fouten beschreven die u kunt tegen komen bij het implementeren van een opslag account.

## <a name="symptom"></a>Symptoom

Als de naam van uw opslag account verboden tekens bevat, ontvangt u een fout melding zoals:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Voor opslag accounts moet u een naam opgeven voor de resource die uniek is binnen Azure. Als u geen unieke naam opgeeft, krijgt u een foutmelding als:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Als u een opslag account met dezelfde naam als een bestaand opslag account in uw abonnement implementeert, maar een andere locatie opgeeft, wordt er een fout bericht weer gegeven met de melding dat het opslag account al bestaat op een andere locatie. Verwijder het bestaande opslag account of geef dezelfde locatie op als het bestaande opslag account.

## <a name="cause"></a>Oorzaak

Opslagaccountnamen moeten tussen 3 en 24 tekens lang zijn en mogen alleen getallen en kleine letters bevatten. De naam moet uniek zijn.

## <a name="solution"></a>Oplossing

Zorg ervoor dat de naam van het opslag account uniek is. U kunt een unieke naam maken door uw naam Conventie samen te voegen met het resultaat van de functie [Unique string](template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Zorg ervoor dat de naam van uw opslag account niet langer is dan 24 tekens. De functie [Unique string](template-functions-string.md#uniquestring) retourneert 13 tekens. Als u een voor voegsel of achtervoegsel aan het **Unique string** resultaat samenvoegt, geeft u een waarde op van 11 tekens of minder.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The value to use for starting the storage account name."
    }
  }
}
```

Zorg ervoor dat de naam van uw opslag account geen hoofd letters of speciale tekens bevat.
---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: a87fbbb276fd8813492cc293bc08b958ee3d6b7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070253"
---
Gebruik de Azure CLI AZ-opdracht voor sleutel [kluis maken](/cli/azure/keyvault#az_keyvault_create) om een Key Vault te maken in de resource groep uit de vorige stap. U moet enkele gegevens verstrekken:

- Naam van de sleutelkluis: Een tekenreeks van 3 tot en met 24 tekens, die alleen cijfers (0-9), letters (a-z, A-Z) en afbreekstreepjes (-) mag bevatten.

  > [!Important]
  > Elke sleutelkluis moet een unieke naam hebben. Vervang <your-unique-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.

- Naam van resourcegroep: **myResourceGroup**.
- De locatie: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

De uitvoer van deze opdracht bevat eigenschappen van de nieuw gemaakte sleutel kluis. Let op de onderstaande twee eigenschappen:

- **Kluisnaam**: de naam die u hierboven hebt opgegeven voor de parameter --name.
- **Kluis-URI**: In het voorbeeld is dit https://&lt;unieke-naam-van-uw-sleutelkluis&gt;.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

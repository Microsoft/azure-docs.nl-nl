---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d19c656946817b06cd620d8a48073bed8299af7d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502250"
---
Gebruik de Azure PowerShell [cmdlet New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) om een Key Vault maken in de resourcegroep uit de vorige stap. U moet enkele gegevens verstrekken:

- Naam van de sleutelkluis: Een tekenreeks van 3 tot en met 24 tekens, die alleen cijfers (0-9), letters (a-z, A-Z) en afbreekstreepjes (-) mag bevatten.

  > [!Important]
  > Elke sleutelkluis moet een unieke naam hebben. Vervang <your-unique-keyvault-name> door de naam van uw sleutelkluis in de volgende voorbeelden.

- Naam van resourcegroep: **myResourceGroup**.
- De locatie: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

De uitvoer van deze cmdlet toont eigenschappen van de nieuw gemaakte sleutelkluis. Let op de onderstaande twee eigenschappen:

- **Kluisnaam**: de naam die u hierboven hebt opgegeven voor de parameter --name.
- **Kluis-URI**: In het voorbeeld is dit https://&lt;unieke-naam-van-uw-sleutelkluis&gt;.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

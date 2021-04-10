---
title: Sjablonen tussen JSON en Bicep converteren
description: Hierin worden opdrachten beschreven voor het converteren van Azure Resource Manager sjablonen van Bicep naar JSON en van JSON naar Bicep.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103422185"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>ARM-sjablonen converteren tussen JSON en Bicep

In dit artikel wordt beschreven hoe u Azure Resource Manager sjablonen (ARM-sjablonen) converteert van JSON naar Bicep en van Bicep naar JSON.

U moet de [BICEP cli hebben geïnstalleerd](bicep-install.md) om de conversie opdrachten uit te voeren.

De conversie opdrachten maken sjablonen die functioneel equivalent zijn. Ze zijn echter mogelijk niet precies hetzelfde in de implementatie. Als u een sjabloon converteert van JSON naar Bicep en vervolgens terugkeert naar JSON, resulteert dit waarschijnlijk in een sjabloon met een andere syntaxis dan de oorspronkelijke sjabloon. Wanneer de geconverteerde sjablonen worden geïmplementeerd, worden dezelfde resultaten gegenereerd.

## <a name="convert-from-json-to-bicep"></a>Converteren van JSON naar Bicep

De Bicep CLI bevat een opdracht voor het decompileren van een bestaande JSON-sjabloon in een Bicep-bestand. Gebruik voor het decompileren van een JSON-bestand:

```azurecli
bicep decompile mainTemplate.json
```

Met deze opdracht wordt een start punt geboden voor Bicep-ontwerp. De opdracht werkt niet voor alle sjablonen. Op dit moment kunnen geneste sjablonen alleen worden ontcompileerd als ze het evaluatie bereik ' binnenste ' gebruiken. Sjablonen die gebruikmaken van Kopieer lussen kunnen niet worden ontcompilatied.

## <a name="convert-from-bicep-to-json"></a>Converteren van Bicep naar JSON

De Bicep CLI biedt ook een opdracht om Bicep te converteren naar JSON. Als u een JSON-bestand wilt maken, gebruikt u:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Sjabloon exporteren en converteren

U kunt de sjabloon voor een resource groep exporteren en deze vervolgens rechtstreeks door geven aan de opdracht decompileren. In het volgende voor beeld ziet u hoe u een geëxporteerde sjabloon decompileert.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exporteer de sjabloon](export-template-portal.md) via de portal.

Gebruiken `bicep decompile <filename>` op het gedownloade bestand.

---

## <a name="side-by-side-view"></a>Weer gave naast elkaar

Met de [Bicep Playground](https://aka.ms/bicepdemo) kunt u GELIJKWAARDIGe JSON-en Bicep-bestanden naast elkaar weer geven. U kunt een voorbeeld sjabloon selecteren om beide versies weer te geven. Of selecteer `Decompile` om uw eigen JSON-sjabloon te uploaden en het equivalente Bicep-bestand weer te geven.

## <a name="next-steps"></a>Volgende stappen

Zie [Bicep-zelf studie](./bicep-tutorial-create-first-bicep.md)voor meer informatie over de Bicep.

---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88689546"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

De volgende eigenschappen zijn beschikbaar in `extensionBundle`:

| Eigenschap | Beschrijving |
| -------- | ----------- |
| id | De naamruimte voor extensiebundels van Microsoft Azure Functions. |
| versie | De versie van de bundel die moet worden geïnstalleerd. De Functions-runtime kiest altijd de maximaal toegestane versie die door het versiebereik of -interval is gedefinieerd. Met de bovenstaande versiewaarde zijn alle bundelversies van 1.0.0 tot 2.0.0 toegestaan. Zie de [intervalnotatie voor het opgeven van versiebereiken](/nuget/reference/package-versioning#version-ranges) voor meer informatie. |
---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027949"
---
De [Configuration Manager-bibliotheek van Microsoft Azure voor .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) biedt een klasse voor het parseren van een verbindingsreeks uit een configuratiebestand. De klasse [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) parseert configuratie-instellingen. Het parseert instellingen voor client toepassingen die worden uitgevoerd op het bureau blad, op een mobiel apparaat, op een virtuele machine van Azure of in een Azure-Cloud service.

Als u wilt verwijzen naar het `CloudConfigurationManager` pakket, voegt u de volgende `using` instructies toe:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

In het volgende voorbeeld ziet u hoe u een verbindingsreeks ophaalt uit een configuratiebestand:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Het gebruik van Azure Configuration Manager is optioneel. U kunt ook een API gebruiken, zoals de ConfigurationManager- [klasse](/dotnet/api/system.configuration.configurationmanager)van de .NET Framework.
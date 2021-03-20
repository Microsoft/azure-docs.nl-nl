---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176705"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>De structuur definitie voor rechthoekige gegevens sets opgeven
De sectie structuur in de JSON gegevens sets is een **optionele** sectie voor rechthoekige tabellen (met rijen & kolommen) en bevat een verzameling kolommen voor de tabel. U gebruikt de sectie structure voor ofwel type-informatie opgeven voor type conversies of voor het uitvoeren van kolom toewijzingen. In de volgende secties worden deze functies uitvoerig beschreven. 

Elke kolom bevat de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
| --- | --- | --- |
| naam |De naam van de kolom. |Ja |
| type |Het gegevens type van de kolom. Zie de sectie Type conversies hieronder voor meer informatie over wanneer u type gegevens moet opgeven |Nee |
| culturele |Op .NET gebaseerde cultuur die moet worden gebruikt wanneer type is opgegeven en is .NET type datetime of date time offset. De standaard waarde is "en-US". |Nee |
| indeling |Indelings teken reeks die moet worden gebruikt wanneer type is opgegeven en is .NET type datetime of date time offset. |Nee |

In het volgende voor beeld ziet u de JSON sectie structure voor een tabel met drie kolommen GebruikersID, name en lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Gebruik de volgende richt lijnen voor het toevoegen van ' Structure ' informatie en wat u in de sectie **structure** wilt toevoegen.

* **Voor gestructureerde gegevens bronnen** die gegevens schema opslaan en gegevens typen, samen met de gegevens zelf (bronnen zoals SQL Server, Oracle, Azure Table enz.), moet u de sectie ' Structure ' alleen opgeven als u wilt dat kolom toewijzing van specifieke bron kolommen aan specifieke kolommen in Sink en de namen ervan niet hetzelfde is (Zie de sectie Details in kolom toewijzing hieronder). 
  
    Zoals hierboven vermeld, is de type-informatie optioneel in de sectie ' Structure '. Voor gestructureerde bronnen is type informatie al beschikbaar als onderdeel van de definitie van de gegevensset in het gegevens archief. u moet dus geen type gegevens opnemen wanneer u de sectie ' Structure ' toevoegt.
* **Voor schema op gegevens bronnen lezen (met name Azure Blob)**  kunt u ervoor kiezen om gegevens op te slaan zonder schema op te slaan of door gegevens te typen met de gegevens. Voor deze typen gegevens bronnen moet u ' Structure ' in de volgende twee gevallen toevoegen:
  * U wilt kolom toewijzing.
  * Wanneer de gegevensset een bron in een Kopieer activiteit is, kunt u type-informatie opgeven in ' Structure ' en data factory deze type gegevens gebruiken voor conversie naar systeem eigen typen voor de sink. Zie [gegevens verplaatsen van en naar Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) -artikel voor meer informatie.

### <a name="supported-net-based-types"></a>Geboden. Op het NET gebaseerde typen
Data Factory ondersteunt de volgende CLS-compatibele .NET-type waarden voor het leveren van type-informatie in ' Structure ' voor schema op gegevens bronnen lezen zoals Azure Blob.

* Int16
* Int32 
* Int64
* Enkelvoudig
* Dubbel
* Decimaal
* Byte []
* Booleaanse waarde
* Tekenreeks 
* Guid
* Datum/tijd
* Date time offset
* Periode 

Voor datetime & date time offset kunt u desgewenst ook de teken reeks ' Culture ' & ' Format ' opgeven om het parseren van uw aangepaste datetime-teken reeks te vergemakkelijken. Zie voor beeld voor type conversie hieronder.


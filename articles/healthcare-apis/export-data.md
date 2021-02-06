---
title: De export uitvoeren door $export opdracht aan te roepen in azure API voor FHIR
description: In dit artikel wordt beschreven hoe u FHIR gegevens exporteert met $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 3437c8bcf8ff508149abae2549d7c34521700840
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627260"
---
# <a name="how-to-export-fhir-data"></a>FHIR-gegevens exporteren


Met de functie voor bulk export kunnen gegevens uit de FHIR-server worden geëxporteerd volgens de [FHIR-specificatie](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Voordat u $export gebruikt, moet u ervoor zorgen dat de Azure-API voor FHIR is geconfigureerd om deze te gebruiken. Raadpleeg [de pagina export gegevens configureren](configure-export-data.md)voor meer informatie over het configureren van export instellingen en het maken van een Azure Storage-account.

## <a name="using-export-command"></a>$export opdracht gebruiken

Na het configureren van de Azure-API voor FHIR voor het exporteren, kunt u de $export opdracht gebruiken om de gegevens uit de service te exporteren. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het configureren van de export. Lees voor meer informatie over het aanroepen van $export-opdracht in FHIR-Server documentatie over de [HL7 FHIR $export-specificatie](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

De Azure API voor FHIR ondersteunt $export op de volgende niveaus:
* [Systeem](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [Patiënt](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Groep patiënten *](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) -Azure-API voor FHIR exporteert alle gerelateerde resources, maar exporteert niet de kenmerken van de groep: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

Wanneer gegevens worden geëxporteerd, wordt een afzonderlijk bestand gemaakt voor elk resource type. Om ervoor te zorgen dat de geëxporteerde bestanden niet te groot worden, maken we een nieuw bestand nadat het formaat van één geëxporteerd bestand groter is dan 64 MB. Het resultaat is dat er meerdere bestanden kunnen worden opgehaald voor elk bron type, dat wordt geïnventariseerd (d.w.z. patiënt-1. ndjson, patiënt-2. ndjson). 


> [!Note] 
> `Patient/$export` en `Group/[ID]/$export` kan dubbele resources exporteren als de resource zich in een compartiment van meer dan één resource bevindt of zich in meerdere groepen bevindt.

Daarnaast wordt het controleren van de export status via de URL die wordt geretourneerd door de locatie-header tijdens de wachtrij, ondersteund in combi natie met het annuleren van de daad werkelijke export taak.



## <a name="settings-and-parameters"></a>Instellingen en para meters

### <a name="headers"></a>Kopteksten
Er zijn twee vereiste header parameters die moeten worden ingesteld voor $export-taken. De waarden worden gedefinieerd door de huidige [$export-specificatie](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Accept** -Application/fhir + JSON
* Voor **keur** : reageren-async

### <a name="query-parameters"></a>Queryparameters
De Azure API voor FHIR ondersteunt de volgende query parameters. Al deze para meters zijn optioneel:

|Queryparameter        | Gedefinieerd door de FHIR spec?    |  Description|
|------------------------|---|------------|
| \_Output | Yes | Ondersteunt momenteel drie waarden om uit te lijnen op de FHIR spec: Application/FHIR + ndjson, Application/ndjson of net ndjson. Alle export taken worden geretourneerd `ndjson` en de door gegeven waarde heeft geen effect op het gedrag van de code. |
| \_moment | Yes | Hiermee kunt u alleen resources exporteren die zijn gewijzigd sinds de gegeven tijd |
| \_voert | Yes | Hiermee kunt u opgeven welke typen resources worden opgenomen. \_Typ bijvoorbeeld = patiënt retourneert alleen patiënten-resources|
| \_typefilter | Yes | Als u een filter met een nauw keurig korrel wilt aanvragen, kunt u \_ typefilter gebruiken in combi natie met de \_ type-para meter. De waarde van de para meter _typeFilter is een door komma's gescheiden lijst met FHIR query's waarmee de resultaten verder worden beperkt |
| \_verpakking | No |  Hiermee geeft u de container in het geconfigureerde opslag account op waarin de gegevens moeten worden geëxporteerd. Als er een container is opgegeven, worden de gegevens naar die container geëxporteerd in een nieuwe map met de naam. Als de container niet is opgegeven, wordt deze geëxporteerd naar een nieuwe container met de tijds tempel-en taak-ID. |


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u FHIR-resources kunt exporteren met behulp van $export opdracht. Meer informatie over het exporteren van de gegevens die u niet hebt geïdentificeerd:
 
>[!div class="nextstepaction"]
>[De geïdentificeerde gegevens exporteren](de-identified-export.md)

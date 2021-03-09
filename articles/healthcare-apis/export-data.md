---
title: De export uitvoeren door $export opdracht aan te roepen in azure API voor FHIR
description: In dit artikel wordt beschreven hoe u FHIR gegevens exporteert met $export
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 2/19/2021
ms.author: cavoeg
ms.openlocfilehash: 9ed78baed35312b9a33c71a3e49b7e9dca22eb9f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487216"
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

### <a name="exporting-fhir-data-to-adls-gen2"></a>FHIR-gegevens exporteren naar ADLS Gen2

Momenteel ondersteunen we $export voor ADLS Gen2 ingeschakelde opslag accounts, met de volgende beperking:

- De gebruiker kan nog niet profiteren van [hiërarchische naam ruimten](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) . Er is geen manier om het exporteren naar een specifieke submap binnen de container te richten. We bieden alleen de mogelijkheid om een specifieke container te richten (waarbij we een nieuwe map voor elke export maken).

- Zodra een export is voltooid, worden er nooit opnieuw items naar die map geëxporteerd, omdat volgende export naar dezelfde container zich in een nieuw gemaakte map bevindt.


## <a name="settings-and-parameters"></a>Instellingen en para meters

### <a name="headers"></a>Kopteksten
Er zijn twee vereiste header parameters die moeten worden ingesteld voor $export-taken. De waarden worden gedefinieerd door de huidige [$export-specificatie](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers).
* **Accept** -Application/fhir + JSON
* Voor **keur** : reageren-async

### <a name="query-parameters"></a>Queryparameters
De Azure API voor FHIR ondersteunt de volgende query parameters. Al deze para meters zijn optioneel:

|Queryparameter        | Gedefinieerd door de FHIR spec?    |  Beschrijving|
|------------------------|---|------------|
| \_Output | Ja | Ondersteunt momenteel drie waarden om uit te lijnen op de FHIR spec: Application/FHIR + ndjson, Application/ndjson of net ndjson. Alle export taken worden geretourneerd `ndjson` en de door gegeven waarde heeft geen effect op het gedrag van de code. |
| \_moment | Ja | Hiermee kunt u alleen resources exporteren die zijn gewijzigd sinds de gegeven tijd |
| \_voert | Ja | Hiermee kunt u opgeven welke typen resources worden opgenomen. \_Typ bijvoorbeeld = patiënt retourneert alleen patiënten-resources|
| \_typefilter | Ja | Als u een filter met een nauw keurig korrel wilt aanvragen, kunt u \_ typefilter gebruiken in combi natie met de \_ type-para meter. De waarde van de para meter _typeFilter is een door komma's gescheiden lijst met FHIR query's waarmee de resultaten verder worden beperkt |
| \_verpakking | Nee |  Hiermee geeft u de container in het geconfigureerde opslag account op waarin de gegevens moeten worden geëxporteerd. Als er een container is opgegeven, worden de gegevens naar die container geëxporteerd in een nieuwe map met de naam. Als de container niet is opgegeven, wordt deze geëxporteerd naar een nieuwe container met de tijds tempel-en taak-ID. |

## <a name="secure-export-to-azure-storage"></a>Beveiligde export naar Azure Storage

De Azure-API voor FHIR ondersteunt een beveiligde export bewerking. Een optie voor het uitvoeren van een beveiligde export is het toestaan van specifieke IP-adressen die zijn gekoppeld aan de Azure API voor FHIR om toegang te krijgen tot het Azure-opslag account. Afhankelijk van of het opslag account zich op dezelfde of een andere locatie bevindt dan de Azure API voor FHIR, zijn de configuraties verschillend.

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Wanneer het Azure-opslag account zich in een andere regio bevindt

Selecteer de Blade netwerken van het Azure-opslag account in de portal. 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="Azure Storage netwerk instellingen." lightbox="media/export-data/storage-networking.png":::
   
Selecteer ' selected Network ' en geef het IP-adres op in het vak **adres bereik** onder het gedeelte Firewall \| Voeg IP-bereiken toe om toegang vanaf internet of uw on-premises netwerken toe te staan. U vindt het IP-adres uit de onderstaande tabel voor de Azure-regio waar de Azure API voor de FHIR-service is ingericht.

|**Azure-regio**         |**Openbaar IP-adres** |
|:----------------------|:-------------------|
| Australië - oost       | 20.53.44.80       |
| Canada - midden       | 20.48.192.84      |
| VS - centraal           | 52.182.208.31     |
| VS - oost              | 20.62.128.148     |
| VS - oost 2            | 20.49.102.228     |
| VS-Oost 2 EUAP       | 20.39.26.254      |
| Duitsland - noord        | 51.116.51.33      |
| Duitsland - west-centraal | 51.116.146.216    |
| Japan - oost           | 20.191.160.26     |
| Korea - centraal        | 20.41.69.51       |
| VS - noord-centraal     | 20.49.114.188     |
| Europa - noord         | 52.146.131.52     |
| Zuid-Afrika - noord   | 102.133.220.197   |
| VS - zuid-centraal     | 13.73.254.220     |
| Azië - zuidoost       | 23.98.108.42      |
| Zwitserland - noord    | 51.107.60.95      |
| Verenigd Koninkrijk Zuid             | 51.104.30.170     |
| Verenigd Koninkrijk West              | 51.137.164.94     |
| VS - west-centraal      | 52.150.156.44     |
| Europa -west          | 20.61.98.66       |
| VS - west 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Wanneer het Azure-opslag account zich in dezelfde regio bevindt

Het configuratie proces is hetzelfde als hierboven, behalve een specifiek IP-adres bereik in CIDR-indeling wordt gebruikt in plaats daarvan, 100.64.0.0/10. De reden waarom het IP-adres bereik, dat 100.64.0.0 – 100.127.255.255 bevat, moet worden opgegeven, is omdat het daad werkelijke IP-adres dat door de service wordt gebruikt, varieert, maar binnen het bereik valt voor elke $export aanvraag.

> [!Note] 
> Het is mogelijk dat in plaats daarvan een privé-IP-adres binnen het bereik van 10.0.2.0/24 kan worden gebruikt. In dat geval wordt de $export bewerking niet voltooid. U kunt de $export aanvraag opnieuw proberen, maar er is geen garantie dat een IP-adres binnen het bereik van 100.64.0.0/10 de volgende keer wordt gebruikt. Dat is het bekende netwerk gedrag per ontwerp. Het alternatief is het configureren van het opslag account in een andere regio.
    
## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u FHIR-resources kunt exporteren met behulp van $export opdracht. Meer informatie over het exporteren van de gegevens die u niet hebt geïdentificeerd:
 
>[!div class="nextstepaction"]
>[De geïdentificeerde gegevens exporteren](de-identified-export.md)

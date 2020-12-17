---
title: Batch processing Kit voor spraak containers
titleSuffix: Azure Cognitive Services
description: Gebruik de batch processing Kit om spraak container aanvragen te schalen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: aahi
ms.openlocfilehash: cc6bcef77ca1601b76468586aa6af202836f1438
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631989"
---
# <a name="batch-processing-kit-for-speech-containers"></a>Batch processing Kit voor spraak containers

Gebruik de batch processing Kit om werk belastingen op spraak containers aan te vullen en uit te schalen. Dit open source-hulp programma is beschikbaar als een container en helpt batch transcriptie te vereenvoudigen voor een groot aantal audio bestanden, in een wille keurige set on-premises en cloud-gebaseerde spraak container-eind punten. 

:::image type="content" source="media/containers/general-diagram.png" alt-text="Een diagram met een voor beeld van een werk stroom voor een batch Kit-container.":::

De batch Kit-container is gratis beschikbaar op [github](https://github.com/microsoft/batch-processing-kit) en   [docker hub](https://hub.docker.com/r/batchkit/speech-batch-kit/tags). Er worden alleen [kosten in rekening gebracht](speech-container-howto.md#billing) voor de spraak containers die u gebruikt.

| Functie  | Beschrijving  |
|---------|---------|
| Batch-audio bestands distributie     | Verzend grote aantallen bestanden automatisch naar on-premises of cloud-gebaseerde spraak container-eind punten. Bestanden kunnen zich op elk POSIX-compatibel volume bevonden, inclusief netwerk bestandssysteem.       |
| Speech SDK-integratie | Geef algemene vlaggen door aan de Speech SDK, waaronder: n-best hypo theses, diarization, taal, Grove maskers.  |
|Uitvoerings modi     | Voer de batch-client eenmaal uit, doorlopend op de achtergrond of maak HTTP-eind punten voor audio bestanden.         |
| Fouttolerantie | Automatisch opnieuw proberen en door gaan met transcriptie zonder de voortgang te verliezen en onderscheid te maken tussen de fouten en kan niet opnieuw worden uitgevoerd. |
| Beschikbaarheids detectie van eind punten | Als een eind punt niet beschikbaar is, wordt door de batch-client getranscribeerd met andere container eindpunten. Nadat de client weer beschikbaar is, wordt het eind punt automatisch gebruikt.   |
| Endpoint Hot-swaping | Het toevoegen, verwijderen of wijzigen van een spraak container-eind punt tijdens runtime zonder de batch voortgang te onderbreken. Updates zijn direct. |
| Real-time logboek registratie | Real-time logboek registratie van aanvragen voor pogingen, tijds tempels en fout redenen, met spraak-SDK-logboek bestanden voor elk audio bestand. |

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie ophalen met `docker pull`

Gebruik de opdracht [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) om de meest recente batch Kit-container te downloaden.

```bash
docker pull docker.io/batchkit/speech-batch-kit:latest
```

## <a name="endpoint-configuration"></a>Eindpunt configuratie

De batch-client gebruikt een yaml-configuratie bestand dat de on-premises container-eind punten opgeeft. In het volgende voor beeld kan worden geschreven naar `/mnt/my_nfs/config.yaml` , die wordt gebruikt in de onderstaande voor beelden. 

```yaml
MyContainer1: 
  concurrency: 5 
  host: 192.168.0.100 
  port: 5000 
  rtf: 3 
MyContainer2: 
  concurrency: 5 
  host: BatchVM0.corp.redmond.microsoft.com 
  port: 5000 
  rtf: 2 
MyContainer3: 
  concurrency: 10 
  host: localhost 
  port: 6001 
  rtf: 4 
```

In dit yaml-voor beeld worden drie spraak containers op drie hosts opgegeven. De eerste host wordt opgegeven met een IPv4-adres, de tweede wordt uitgevoerd op dezelfde VM als de batch-client en de derde container wordt opgegeven door de DNS-hostnaam van een andere virtuele machine. `concurrency`Met de waarde wordt het maximale aantal gelijktijdige bestands transcripties opgegeven dat in dezelfde container kan worden uitgevoerd. De `rtf` waarde (realtime factor) is optioneel en kan worden gebruikt voor het afstemmen van de prestaties.
 
De batch-client kan dynamisch detecteren of een eind punt niet beschikbaar is (bijvoorbeeld door het opnieuw opstarten van de container of door een netwerk probleem) en wanneer het weer beschikbaar wordt. Transcriptie-aanvragen worden niet verzonden naar containers die niet beschikbaar zijn. de client blijft andere beschik bare containers gebruiken. U kunt eind punten op elk gewenst moment toevoegen, verwijderen of bewerken zonder de voortgang van de batch te onderbreken.


## <a name="run-the-batch-processing-container"></a>De batch verwerkings container uitvoeren
  
> [!NOTE] 
> * In dit voor beeld wordt dezelfde directory ( `/my_nfs` ) gebruikt voor het configuratie bestand en de invoer, uitvoer en Logboeken. U kunt voor deze mappen gehoste of NBS-gekoppelde directory's gebruiken.
> * Als de client wordt uitgevoerd met, `–h` worden de beschik bare opdracht regel parameters en de bijbehorende standaard waarden weer geven. 
> * De batch verwerkings container wordt alleen ondersteund in Linux.

Gebruik de opdracht docker `run` om de container te starten. Hiermee start u een interactieve shell in de container.

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs --entrypoint /bin/bash /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest
```

De batch-client uitvoeren:  

```Docker
run-batch-client -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```

De batch-client en-container uitvoeren in één opdracht:

```Docker
docker run --rm -ti -v  /mnt/my_nfs:/my_nfs docker.io/batchkit/speech-batch-kit:latest  -config /my_nfs/config.yaml -input_folder /my_nfs/audio_files -output_folder /my_nfs/transcriptions -log_folder  /my_nfs/logs -file_log_level DEBUG -nbest 1 -m ONESHOT -diarization  None -language en-US -strict_config   
```


De client wordt gestart. Als een audio bestand tijdens een vorige uitvoering al is getranscribeerd, wordt het bestand automatisch door de client overs Laan. Bestanden worden verzonden met een automatische nieuwe poging als er tijdelijke fouten optreden en u kunt onderscheid maken tussen de fouten die u wilt voor de client om opnieuw te proberen. Bij een transcriptie-fout gaat de client verder met transcriptie en kan het opnieuw worden geprobeerd zonder de voortgang te verliezen.  

## <a name="run-modes"></a>Uitvoerings modi 

De batch verwerkings pakket biedt drie modi, met behulp van de `--run-mode` para meter.

#### <a name="oneshot"></a>[Oneshot](#tab/oneshot)

`ONESHOT` modus transcribeert één batch met audio bestanden (van een invoer Directory en een lijst met optionele bestanden) naar een uitvoermap.

:::image type="content" source="media/containers/batch-oneshot-mode.png" alt-text="Een diagram van de batch Kit container verwerkings bestanden in de modus OneShot.":::

1. Definieer de eind punten van de spraak container die de batch-client in het bestand zal gebruiken `config.yaml` . 
2. Plaats audio bestanden voor transcriptie in een invoer Directory.  
3. Roep de container aan op de Directory, die begint met de verwerking van de bestanden. Als het audio bestand al is getranscribeerd in een vorige run met dezelfde uitvoermap (dezelfde bestands naam en controlesom), wordt het bestand door de client overs Laan. 
4. De bestanden worden uit stap 1 naar de container eindpunten verzonden.
5. Logboeken en de uitvoer van de spraak container worden geretourneerd naar de opgegeven uitvoermap. 

#### <a name="daemon"></a>[Daemon](#tab/daemon)

> [!TIP]
> Als er tegelijkertijd meerdere bestanden aan de invoer Directory worden toegevoegd, kunt u de prestaties verbeteren door ze in een regel matig interval toe te voegen.

`DAEMON` in de modus worden bestaande bestanden in een bepaalde map getranscribeerd en worden er voortdurend nieuwe audio bestanden getranscribeerd wanneer ze worden toegevoegd.          

:::image type="content" source="media/containers/batch-daemon-mode.png" alt-text="Een diagram waarin batch-Kit container verwerkings bestanden in de daemon-modus worden verwerkt.":::

1. Definieer de eind punten van de spraak container die de batch-client in het bestand zal gebruiken `config.yaml` . 
2. Roep de container aan in een invoer Directory. De batch-client begint met het controleren van de map op binnenkomende bestanden. 
3. Stel doorlopend afspelen van audio bestanden in de invoer Directory in. Als het audio bestand al is getranscribeerd in een vorige run met dezelfde uitvoermap (dezelfde bestands naam en controlesom), wordt het bestand door de client overs Laan. 
4. Zodra een bestand schrijven of POSIX-signaal wordt gedetecteerd, wordt de container geactiveerd om te reageren.
5. De bestanden worden uit stap 1 naar de container eindpunten verzonden.
6. Logboeken en de uitvoer van de spraak container worden geretourneerd naar de opgegeven uitvoermap. 

#### <a name="rest"></a>[REST](#tab/rest)

`REST` modus is een API-server modus die een eenvoudige set HTTP-eind punten biedt voor het verzenden van audio bestanden, het controleren van statussen en lange polling. Maakt ook programmatisch gebruik mogelijk met behulp van een module-extensie van python of als een submodule importeren.

:::image type="content" source="media/containers/batch-rest-api-mode.png" alt-text="Een diagram van de batch Kit container verwerkings bestanden in rust modus.":::

1. Definieer de eind punten van de spraak container die de batch-client in het bestand zal gebruiken `config.yaml` . 
2. Verzend een aanvraag voor een HTTP-aanvraag naar een van de eind punten van de API-server. 
        
    |Eindpunt  |Beschrijving  |
    |---------|---------|
    |`/submit`     | Eind punt voor het maken van nieuwe batch-aanvragen.        |
    |`/status`     | Eind punt voor het controleren van de status van een batch-aanvraag. De verbinding blijft open totdat de batch is voltooid.       |
    |`/watch`     | Eind punt voor het gebruik van HTTP-lange polling totdat de batch is voltooid.        |

3. Audio bestanden worden geüpload vanuit de invoer Directory. Als het audio bestand al is getranscribeerd in een vorige run met dezelfde uitvoermap (dezelfde bestands naam en controlesom), wordt het bestand door de client overs Laan. 
4. Als een aanvraag naar het `/submit` eind punt wordt verzonden, worden de bestanden uit stap 1 naar de container eindpunten gezonden.
5. Logboeken en de uitvoer van de spraak container worden geretourneerd naar de opgegeven uitvoermap. 

---

## <a name="logging"></a>Logboekregistratie

> [!NOTE]
> De batch-client kan het bestand *Run. log* periodiek overschrijven als het te groot wordt.

De client maakt een *Run. log* -bestand in de map die is opgegeven door het `-log_folder` argument in de docker- `run` opdracht. Logboeken worden standaard vastgelegd op het niveau van de fout opsporing. Dezelfde logboeken worden verzonden naar `stdout/stderr` en gefilterd op basis van de `-file_log_level` `console_log_level` argumenten of. Dit logboek is alleen nodig voor fout opsporing of als u een tracering wilt verzenden voor ondersteuning. De map logboek registratie bevat ook de spraak-SDK-logboeken voor elk audio bestand.

De opgegeven uitvoermap `-output_folder` bevat een *run_summary.jsin* het   bestand, dat periodiek elke 30 seconden wordt herschreven of wanneer nieuwe transcripties zijn voltooid. U kunt dit bestand gebruiken om de voortgang van de batch te controleren. Het bevat ook de laatste uitvoerings statistieken en de uiteindelijke status van elk bestand wanneer de batch is voltooid. De batch is voltooid wanneer het proces een schone afsluiting heeft. 

## <a name="next-steps"></a>Volgende stappen

* [Containers installeren en uitvoeren](speech-container-howto.md)

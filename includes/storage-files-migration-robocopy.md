---
title: bestand opnemen
description: bestand opnemen
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 01512f0e37761915fcb6eeba8c162e1a3db62c48
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491666"
---
```console
Robocopy /MT:16 /R:5 /W:5 /B /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Switch                | Betekenis |
|-----------------------|---------|
| `/MT:n`               | Hiermee kan RoboCopy meerdere threads uitvoeren. Standaard waarde voor n = 8 en de maximum waarde is 128 threads. Deze waarde moet overeenkomen met het aantal processor kernen en het aantal threads per kern. Bedenk of kernen moeten worden gereserveerd voor andere taken die een productie server kan hebben. |
| `/R:n`                | De snelheid van een RoboCopy-uitvoering kan worden verbeterd door een maximum aantal nieuwe pogingen op te geven voor een bestand dat bij de eerste poging niet kan worden gekopieerd. n = het maximum aantal nieuwe pogingen voordat het bestand definitief kan worden gekopieerd in deze RoboCopy-uitvoering. Deze switch werkt het beste in scenario's waarin het al duidelijk is dat er meer RoboCopy-uitvoeringen zijn. Als het bestand in deze uitvoering niet kan worden gekopieerd, wordt de volgende RoboCopy-taak opnieuw geprobeerd. Vaak worden bestanden die zijn mislukt omdat ze in gebruik waren of vanwege time-outproblemen, mogelijk uiteindelijk met deze aanpak gekopieerd. |
| `/W:n`                | Met deze schakel optie geeft u op hoelang de tijd moet worden gewacht voordat wordt geprobeerd een bestand te kopiëren dat bij de laatste poging niet is gekopieerd. n = het aantal seconden dat moet worden gewacht tussen nieuwe pogingen. `/W:n` wordt vaak gebruikt in combi natie met `/R:n` . |
| `/B`                  | Voert RoboCopy uit in dezelfde modus als een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waarnaar de huidige gebruiker geen machtigingen heeft. |
| `/MIR`                | Met een *Mirror-bron naar het doel* kunnen Robocopy alleen verschillen tussen de bron en het doel kopiëren. Lege submappen worden gekopieerd. Items (bestanden of mappen) die zijn gewijzigd of niet bestaan in het doel, worden gekopieerd. Items die voor komen op het doel, maar niet op de bron, worden verwijderd uit het doel. Wanneer u deze switch gebruikt, is het van cruciaal belang dat u precies overeenkomt met de structuur van de bron-en doel mappen. "Matching" betekent dat u kopieert van het juiste bron-en mapniveau naar het overeenkomstige mapniveau op het doel. Alleen dan kan een ' up ' kopie worden gemaakt. Wanneer de bron en het doel niet overeenkomen, wordt met behulp van een `/MIR` grote schaal verwijderd en worden de kopieën opnieuw gekopieerd. |
| `/IT`                 | Zorgt dat de betrouw baarheid in bepaalde spiegel scenario's blijft behouden. </br>*Voor beeld* : als er tussen twee Robocopy een bestand wordt uitgevoerd met een ACL-wijziging en een kenmerk update: bijvoorbeeld is dit gemarkeerd als *verborgen*. Zonder/IT kan de ACL-wijziging worden gemist door RoboCopy en niet worden overgedragen naar de doel locatie. |
|`/COPY:[copyflags]`    | Betrouw baarheid van de bestands kopie (standaard als niet opgegeven is `/COPY:DAT` ), kopieer vlaggen: `D` = gegevens, `A` = kenmerken, `T` = Time Stamps, `S` = beveiliging = NTFS acl's, `O` = eigenaargegevens, `U` = een<u>u</u>ATH-informatie. Controle gegevens kunnen niet worden opgeslagen in een Azure-bestands share. |
| `/DCOPY:[copyflags]`  | Betrouw baarheid voor het kopiëren van mappen (standaard als niet opgegeven is `/DCOPY:DA` ), kopieer vlaggen: `D` = gegevens, `A` = kenmerken, `T` = Time Stamps. |
| `/NP`                 | De voortgang van de kopie voor elk bestand en elke map wordt niet weer gegeven. Als u de voortgang weergeeft, worden de Kopieer prestaties aanzienlijk verminderd. |
| `/NFL`                | Hiermee geeft u op dat bestandsnamen niet moeten worden vastgelegd. Verbetert de Kopieer prestaties. |
| `/NDL`                | Hiermee geeft u op dat mapnamen niet moeten worden vastgelegd. Verbetert de Kopieer prestaties. |
| `/UNILOG:<file name>` | Hiermee wordt de status van de uitvoer naar een logboek bestand opgeslagen als UNICODE (het bestaande logboek wordt overschreven). |
| `/LFSM`               | **alleen voor doelen met gelaagde opslag** </br>Het gebruik van/LFSM vraagt RoboCopy om in de modus ' laag vrije ruimte ' te werken. Deze schakel optie is alleen nuttig voor doelen met gelaagde opslag, die mogelijk geen lokale capaciteit meer heeft voordat RoboCopy kan worden voltooid. Deze schakel optie is specifiek toegevoegd voor gebruik met een Azure File Sync ingeschakeld doel voor Cloud lagen. Dit kan onafhankelijk van Azure File Sync worden gebruikt. In deze modus wordt RoboCopy onderbroken wanneer een bestand wordt gekopieerd, waardoor de beschik bare ruimte van het bestemmings volume onder de waarde ' Floor ' zou gaan. Deze waarde kan worden opgegeven in de `/LFSM:n` vorm van de vlag. De para meter `n` is opgegeven in base 2: `nKB` , `nMB` of `nGB` . Als `/LFSM` is opgegeven zonder expliciete vloer waarde, wordt de vloer ingesteld op 10 procent van de grootte van het doel volume. De modus voor weinig vrije ruimte is niet compatibel met/MT,/EFSRAW,/B en/ZB. |
| `/Z`                  | **zorgvuldig gebruik** </br>Kopieert bestanden in de modus voor opnieuw opstarten. gebruik wordt alleen aanbevolen in een onstabiele netwerk omgeving. Met deze optie worden de Kopieer prestaties aanzienlijk verminderd door extra logboek registratie. |
| `/ZB`                 | **zorgvuldig gebruik** </br>Maakt gebruik van de modus voor opnieuw opstarten. Als de toegang wordt geweigerd, wordt met deze optie de back-upmodus gebruikt. Met deze optie worden de Kopieer prestaties aanzienlijk verminderd door controle punten. |
   
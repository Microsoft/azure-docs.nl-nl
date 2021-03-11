---
title: Een mapstructuur toewijzen aan een Azure File Sync-topologie
description: Wijs een bestaande bestands-en mapstructuur toe aan Azure-bestands shares voor gebruik met Azure File Sync. Een gemeen schappelijk tekst blok, gedeeld via migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: cde85e245c8cc6ae8c55b24270f125bacc111737
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547544"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Switch              | Betekenis |
|---------------------|---------|
| /MT                 | Hiermee kan RoboCopy meerdere threads uitvoeren. De standaard waarde is 8 en de maximum waarde is 128. Deze waarde moet overeenkomen met het aantal processor kernen en het aantal threads per kern. Bedenk of kernen moeten worden gereserveerd voor andere taken die een productie server kan hebben. |
| /NP                 | De voortgang van de kopie voor elk bestand en elke map wordt niet weer gegeven. Als u de voortgang weergeeft, worden de Kopieer prestaties aanzienlijk verminderd. |
| /NFL                | Hiermee geeft u op dat bestandsnamen niet moeten worden vastgelegd. Verbetert de Kopieer prestaties. |
| /NDL                | Hiermee geeft u op dat mapnamen niet moeten worden vastgelegd. Verbetert de Kopieer prestaties. |
| /B                  | Voert RoboCopy uit in dezelfde modus als een back-uptoepassing zou gebruiken. Hiermee kan RoboCopy bestanden verplaatsen waarnaar de huidige gebruiker geen machtigingen heeft. |
| /MIR                | Met een *Mirror-bron naar het doel* kunnen Robocopy alleen verschillen tussen de bron en het doel kopiëren. Lege submappen worden gekopieerd. Items (bestanden of mappen) die zijn gewijzigd of niet bestaan in het doel, worden gekopieerd. Items die voor komen op het doel, maar niet op de bron, worden verwijderd uit het doel. Wanneer u deze switch gebruikt, is het van cruciaal belang dat u precies overeenkomt met de structuur van de bron-en doel mappen. "Matching" betekent dat u kopieert van het juiste bron-en mapniveau naar het overeenkomstige mapniveau op het doel. Alleen dan kan een ' up ' kopie worden gemaakt. Wanneer de bron en het doel niet overeenkomen, wordt met behulp van een `/MIR` grote schaal verwijderd en worden de kopieën opnieuw gekopieerd. |
| /IT                 | Zorgt dat de betrouw baarheid in bepaalde spiegel scenario's blijft behouden. </br>*Voor beeld* : als er tussen twee Robocopy een bestand wordt uitgevoerd met een ACL-wijziging en een kenmerk update: bijvoorbeeld is dit gemarkeerd als *verborgen*. Zonder/IT kan de ACL-wijziging worden gemist door RoboCopy en niet worden overgedragen naar de doel locatie. |
|Kopieer`[copyflags]`  | Betrouw baarheid van de bestands kopie (standaard als niet opgegeven is `/COPY:DAT` ), kopieer vlaggen: `D` = gegevens, `A` = kenmerken, `T` = Time Stamps, `S` = beveiliging = NTFS acl's, `O` = eigenaargegevens, `U` = een<u>u</u>ATH-informatie. Controle gegevens kunnen niet worden opgeslagen in een Azure-bestands share. |
| /DCOPY:`[copyflags]`| Betrouw baarheid voor het kopiëren van mappen (standaard als niet opgegeven is `/DCOPY:DA` ), kopieer vlaggen: `D` = gegevens, `A` = kenmerken, `T` = Time Stamps. |
| /UNILOG:<file name> | Hiermee wordt de status van de uitvoer naar een logboek bestand opgeslagen als UNICODE (het bestaande logboek wordt overschreven). |
| /LFSM               | **alleen voor doelen met gelaagde opslag** </br>Het gebruik van/LFSM vraagt RoboCopy om in de modus ' laag vrije ruimte ' te werken. Deze schakel optie is alleen nuttig voor doelen met gelaagde opslag, die mogelijk geen lokale capaciteit meer heeft voordat RoboCopy kan worden voltooid. Deze schakel optie is specifiek toegevoegd voor gebruik met een Azure File Sync ingeschakeld doel voor Cloud lagen. Dit kan onafhankelijk van Azure File Sync worden gebruikt. In deze modus wordt RoboCopy onderbroken wanneer een bestand wordt gekopieerd, waardoor de beschik bare ruimte van het bestemmings volume onder de waarde ' Floor ' zou gaan. Deze waarde kan worden opgegeven in de `/LFSM:n` vorm van de vlag. De para meter `n` is opgegeven in base 2: `nKB` , `nMB` of `nGB` . Als `/LFSM` is opgegeven zonder expliciete vloer waarde, wordt de vloer ingesteld op 10 procent van de grootte van het doel volume. De modus voor weinig vrije ruimte is niet compatibel met/MT,/EFSRAW,/B en/ZB. |
| /Z                  | **zorgvuldig gebruik** </br>Kopieert bestanden in de modus voor opnieuw opstarten. gebruik wordt alleen aanbevolen in een onstabiele netwerk omgeving. Met deze optie worden de Kopieer prestaties aanzienlijk verminderd door extra logboek registratie. |
| /ZB                 | **zorgvuldig gebruik** </br>Maakt gebruik van de modus voor opnieuw opstarten. Als de toegang wordt geweigerd, wordt met deze optie de back-upmodus gebruikt. Met deze optie worden de Kopieer prestaties aanzienlijk verminderd door controle punten. |
   
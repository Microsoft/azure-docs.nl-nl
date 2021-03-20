---
title: Query tijd optimaliseren door gebruik te maken van de strategie voor het opslaan van de pop-uptabel in Azure Database for PostgreSQL-één server
description: In dit artikel wordt beschreven hoe u de query tijd optimaliseert met de strategie voor het opslaan van de pop-uptabel op een Azure Database for PostgreSQL-één server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86116179"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Query tijd optimaliseren met de opslag strategie van de pop-uptabel 
In dit artikel wordt beschreven hoe u de query tijden optimaliseert met de opslag techniek voor het beperken van kenmerken van de pop-up.

## <a name="toast-table-storage-strategies"></a>Strategie voor opslag van pop-uptabelen
Er worden vier verschillende strategieën gebruikt voor het opslaan van kolommen op een schijf die gebruik kan maken van de pop-up. Ze vertegenwoordigen verschillende combi Naties tussen compressie en out-of-line-opslag. De strategie kan worden ingesteld op het niveau van het gegevens type en op kolom niveau.
- **Plain** voor komt compressie of out-of-line opslag. Hiermee wordt het gebruik van single-byte headers voor varlena-typen uitgeschakeld. Alleen-lezen is de enige mogelijke strategie voor kolommen met gegevens typen die niet kunnen worden gebruikt voor de pop-up.
- Met **Extended** kunnen zowel compressie als out-of-line Storage worden uitgevoerd. Uitgebreid is de standaard waarde voor de meeste gegevens typen waarvoor pop-up kan worden gebruikt. Er wordt eerst een compressie poging uitgevoerd. Out-of-line Storage wordt geprobeerd als de rij nog te groot is.
- **Extern** maakt out-of-line opslag, maar geen compressie mogelijk. Het gebruik van extern maakt subtekenreeks-bewerkingen sneller op brede tekst en byte-a-kolommen. Deze snelheid wordt geleverd met de boete van meer opslag ruimte. Deze bewerkingen worden geoptimaliseerd om alleen de vereiste onderdelen van de out-of-line-waarde op te halen wanneer deze niet zijn gecomprimeerd.
- Met **Main** kan compressie worden uitgevoerd, maar dit is geen out-of-line opslag. Out-of-line-opslag wordt nog steeds uitgevoerd voor dergelijke kolommen, maar alleen als een laatste redmiddel. Deze fout treedt op als er geen andere manier is om de rij klein genoeg te maken voor een pagina.

## <a name="use-toast-table-storage-strategies"></a>Opslag strategieën voor de pop-up tabel gebruiken
Als uw query's toegang hebben tot gegevens typen die gebruik kunnen maken van de pop-up, kunt u overwegen de hoofd strategie te gebruiken in plaats van de standaard optie Extended om query tijden te verminderen. Geen regel voor out-of-line-opslag. Als uw query's geen toegang krijgen tot gegevens typen die pop-up kunnen gebruiken, is het wellicht handig om de uitgebreide optie te hand haven. Een groter deel van de rijen van de hoofd tabel past in de gedeelde buffer cache, waardoor de prestaties worden verbeterd.

Als u een werk belasting hebt die gebruikmaakt van een schema met brede tabellen en hoge teken aantallen, overweeg dan om PostgreSQL-pop-uptabels te gebruiken. Een voor beeld van een klanten tabel had meer dan 350 kolommen met meerdere kolommen met een bereik van 255 tekens. Nadat deze is geconverteerd naar de hoofd strategie van de pop-uptabel, wordt de Bench Mark-query tijd gereduceerd van 4203 seconden naar 467 seconden. Dat is een verbetering van 89 procent.

## <a name="next-steps"></a>Volgende stappen
Controleer de werk belasting op de vorige kenmerken. 

Raadpleeg de volgende PostgreSQL-documentatie: 
- [Hoofd stuk 68, fysieke opslag in de data base](https://www.postgresql.org/docs/current/storage-toast.html) 
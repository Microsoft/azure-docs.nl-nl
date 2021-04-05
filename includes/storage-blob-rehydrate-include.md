---
title: bestand opnemen
description: bestand opnemen
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: a369eb7000fb8622a69f4205ffcc232ae9c9d242
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95545925"
---
Als u gegevens wilt lezen die aanwezig zijn in Archive Storage, moet u de laag van de blob eerst wijzigen in Hot of Cool. Dit proces wordt ook wel rehydratatie genoemd en kan uren duren om te volt ooien. We raden grote BLOB-grootten aan voor optimale rehydratatie-prestaties. Enkele kleine blobs tegelijk reactiveren kan extra tijd toevoegen. Er zijn momenteel twee herhydrate prioriteiten, hoog en standaard, die kunnen worden ingesteld via de optionele *x-MS-autohydrat-Priority-* eigenschap op een [set BLOB-laag](/rest/api/storageservices/set-blob-tier) of een BLOB-bewerking [kopiëren](/rest/api/storageservices/copy-blob) .

* **Standaard prioriteit**: de rehydratatie-aanvraag wordt verwerkt in de volg orde waarin deze is ontvangen en kan Maxi maal 15 uur duren.
* **Hoge prioriteit**: de rehydratatie-aanvraag krijgt de prioriteit van standaard aanvragen en kan in minder dan 1 uur eindigen voor objecten onder een grootte van 10 GB. 

> [!NOTE]
> Standaard prioriteit is de standaard optie voor rehydratatie voor archiveren. Hoge prioriteit is een snellere optie waarmee meer dan de standaard prioriteit rehydratatie wordt berekend en meestal wordt gereserveerd voor gebruik in situaties waarin nood herstel van gegevens wordt uitgevoerd.
>
> Hoge prioriteit kan langer dan 1 uur duren, afhankelijk van de grootte van de BLOB en de huidige vraag. Aanvragen met een hoge prioriteit worden gegarandeerd met prioriteit voor aanvragen met een standaard prioriteit.

Zodra een rehydratatie-aanvraag wordt gestart, kan deze niet worden geannuleerd. Tijdens het rehydratatie-proces blijft de BLOB-eigenschap *x-MS-Access-tier* zichtbaar als Archive totdat rehydratatie is voltooid naar een online-laag. Als u de status en voortgang van de rehydratatie wilt bevestigen, kunt u de [Get BLOB-eigenschappen](/rest/api/storageservices/get-blob-properties) aanroepen om de *x-MS-Archive-status* en de *x-MS-rehydrat-Priority-* eigenschappen te controleren. De status van het archief kan ' rehydrat-pending-to-hot ' of ' rehydrat-pending-to-cool ' lezen, afhankelijk van de bestemmings laag. De prioriteit van de herhydrater geeft de snelheid van ' hoog ' of ' standaard ' aan. Na voltooiing worden de eigenschappen archief status en opnieuw gehydrateerde prioriteit verwijderd en wordt de BLOB-eigenschap van de Access-laag bijgewerkt zodat deze overeenkomt met de geselecteerde warme of koude laag.
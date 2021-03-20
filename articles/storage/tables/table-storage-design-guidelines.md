---
title: Richt lijnen voor Azure Storage-tabel ontwerp | Microsoft Docs
description: Meer informatie over richt lijnen voor het ontwerpen van uw Azure Storage-tabel service ter ondersteuning van lees-en schrijf bewerkingen.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: f84707e454a8b1f5d5947478fe65108a142a9757
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88236315"
---
# <a name="guidelines-for-table-design"></a>Richtlijnen voor tabelontwerp

Het ontwerpen van tabellen voor gebruik met de Azure Storage Table-service wijkt af van het ontwerp van overwegingen voor een relationele data base. In dit artikel worden richt lijnen beschreven voor het ontwerpen van uw Table service-oplossing om efficiënt te kunnen lezen en te schrijven.

## <a name="design-your-table-service-solution-to-be-read-efficient"></a>Uw Table service-oplossing zo ontwerpen dat deze kan worden gelezen

* ***Ontwerpen voor het uitvoeren van query's in lees-en zware toepassingen.*** Wanneer u uw tabellen ontwerpt, moet u nadenken over de query's (met name de latentie gevoelige waarden) die u wilt uitvoeren voordat u denkt dat u uw entiteiten wilt bijwerken. Dit resulteert doorgaans in een efficiënte en beste oplossing.  
* ***Geef zowel PartitionKey als RowKey op in uw query's.** _ _Point query's * zoals dit zijn de meest efficiënte query's voor de tabel service.  
* ***Overweeg dubbele exemplaren van entiteiten op te slaan.*** Table Storage is goedkope, dus overweeg om dezelfde entiteit meerdere keren (met verschillende sleutels) op te slaan om efficiëntere query's mogelijk te maken.  
* ***Overweeg uw gegevens te denormaliseren.*** Table Storage is goedkope manier om uw gegevens te denormaliseren. Zo kunt u bijvoorbeeld overzichts entiteiten opslaan zodat query's voor statistische gegevens alleen toegang hebben tot één entiteit.  
* ***Samengestelde sleutel waarden gebruiken.** _ De enige sleutels die u hebt, zijn _ *PartitionKey** en **RowKey**. Gebruik bijvoorbeeld samengestelde sleutel waarden om alternatieve toegangs paden naar entiteiten in te scha kelen.  
* ***Query projectie gebruiken.*** U kunt de hoeveelheid gegevens die u via het netwerk overdraagt verminderen met behulp van query's waarmee u alleen de gewenste velden selecteert.  

## <a name="design-your-table-service-solution-to-be-write-efficient"></a>Uw Table service-oplossing zo ontwerpen dat deze efficiënt kan worden geschreven  

* ***Maak geen dynamische partities.*** Kies sleutels waarmee u op elk gewenst moment uw aanvragen kunt verspreiden over meerdere partities.  
* ***Vermijd pieken in verkeer.*** Het verkeer gedurende een redelijke periode vloeiend te maken en pieken in het verkeer te voor komen.
* ***Maak niet noodzakelijkerwijs een afzonderlijke tabel voor elk type entiteit.*** Wanneer u atomische trans acties tussen entiteits typen nodig hebt, kunt u deze typen meerdere entiteiten opslaan in dezelfde partitie in dezelfde tabel.
* ***Houd rekening met de maximale door Voer die u moet door nemen.*** U moet rekening houden met de schaalbaarheids doelen voor de Table service en ervoor te zorgen dat uw ontwerp deze niet overschrijdt.  

Als u deze hand leiding leest, ziet u voor beelden waarin al deze principes in de praktijk worden geplaatst. 

## <a name="next-steps"></a>Volgende stappen

- [Tabelontwerppatronen](table-storage-design-patterns.md)
- [Ontwerp voor query's](table-storage-design-for-query.md)
- [Tabel gegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)

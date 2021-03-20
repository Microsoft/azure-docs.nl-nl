---
title: Azure-tabel opslag ontwerpen voor het wijzigen van gegevens | Microsoft Docs
description: Ontwerp tabellen voor het wijzigen van gegevens in azure-tabel opslag. Invoeg-, bijwerk-en verwijder bewerkingen optimaliseren. Zorg ervoor dat uw opgeslagen entiteiten consistent zijn.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 25785bc4b945f469e67f2a71eb6676940e091d56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88236757"
---
# <a name="design-for-data-modification"></a>Ontwerp voor gegevenswijziging
Dit artikel richt zich op de ontwerp overwegingen voor het optimaliseren van invoegingen, updates en verwijderingen. In sommige gevallen moet u de afweging evalueren tussen de ontwerpen die worden geoptimaliseerd voor het uitvoeren van query's op ontwerpen die worden geoptimaliseerd voor het wijzigen van gegevens, net zoals bij ontwerpen voor relationele data bases (hoewel de technieken voor het beheer van de ontwerp traden verschillen in een relationele data base). In de sectie tabel ontwerp patronen worden een aantal gedetailleerde ontwerp patronen voor de Table service beschreven en worden enkele van deze trans acties gemarkeerd. In de praktijk zult u merken dat veel modellen die zijn geoptimaliseerd voor het uitvoeren van query's, ook geschikt zijn voor het wijzigen van entiteiten.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>De prestaties van INSERT-, update-en delete-bewerkingen optimaliseren
Als u een entiteit wilt bijwerken of verwijderen, moet u deze kunnen identificeren met behulp van de waarden **PartitionKey** en **RowKey** . In dit opzicht moeten uw keuze van **PartitionKey** en **RowKey** voor het wijzigen van entiteiten vergelijk bare criteria volgen op uw keuze om punt query's te ondersteunen, omdat u zo efficiënt mogelijk entiteiten wilt identificeren. U wilt geen inefficiënte partitie of tabel Scan gebruiken om een entiteit te vinden om de **PartitionKey** -en **RowKey** -waarden te ontdekken die u moet bijwerken of verwijderen.  

De volgende patronen in het ontwerp patroon van de sectie tabel maken het optimaliseren van de prestaties of uw insert-, update-en delete-bewerkingen:  

* [Patroon voor grote volumes verwijderen](table-storage-design-patterns.md#high-volume-delete-pattern) : Hiermee wordt het verwijderen van een groot aantal entiteiten ingeschakeld door alle entiteiten voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel op te slaan. u verwijdert de entiteiten door de tabel te verwijderen.  
* [Patroon van de gegevens reeks](table-storage-design-patterns.md#data-series-pattern) : Sla volledige gegevens reeksen op in één entiteit om het aantal aanvragen dat u maakt, te minimaliseren.  
* [Patroon voor grote entiteiten](table-storage-design-patterns.md#wide-entities-pattern) : gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  
* [Patroon voor grote entiteiten](table-storage-design-patterns.md#large-entities-pattern) : gebruik Blob Storage om grote eigenschaps waarden op te slaan.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Consistentie in uw opgeslagen entiteiten garanderen
De andere sleutel factor die van invloed is op uw keuze van sleutels voor het optimaliseren van gegevens wijzigingen is het garanderen van consistentie met behulp van atomische trans acties. U kunt alleen een EGT gebruiken om te werken aan entiteiten die zijn opgeslagen in dezelfde partitie.  

De volgende patronen in het artikel [tabel ontwerp patronen](table-storage-design-patterns.md) adres consistentie beheer:  

* [Intra-Partition secundair index patroon](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende **RowKey** -waarden (in dezelfde partitie) om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden.  
* [Secundair index patroon tussen partities](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende RowKey-waarden in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden.  
* [Patroon uiteindelijk consistent trans acties](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) : Schakel uiteindelijk consistent gedrag in voor de partitie grenzen of de grenzen van het opslag systeem met behulp van Azure-wacht rijen.
* [Patroon van index entiteiten](table-storage-design-patterns.md#index-entities-pattern) : behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  
* [Denormalisatie patroon](table-storage-design-patterns.md#denormalization-pattern) : gerelateerde gegevens samen in één entiteit combi neren zodat u alle gegevens kunt ophalen die u nodig hebt met één punt query.  
* [Patroon van de gegevens reeks](table-storage-design-patterns.md#data-series-pattern) : Sla volledige gegevens reeksen op in één entiteit om het aantal aanvragen dat u maakt, te minimaliseren.  

Zie de sectie [trans acties voor entiteits](table-storage-design.md#entity-group-transactions)groepen voor meer informatie over entiteits transacties.  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zorg ervoor dat uw ontwerp voor efficiënte wijzigingen efficiënte query's vereenvoudigt
In veel gevallen kan een ontwerp voor efficiënte query resultaten leiden tot efficiënte wijzigingen, maar moet u altijd evalueren of dit het geval is voor uw specifieke scenario. Sommige van de patronen in de [tabel ontwerp patronen](table-storage-design-patterns.md) van het artikel evalueren expliciet de verhoudingen van het opvragen en aanpassen van entiteiten, en u moet altijd rekening houden met het aantal van elk type bewerking.  

De volgende patronen in de [tabel met ontwerp patronen](table-storage-design-patterns.md) voor het artikel zijn een afweging tussen het ontwerpen van efficiënte query's en het ontwerpen voor efficiënte gegevens aanpassing:  

* [Samengesteld sleutel patroon](table-storage-design-patterns.md#compound-key-pattern) : gebruik samengestelde **RowKey** -waarden om een client in staat te stellen gerelateerde gegevens te zoeken met een single point-query.  
* [Eind patroon van logboek](table-storage-design-patterns.md#log-tail-pattern) : Haal de *n* -entiteiten op die het laatst aan een partitie zijn toegevoegd met behulp van een **RowKey** -waarde die in omgekeerde datum-en tijd volgorde wordt gesorteerd.  

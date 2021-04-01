---
title: Azure-tabel opslag ontwerpen voor query's | Microsoft Docs
description: Ontwerp tabellen voor query's in azure-tabel opslag. Kies een geschikte partitie sleutel, Optimaliseer query's en sorteer gegevens voor de Table service.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 43ae21d97bc9d8292270ae62006e649f4bcf540b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93316150"
---
# <a name="design-for-querying"></a>Ontwerp voor query's
Table service oplossingen kunnen intensieve, schrijf intensief of een combi natie van beide worden gelezen. In dit artikel vindt u informatie over de zaken die u moet overwegen wanneer u uw Table service ontwerpt om Lees bewerkingen efficiënt te ondersteunen. Normaal gesp roken is een ontwerp dat lees bewerkingen efficiënt ondersteunt ook efficiënt voor schrijf bewerkingen. Er zijn echter extra aandachtspunten bij het ontwerpen ter ondersteuning van schrijf bewerkingen, zoals beschreven in het artikel [ontwerp voor het wijzigen van gegevens](table-storage-design-for-modification.md).

Een goed uitgangs punt voor het ontwerpen van uw Table service-oplossing om gegevens efficiënt te kunnen lezen is het stellen van ' welke query's moet mijn toepassing worden uitgevoerd om de benodigde gegevens van de Table service op te halen? '  

> [!NOTE]
> Met de Table service is het belang rijk om het ontwerp correct te laten beginnen, omdat het moeilijk en kostbaar is om het later te wijzigen. In een relationele data base is het vaak mogelijk om prestatie problemen op te lossen door indexen toe te voegen aan een bestaande Data Base: dit is geen optie met de Table service.  
> 
> 

Deze sectie richt zich op de belangrijkste problemen die u moet aanpakken wanneer u uw tabellen ontwerpt voor het uitvoeren van query's. De onderwerpen in deze sectie zijn onder andere:

* [Hoe uw keuze van PartitionKey en RowKey gevolgen heeft voor de query prestaties](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Een geschikte PartitionKey kiezen](#choosing-an-appropriate-partitionkey)
* [Query's optimaliseren voor de Table service](#optimizing-queries-for-the-table-service)
* [Gegevens sorteren in de Table service](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hoe uw keuze van PartitionKey en RowKey gevolgen heeft voor de query prestaties
In de volgende voor beelden wordt ervan uitgegaan dat de tabel service werk nemers-entiteiten opslaat met de volgende structuur (in de meeste voor beelden wordt de **Time Stamp** -eigenschap voor duidelijkheid wegge laten):  

| *Kolomnaam* | *Gegevenstype* |
| --- | --- |
| **PartitionKey** (afdelings naam) |Tekenreeks |
| **RowKey** (werk nemer-id) |Tekenreeks |
| **FirstName** |Tekenreeks |
| **LastName** |Tekenreeks |
| **Age** |Geheel getal |
| **EmailAddress** |Tekenreeks |

In het artikel [overzicht van Azure Table Storage](table-storage-overview.md) worden enkele van de belangrijkste functies van de Azure-Table service beschreven die een directe invloed hebben op het ontwerpen van query's. Dit resulteert in de volgende algemene richt lijnen voor het ontwerpen van Table service query's. Houd er rekening mee dat de filter syntaxis die in de onderstaande voor beelden wordt gebruikt, afkomstig is uit de Table service REST API, Zie [query-entiteiten](/rest/api/storageservices/Query-Entities)voor meer informatie.  

* Een *-**punt query** _ is de meest efficiënte zoek opdracht die wordt gebruikt voor het gebruik van grote hoeveel heden lookups of lookups waarvoor de laagste latentie is vereist. Een dergelijke query kan de indexen gebruiken om een afzonderlijke entiteit efficiënt te vinden door de waarden _ *PartitionKey** en **RowKey** op te geven. Bijvoorbeeld: $filter = (PartitionKey EQ ' Sales ') en (RowKey EQ ' 2 ')  
* Second Best is een *-**bereik query** _ die gebruikmaakt van de _ *PartitionKey** en filters voor een bereik van **RowKey** -waarden om meer dan één entiteit te retour neren. De waarde **PartitionKey** identificeert een specifieke partitie en de **RowKey** -waarden identificeren een subset van de entiteiten in die partitie. Bijvoorbeeld: $filter = PartitionKey EQ ' Sales ' en RowKey ge ' en RowKey lt 'T '  
* De derde beste is een *-**partitie scan** _ die gebruikmaakt van de _ *PartitionKey** en filters op een andere niet-sleutel eigenschap en die mogelijk meer dan één entiteit retourneert. De waarde **PartitionKey** identificeert een specifieke partitie en de eigenschapwaarden worden geselecteerd voor een subset van de entiteiten in die partitie. Bijvoorbeeld: $filter = PartitionKey EQ ' Sales ' en LastName EQ ' Smit '  
* Een ***tabel scan** _ bevat niet de _ *PartitionKey** en is zeer inefficiënt omdat hiermee wordt gezocht in alle partities waaruit de tabel bestaat, op zijn beurt voor overeenkomende entiteiten. Er wordt een tabel scan uitgevoerd, ongeacht of het filter gebruikmaakt van de **RowKey**. Bijvoorbeeld: $filter = LastName EQ ' Jansen '  
* Query's waarmee meerdere entiteiten worden geretourneerd, retour neren ze in **PartitionKey** -en **RowKey** -volg orde. Kies een **RowKey** die de meest voorkomende sorteer volgorde definieert om te voor komen dat de entiteiten in de client worden gebruikt.  

Houd er rekening mee dat het gebruik van een '**or**' om een filter op te geven op basis van **RowKey** waarden resulteert in een partitie scan en niet wordt behandeld als een bereik query. U moet daarom query's voor komen die gebruikmaken van filters zoals: $filter = PartitionKey EQ ' Sales ' en (RowKey EQ ' 121 ' of RowKey EQ ' 322 ')  

Zie voor voor beelden van code aan client zijde die de Storage-client bibliotheek gebruikt voor het uitvoeren van efficiënte query's:  

* [Een punt query uitvoeren met de Storage-client bibliotheek](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Meerdere entiteiten ophalen met behulp van LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projectie aan server zijde](table-storage-design-patterns.md#server-side-projection)  

Zie voor voor beelden van code aan client zijde die meerdere entiteits typen kan verwerken die zijn opgeslagen in dezelfde tabel:  

* [Werken met heterogene entiteits typen](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Een geschikte PartitionKey kiezen
Uw keuze van **PartitionKey** moet de nood zaak hebben om het gebruik van EGTs (om consistentie te garanderen) in te stellen op basis van de vereiste om uw entiteiten over meerdere partities te verdelen (om ervoor te zorgen dat er een schaal bare oplossing wordt).  

U kunt op een extreem punt al uw entiteiten opslaan in één partitie, maar dit kan de schaal baarheid van uw oplossing beperken en voor komen dat de Table-service taak verdeling kan aanvragen. In het andere geval kunt u één entiteit per partitie opslaan, die zeer schaalbaar is en waardoor de Table-service taak verdeling kan aanvragen, maar die voor komt dat u trans acties van entiteits groepen gebruikt.  

Een ideale **PartitionKey** maakt het mogelijk om efficiënte query's te gebruiken en met voldoende partities om ervoor te zorgen dat uw oplossing schaalbaar is. Normaal gesp roken zult u zien dat uw entiteiten een geschikte eigenschap hebben die uw entiteiten distribueert over voldoende partities.

> [!NOTE]
> Een voor beeld: in een systeem dat informatie over gebruikers of werk nemers opslaat, kan UserID een goede PartitionKey zijn. Mogelijk hebt u verschillende entiteiten die een bepaalde gebruikers-id gebruiken als de partitie sleutel. Elke entiteit die gegevens over een gebruiker opslaat, wordt gegroepeerd in één partitie, zodat deze entiteiten toegankelijk zijn via entiteits transacties, terwijl ze toch uiterst schaalbaar zijn.
> 
> 

Er zijn aanvullende overwegingen in uw keuze aan **PartitionKey** die betrekking hebben op de manier waarop u entiteiten gaat invoegen, bijwerken en verwijderen. Zie [tabellen ontwerpen voor het wijzigen van gegevens](table-storage-design-for-modification.md)voor meer informatie.  

## <a name="optimizing-queries-for-the-table-service"></a>Query's optimaliseren voor de Table service
De Table service indexeert automatisch uw entiteiten met behulp van de waarden **PartitionKey** en **RowKey** in één geclusterde index. de reden hiervoor is dat het punt query's het meest efficiënt zijn om te gebruiken. Er zijn echter geen andere indexen dan die op de geclusterde index op de **PartitionKey** en **RowKey**.

Veel modellen moeten voldoen aan de vereisten om het opzoeken van entiteiten op basis van meerdere criteria mogelijk te maken. U kunt bijvoorbeeld werknemers entiteiten zoeken op basis van e-mail, werk nemer-ID of achternaam. De patronen die in de [tabel ontwerp patronen](table-storage-design-patterns.md) worden beschreven, zijn van deze typen vereisten en beschrijven manieren om het feit te omzeilen dat de Table service geen secundaire indexen levert:  

* [Intra-Partition secundair index patroon](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende **RowKey** -waarden (in dezelfde partitie) om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden.  
* [Secundair index patroon tussen partities](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende **RowKey** -waarden in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende **RowKey** -waarden.  
* [Patroon van index entiteiten](table-storage-design-patterns.md#index-entities-pattern) : behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  

## <a name="sorting-data-in-the-table-service"></a>Gegevens sorteren in de Table service
De Table service entiteiten retourneert die in oplopende volg orde zijn gesorteerd op basis van **PartitionKey** en vervolgens op **RowKey**. Deze sleutels zijn teken reeks waarden en om ervoor te zorgen dat numerieke waarden correct worden gesorteerd, moet u ze converteren naar een vaste lengte en ze met nullen aanvullen. Als de waarde van de werk nemer-ID die u als **RowKey** gebruikt, bijvoorbeeld een geheel getal is, moet u werk nemer-id **123** converteren naar **00000123**.  

Veel toepassingen hebben vereisten voor het gebruik van gegevens die in verschillende orders zijn gesorteerd: bijvoorbeeld het sorteren van werk nemers op naam of het samen voegen van de datum. De volgende patronen bepalen hoe alternatieve Sorteer volgorden voor uw entiteiten worden aangegeven:  

* [Intra-Partition secundair index patroon](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende RowKey-waarden (in dezelfde partitie) om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende RowKey-waarden.  
* [Secundair index patroon tussen partities](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) : Sla meerdere kopieën van elke entiteit op met behulp van verschillende RowKey-waarden in afzonderlijke partities in afzonderlijke tabellen om snelle en efficiënte zoek acties en alternatieve Sorteer volgorden mogelijk te maken met behulp van verschillende RowKey-waarden.
* [Eind patroon van logboek](table-storage-design-patterns.md#log-tail-pattern) : Haal de *n* -entiteiten op die het laatst aan een partitie zijn toegevoegd met behulp van een **RowKey** -waarde die in omgekeerde datum-en tijd volgorde wordt gesorteerd.  

## <a name="next-steps"></a>Volgende stappen

- [Tabelontwerppatronen](table-storage-design-patterns.md)
- [Relaties modelleren](table-storage-design-modeling.md)
- [Tabel gegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)
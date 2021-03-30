---
title: Model relaties in azure Table Storage-ontwerp | Microsoft Docs
description: Inzicht in het model proces bij het ontwerpen van uw Azure Table Storage-oplossing. Lees over een-op-veel, een-op-een-en overname relaties.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 3023b478ef7a4aaf6d9239e997bdf63282b56210
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88271189"
---
# <a name="modeling-relationships"></a>Relaties modelleren
In dit artikel wordt het modelleer proces beschreven om u te helpen bij het ontwerpen van uw Azure Table Storage-oplossingen.

Het bouwen van domein modellen is een belang rijke stap in het ontwerp van complexe systemen. Normaal gesp roken gebruikt u het model proces om entiteiten en de relaties daartussen te identificeren als een manier om inzicht te krijgen in het bedrijfs domein en het ontwerp van uw systeem te informeren. In deze sectie wordt uitgelegd hoe u een aantal algemene relatie typen in domein modellen kunt vertalen naar ontwerpen voor de Table service. Het toewijzings proces van een logisch gegevens model naar een fysiek NoSQL gebaseerd gegevens model wijkt af van het beleid dat wordt gebruikt bij het ontwerpen van een relationele data base. In het ontwerp van relationele data bases wordt doorgaans uitgegaan van een gegevensnormalisatieproces dat is geoptimaliseerd voor het minimaliseren van redundantie, en een declaratieve query functie waarmee wordt uitgelegd hoe de implementatie van de data base werkt.  

## <a name="one-to-many-relationships"></a>Een-op-veel-relaties
Een-op-veel-relaties tussen bedrijfs domein objecten komen vaak voor: bijvoorbeeld één afdeling heeft veel werk nemers. Er zijn verschillende manieren voor het implementeren van een-op-veel-relaties in de Table service elk met voor-en nadelen die relevant kunnen zijn voor het specifieke scenario.  

Bekijk het voor beeld van een grote Multi-National Corporation met tien duizenden afdelingen en werk nemers, waarbij elke afdeling veel werk nemers en elke werk nemer heeft die aan één specifieke afdeling zijn gekoppeld. Een manier is om afzonderlijke afdelings-en werknemers entiteiten zoals deze te bewaren:  


![Afzonderlijke afdelingen en entiteiten van werk nemers opslaan](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

In dit voor beeld ziet u een impliciete een-op-veel-relatie tussen de typen op basis van de waarde **PartitionKey** . Elke afdeling kan veel werk nemers hebben.  

In dit voor beeld ziet u ook een afdelings entiteit en de gerelateerde werknemers entiteiten in dezelfde partitie. U kunt ervoor kiezen om verschillende partities, tabellen of zelfs opslag accounts te gebruiken voor de verschillende entiteits typen.  

Een alternatieve methode is om uw gegevens te denormaliseren en alleen werk nemers te slaan met Gedenormaliseerde gegevens van de afdeling, zoals wordt weer gegeven in het volgende voor beeld. In dit specifieke scenario is deze gedenormaliseerde aanpak mogelijk niet het beste als u een vereiste hebt om de details van een Afdelings Manager te wijzigen. Als u dit wilt doen, moet u elke werk nemer in de afdeling bijwerken.  

![Entiteit werk nemer](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Zie voor meer informatie het [denormalisatie patroon](table-storage-design-patterns.md#denormalization-pattern) verderop in deze hand leiding.  

De volgende tabel bevat een overzicht van de voor-en nadelen van elk van de benaderingen die hierboven worden beschreven voor het opslaan van werk nemers en afdelings entiteiten die een een-op-veel-relatie hebben. U moet ook rekening houden met het aantal keren dat u verwacht verschillende bewerkingen uit te voeren: het kan acceptabel zijn om een ontwerp te hebben dat een dure bewerking bevat als deze bewerking alleen zelden plaatsvindt.  

<table>
<tr>
<th>Methode</th>
<th>Voordelen</th>
<th>Nadelen</th>
</tr>
<tr>
<td>Afzonderlijke entiteits typen, dezelfde partitie, dezelfde tabel</td>
<td>
<ul>
<li>U kunt een afdelings entiteit bijwerken met één bewerking.</li>
<li>U kunt een trans actie voor entiteits groepen * (EGT) gebruiken om de consistentie te behouden als u een vereiste hebt om een afdelings entiteit te wijzigen wanneer u een werknemers entiteit bijwerkt of invoegt of verwijdert. Als u bijvoorbeeld het aantal werk nemers per afdeling beheert.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u zowel een werk nemer als een afdelings entiteit voor sommige client activiteiten ophalen.</li>
<li>Opslag bewerkingen vinden plaats in dezelfde partitie. Op volumes met een hoge trans actie kan dit leiden tot een hotspot.</li>
<li>U kunt een werk nemer niet met een EGT verplaatsen naar een nieuwe afdeling.</li>
</ul>
</td>
</tr>
<tr>
<td>Afzonderlijke entiteits typen, verschillende partities of tabellen of opslag accounts</td>
<td>
<ul>
<li>U kunt een afdelings-of werknemers entiteit bijwerken met één bewerking.</li>
<li>Op volumes met een hoge trans actie kan dit de belasting over meer partities verdelen.</li>
</ul>
</td>
<td>
<ul>
<li>Mogelijk moet u zowel een werk nemer als een afdelings entiteit voor sommige client activiteiten ophalen.</li>
<li>U kunt EGTs niet gebruiken om consistentie te behouden wanneer u een werk nemer bijwerkt of invoegt/verwijdert en een afdeling bijwerkt. U kunt bijvoorbeeld het aantal werk nemers in een afdelings entiteit bijwerken.</li>
<li>U kunt een werk nemer niet met een EGT verplaatsen naar een nieuwe afdeling.</li>
</ul>
</td>
</tr>
<tr>
<td>Denormaliseren in één entiteits type</td>
<td>
<ul>
<li>U kunt alle informatie die u nodig hebt, ophalen met één aanvraag.</li>
</ul>
</td>
<td>
<ul>
<li>Het kan duur zijn om de consistentie te hand haven als u de gegevens van de afdeling moet bijwerken (hiervoor moet u alle werk nemers in een afdeling bijwerken).</li>
</ul>
</td>
</tr>
</table>

* Zie [trans acties voor entiteits groepen](table-storage-design.md#entity-group-transactions) voor meer informatie.  


Hoe u kiest tussen deze opties en welke van de voor-en nadelen het belangrijkst zijn, is afhankelijk van uw specifieke toepassings scenario's. Hoe vaak u bijvoorbeeld afdelings entiteiten wijzigt. moeten al uw werknemers query's de aanvullende informatie over de afdeling hebben. hoe dicht zijn de schaalbaarheids limieten voor uw partities of uw opslag account?  

## <a name="one-to-one-relationships"></a>Een-op-een-relaties
Domein modellen kunnen een-op-een-relaties tussen entiteiten bevatten. Als u een een-op-een-relatie wilt implementeren in de Table service, moet u ook kiezen hoe u de twee gerelateerde entiteiten wilt koppelen wanneer u deze beide moet ophalen. Deze koppeling kan impliciet zijn, op basis van een Conventie in de sleutel waarden of expliciet door een koppeling op te slaan in de vorm van **PartitionKey** -en **RowKey** -waarden in elke entiteit naar de gerelateerde entiteit. Zie de sectie [een-op-veel-relaties](#one-to-many-relationships)voor een bespreking van de vraag of u de gerelateerde entiteiten in dezelfde partitie moet opslaan.  

Er zijn ook implementatie overwegingen die kunnen leiden tot het implementeren van een-op-een-relaties in de Table service:  

* Verwerken van grote entiteiten (Zie het [patroon grote entiteiten](table-storage-design-patterns.md#large-entities-pattern)voor meer informatie).  
* Toegangs beheer implementeren (Zie toegang beheren met hand tekeningen voor gedeelde toegang) voor meer informatie.  

## <a name="join-in-the-client"></a>Toevoegen aan de client
Hoewel er manieren zijn om relaties te model leren in de Table service, moet u niet verg eten dat de twee voornaamste redenen voor het gebruik van de Table service schaal baarheid en prestaties zijn. Als u een model maakt van veel relaties die de prestaties en schaal baarheid van uw oplossing in de hand hebben, kunt u het beste zelf bepalen of u alle gegevens relaties in uw tabel ontwerp wilt maken. U kunt het ontwerp mogelijk vereenvoudigen en de schaal baarheid en prestaties van uw oplossing verbeteren als u uw client toepassing de benodigde deelname wilt laten uitvoeren.  

Als u bijvoorbeeld kleine tabellen hebt die gegevens bevatten die niet vaak veranderen, kunt u deze gegevens eenmaal ophalen en opslaan in de cache op de client. Dit kan voor komen dat herhaalde retours dezelfde gegevens ophalen. In de voor beelden die we in deze hand leiding hebben bekeken, is de set van afdelingen in een kleine organisatie waarschijnlijk klein en is het niet vaak een goede kandidaat te maken voor gegevens die client toepassing kan downloaden en opslaan als opzoek gegevens.  

## <a name="inheritance-relationships"></a>Overname relaties
Als uw client toepassing een set klassen gebruikt die deel uitmaken van een overname relatie om bedrijfs entiteiten weer te geven, kunt u deze entiteiten gemakkelijk persistent maken in de Table service. U kunt bijvoorbeeld de volgende set klassen in uw client toepassing hebben gedefinieerd, waarbij **persoon** een abstracte klasse is.

![Abstracte persoons klasse](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

U kunt exemplaren van de twee concrete klassen in het Table service persistent maken met behulp van een tabel met één persoon, met behulp van entiteiten in die er als volgt uitzien:  

![Tabel persoon](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Zie de sectie werken met heterogene entiteits typen verderop in deze hand leiding voor meer informatie over het werken met meerdere entiteits typen in dezelfde tabel in client code. Dit biedt voor beelden van het herkennen van het entiteits type in client code.  


## <a name="next-steps"></a>Volgende stappen

- [Tabelontwerppatronen](table-storage-design-patterns.md)
- [Ontwerp voor query's](table-storage-design-for-query.md)
- [Tabel gegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)

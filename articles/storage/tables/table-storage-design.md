---
title: Ontwerp schaal bare en uitvoerende tabellen in azure Table Storage. | Microsoft Docs
description: Meer informatie over het ontwerpen van schaal bare en uitvoerende tabellen in azure Table Storage. Bekijk tabel partities, entiteits groeps transacties en capaciteits-en kosten overwegingen.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 03/09/2020
ms.subservice: tables
ms.openlocfilehash: 8f3bd2a998066804bfb589e3262ac5e68db601fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93306939"
---
# <a name="design-scalable-and-performant-tables"></a>Schaalbare en beter bruikbare tabellen ontwerpen

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Als u schaal bare en uitvoerende tabellen wilt ontwerpen, moet u rekening houden met factoren zoals prestaties, schaal baarheid en kosten. Als u eerder schema's hebt ontworpen voor relationele data bases, zijn deze overwegingen bekend, maar hoewel er wat overeenkomsten zijn tussen het Azure Table service-opslag model en relationele modellen, zijn er ook belang rijke verschillen. Deze verschillen leiden doorgaans tot verschillende ontwerpen waarvan het prestatie meter item intuïtief of onjuist kan zijn voor iemand die vertrouwd is met relationele data bases, maar duidelijk is als u ontwerpt voor een NoSQL sleutel/waarde-archief zoals Azure Table service. Veel van uw ontwerp verschillen zijn het feit dat de Table service is ontworpen voor de ondersteuning van Cloud toepassingen die miljarden entiteiten (of rijen in relationele data base terminologie) van gegevens bevatten of voor gegevens sets die hoge-transactie volumes moeten ondersteunen. Daarom moet u nadenken over de manier waarop u uw gegevens opslaat en begrijpt hoe de Table service werkt. Met een goed ontworpen NoSQL-gegevens archief kan uw oplossing veel verder worden geschaald en tegen lagere kosten dan een oplossing die gebruikmaakt van een relationele data base. Deze hand leiding helpt u bij deze onderwerpen.  

## <a name="about-the-azure-table-service"></a>Over de Azure Table service
Deze sectie bevat een overzicht van enkele belang rijke functies van de Table service die vooral relevant zijn voor het ontwerpen van prestaties en schaal baarheid. Als u geen ervaring hebt met Azure Storage en de Table service, lees dan eerst [Inleiding tot Microsoft Azure Storage](../../storage/common/storage-introduction.md) en aan de [slag met Azure Table Storage met .net voordat u](../../cosmos-db/tutorial-develop-table-dotnet.md) de rest van dit artikel leest. Hoewel de focus van deze hand leiding zich op het Table service bevindt, bevat het een bespreking van de Azure-wachtrij en BLOB-Services, en hoe u deze kunt gebruiken met de Table service.  

Wat is de Table service? Zoals u kunt verwachten van de naam, gebruikt het Table service een tabel indeling om gegevens op te slaan. In de standaard terminologie vertegenwoordigt elke rij van de tabel een entiteit, en in de kolommen worden de verschillende eigenschappen van die entiteit opgeslagen. Elke entiteit heeft een paar sleutels om deze uniek te identificeren en een time stamp-kolom die de Table service gebruikt om bij te houden wanneer de entiteit voor het laatst is bijgewerkt. De tijds tempel wordt automatisch toegepast en u kunt de tijds tempel niet hand matig overschrijven met een wille keurige waarde. De Table service maakt gebruik van deze laatst gewijzigde tijds tempel (LMT) om optimistische gelijktijdigheid te beheren.  

> [!NOTE]
> De Table service-REST API bewerkingen retour neren ook een **ETAG** -waarde die is afgeleid van de LMT. In dit document worden de termen ETag en LMT door elkaar gebruikt omdat ze naar dezelfde onderliggende gegevens verwijzen.  
> 
> 

In het volgende voor beeld ziet u een eenvoudig tabel ontwerp voor het opslaan van werk nemers en afdelings entiteiten. Veel van de voor beelden die verderop in deze hand leiding worden weer gegeven, zijn gebaseerd op dit eenvoudige ontwerp.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>E-mail</th>
</tr>
<tr>
<td>Don</td>
<td>Huis</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>E-mail</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Afdeling</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Leeftijd</th>
<th>E-mail</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Tot nu toe worden deze gegevens weer gegeven zoals in een tabel in een relationele data base, met de belangrijkste verschillen tussen de verplichte kolommen en de mogelijkheid om meerdere entiteits typen op te slaan in dezelfde tabel. Daarnaast heeft elk van de door de gebruiker gedefinieerde eigenschappen, zoals **FirstName** of **Age** , een gegevens type, zoals geheel getal of teken reeks, net als een kolom in een relationele data base. Hoewel in tegens telling tot een relationele data base, betekent het schema-minder aard van het Table service dat een eigenschap niet hetzelfde gegevens type hoeft te hebben voor elke entiteit. Als u complexe gegevens typen in één eigenschap wilt opslaan, moet u een serialisatie-indeling gebruiken, zoals JSON of XML. Zie [inzicht in het tabel service gegevens model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)voor meer informatie over de tabel service, zoals ondersteunde gegevens typen, ondersteunde datumbereiken, naamgevings regels en grootte beperkingen.

Uw keuze van **PartitionKey** en **RowKey** is fundamenteel voor een goed ontwerp van een tabel. Elke entiteit die is opgeslagen in een tabel moet een unieke combi natie van **PartitionKey** en **RowKey** hebben. Net als bij sleutels in een relationele-database tabel worden de waarden **PartitionKey** en **RowKey** geïndexeerd voor het maken van een geclusterde index voor het inschakelen van snelle vormgeving. De Table service maakt echter geen secundaire indexen, dus **PartitionKey** en **RowKey** zijn de enige geïndexeerde eigenschappen. Sommige van de patronen die in [tabel ontwerp patronen](table-storage-design-patterns.md) worden beschreven, illustreren hoe u deze zicht bare beperking kunt omzeilen.  

Een tabel bestaat uit een of meer partities, en veel van de ontwerp beslissingen die u aanbrengt, zijn een geschikte **PartitionKey** en **RowKey** om uw oplossing te optimaliseren. Een oplossing kan bestaan uit één tabel die al uw entiteiten bevat, ingedeeld in partities, maar een oplossing heeft meestal meerdere tabellen. Met tabellen kunt u uw entiteiten logisch organiseren, de toegang tot de gegevens beheren met behulp van toegangs beheer lijsten en een volledige tabel verwijderen met één opslag bewerking.  

## <a name="table-partitions"></a>Tabelpartities
De account naam, tabel naam en **PartitionKey** bepalen samen de partitie in de opslag service waar de tabel service de entiteit opslaat. En als onderdeel van het adresserings schema voor entiteiten definiëren partities een bereik voor [trans acties (zie hieronder](#entity-group-transactions) ) en vormen de basis van de manier waarop de tabel service wordt geschaald. Zie voor meer informatie over partities de [controle lijst voor prestaties en schaal baarheid voor tabel opslag](storage-performance-checklist.md).  

In de Table service, een afzonderlijk knoop punt Services een of meer volledige partities en de service wordt geschaald door dynamische Load Balancing-partities tussen knoop punten. Als een knoop punt wordt geladen, kan de Table-service het bereik van partities dat door dat knoop punt wordt verwerkt, *splitsen* op verschillende knoop punten. Wanneer er verkeer wordt ondergebracht, kan de-service de partitielay-bereiken van de Stille knoop punten weer *samen voegen* op een enkel knoop punt.  

Zie het artikel [Microsoft Azure Storage: een Maxi maal beschik bare Cloud opslag service met sterke consistentie](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)voor meer informatie over de interne Details van de Table service, met name hoe de service partities beheert.  

## <a name="entity-group-transactions"></a>Trans acties van entiteits groep
In de Table service zijn EGTs (trans acties voor entiteits groepen) het enige ingebouwde mechanisme voor het uitvoeren van atomische updates op meerdere entiteiten. EGTs worden soms ook wel batch- *trans acties* genoemd. EGTs kan alleen worden gebruikt voor entiteiten die zijn opgeslagen in dezelfde partitie (dat wil zeggen, delen dezelfde partitie sleutel in een bepaalde tabel). U moet er dus altijd voor zorgen dat deze entiteiten zich in dezelfde partitie bevinden, zodat u een Atomic-transactioneel gedrag voor meerdere entiteiten nodig hebt. Dit is vaak een reden om meerdere entiteits typen in dezelfde tabel (en partitie) te bewaren en niet meerdere tabellen te gebruiken voor verschillende entiteits typen. Eén EGT kan aan Maxi maal 100 entiteiten worden gebruikt.  Als u meerdere gelijktijdige EGTs voor verwerking verzendt, is het belang rijk om ervoor te zorgen dat deze EGTs niet worden uitgevoerd op entiteiten die gemeen schappelijk zijn voor EGTs; anders kan de verwerking worden vertraagd.

Met EGTs kunt u ook een mogelijke trans actie in uw ontwerp introduceren. Dat wil zeggen dat het gebruik van meer partities de schaal baarheid van uw toepassing verg root, omdat Azure meer mogelijkheden heeft voor taak verdeling van aanvragen op verschillende knoop punten. Maar het gebruik van meer partities kan de mogelijkheid van uw toepassing beperken tot het uitvoeren van atomische trans acties en het behouden van sterke consistentie voor uw gegevens. Daarnaast zijn er specifieke schaalbaarheids doelen op het niveau van een partitie die de door Voer van trans acties die u voor één knoop punt kan verwachten, kunnen beperken. Zie [schaalbaarheids doelen voor standaard opslag accounts](../common/scalability-targets-standard-account.md)voor meer informatie over de schaalbaarheids doelen voor Azure Standard-opslag accounts. Zie [Schaalbaarheids- en prestatiedoelen voor de Table service](scalability-targets.md) voor meer informatie over de schaalbaarheidsdoelen voor Table-opslag.

## <a name="capacity-considerations"></a>Overwegingen bij capaciteitsbepaling

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Kostenoverwegingen
Table Storage is relatief goed koop, maar u moet kosten ramingen voor zowel het capaciteits gebruik als het aantal trans acties opnemen als onderdeel van de evaluatie van een Table service oplossing. In veel gevallen is het opslaan van gedenormaliseerde of dubbele gegevens voor het verbeteren van de prestaties of schaal baarheid van uw oplossing een geldige benadering. Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/)voor meer informatie over prijzen.  

## <a name="next-steps"></a>Volgende stappen

- [Tabel ontwerp patronen](table-storage-design-patterns.md)
- [Relaties modelleren](table-storage-design-modeling.md)
- [Ontwerp voor query's](table-storage-design-for-query.md)
- [Tabel gegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)
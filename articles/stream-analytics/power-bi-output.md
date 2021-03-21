---
title: Power BI uitvoer van Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u gegevens kunt uitvoeren van Azure Stream Analytics naar Power BI.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: a94389a075fd62d80345a21e32f1bc977dfdee87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020057"
---
# <a name="power-bi-output-from-azure-stream-analytics"></a>Power BI uitvoer van Azure Stream Analytics

U kunt [Power bi](https://powerbi.microsoft.com/) als uitvoer voor een stream Analytics taak gebruiken om een uitgebreide visualisatie ervaring met analyse resultaten te bieden. U kunt deze mogelijkheid gebruiken voor operationele Dash boards, het genereren van rapporten en het rapporteren van metrische gegevens.

Power BI uitvoer van Stream Analytics is momenteel niet beschikbaar in de regio's 21Vianet en Azure Duitsland (T-Systems International) van Azure China.

## <a name="output-configuration"></a>Uitvoer configuratie

De volgende tabel geeft een lijst van eigenschaps namen en de bijbehorende beschrijvingen om uw Power BI-uitvoer te configureren.

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Uitvoeralias |Geef een beschrijvende naam op die wordt gebruikt in query's om de uitvoer van de query naar deze Power BI uitvoer te sturen. |
| Werkruimte Groep |Als u het delen van gegevens met andere Power BI gebruikers wilt inschakelen, kunt u groepen in uw Power BI account selecteren of **mijn werk ruimte** kiezen als u niet naar een groep wilt schrijven. Als u een bestaande groep wilt bijwerken, moet u de Power BI-verificatie vernieuwen. |
| Naam van de gegevensset |Geef een naam op voor de gegevensset die u voor de Power BI uitvoer wilt gebruiken. |
| Tabelnaam |Geef een tabel naam op onder de gegevensset van de Power BI uitvoer. Power BI uitvoer van Stream Analytics taken kan momenteel slechts één tabel bevatten in een gegevensset. |
| Verbinding machtigen | U moet autoriseren met Power BI om uw uitvoer instellingen te configureren. Zodra u deze uitvoer toegang hebt verleend aan uw Power BI-dash board, kunt u de toegang intrekken door het wacht woord van het gebruikers account te wijzigen, de taak uitvoer te verwijderen of de Stream Analytics-taak te verwijderen. | 

Raadpleeg de zelf studie [Azure stream Analytics en Power bi](stream-analytics-power-bi-dashboard.md) voor een overzicht van het configureren van een Power bi uitvoer en een dash board.

> [!NOTE]
> Maak geen expliciete gegevensset en tabel in het dash board Power BI. De gegevensset en de tabel worden automatisch ingevuld wanneer de taak wordt gestart en de taak pomp uitvoer wordt gestart in Power BI. Als de taak query geen resultaten genereert, worden de gegevensset en tabel niet gemaakt. Als Power BI al een gegevensset en een tabel met dezelfde naam heeft als die in deze Stream Analytics taak, worden de bestaande gegevens overschreven.
>

### <a name="create-a-schema"></a>Een schema maken

Azure Stream Analytics maakt een Power BI gegevensset en tabel schema voor de gebruiker als deze nog niet bestaan. In alle andere gevallen wordt de tabel bijgewerkt met nieuwe waarden. Op dit moment kan slechts één tabel bestaan in een gegevensset. 

Power BI gebruikt het FIFO-Bewaar beleid (First-in, first-out). Gegevens worden verzameld in een tabel totdat er 200.000 rijen zijn gevonden.

> [!NOTE]
> Het is niet raadzaam om meerdere uitvoer bewerkingen te gebruiken om naar dezelfde gegevensset te schrijven, omdat deze verschillende problemen kan veroorzaken. Elke uitvoer probeert de Power BI dataset onafhankelijk te maken, wat kan leiden tot meerdere gegevens sets met dezelfde naam. Als de uitvoer geen consistente schema's heeft, wijzigt de gegevensset het schema bij elke schrijf bewerking, wat leidt tot te veel schema wijzigings aanvragen. Zelfs als deze problemen worden vermeden, is het mogelijk dat meerdere uitvoer minder worden uitgevoerd dan één samengevoegde uitvoer.

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Converteer een gegevens type van Stream Analytics naar Power BI

Azure Stream Analytics het gegevens model dynamisch bij runtime bijgewerkt als het uitvoer schema wordt gewijzigd. Kolom naam wijzigingen, kolom type wijzigingen en het toevoegen of verwijderen van kolommen worden allemaal bijgehouden.

Deze tabel bevat informatie over de gegevens type conversies van [Stream Analytics gegevens typen](/stream-analytics-query/data-types-azure-stream-analytics) Power BI naar de [EDM-typen (Entity Data Model)](/dotnet/framework/data/adonet/entity-data-model), als een Power bi-gegevensset en-tabel niet bestaan.

Van Stream Analytics | Power BI
-----|-----
bigint | Int64
nvarchar (max) | Tekenreeks
datum/tijd | Datum/tijd
float | Dubbel
Record matrix | Teken reeks type, constante waarde "IRecord" of "IArray"

### <a name="update-the-schema"></a>Het schema bijwerken

Stream Analytics leidt het schema van het gegevens model af op basis van de eerste set gebeurtenissen in de uitvoer. Later, indien nodig, wordt het schema van het gegevens model bijgewerkt om binnenkomende gebeurtenissen te kunnen verwerken die mogelijk niet in het oorspronkelijke schema passen.

Vermijd de `SELECT *` query om dynamische schema-updates voor rijen te voor komen. Naast mogelijke prestatie implicaties kan dit leiden tot een onzekerheid van de tijd die nodig is voor de resultaten. Selecteer de exacte velden die moeten worden weer gegeven op het Power BI dash board. Daarnaast moeten de gegevens waarden voldoen aan het gekozen gegevens type.

Vorige/huidige | Int64 | Tekenreeks | Datum/tijd | Dubbel
-----------------|-------|--------|----------|-------
Int64 | Int64 | Tekenreeks | Tekenreeks | Dubbel
Dubbel | Dubbel | Tekenreeks | Tekenreeks | Dubbel
Tekenreeks | Tekenreeks | Tekenreeks | Tekenreeks | Tekenreeks 
Datum/tijd | Tekenreeks | Tekenreeks |  Datum/tijd | Tekenreeks

## <a name="output-batch-size"></a>Grootte van uitvoer batch

Zie [Power bi-limieten voor rest API](/power-bi/developer/automation/api-rest-api-limitations)voor uitvoer Batch grootte.

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteit gebruiken om uw Azure Stream Analytics-taak te verifiëren voor Power BI (preview)](powerbi-output-managed-identity.md)
* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
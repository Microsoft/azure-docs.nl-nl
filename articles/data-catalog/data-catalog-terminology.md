---
title: Azure Data Catalog terminologie
description: Dit artikel bevat een inleiding tot de concepten en termen die worden gebruikt in Azure Data Catalog documentatie.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 008d0f106b07d61f0989a479457b12398000b5d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674565"
---
# <a name="azure-data-catalog-terminology"></a>Azure Data Catalog terminologie

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Dit artikel bevat een inleiding tot de concepten en termen die worden gebruikt in Azure Data Catalog documentatie.

## <a name="catalog"></a>Catalogus

De Azure Data Catalog is een op de cloud gebaseerde opslag voor meta gegevens waarin gegevens bronnen en gegevens assets kunnen worden geregistreerd. De catalogus fungeert als centrale opslag locatie voor de structurele meta gegevens die zijn geëxtraheerd uit gegevens bronnen en voor beschrijvende meta gegevens die worden toegevoegd door gebruikers.

## <a name="data-source"></a>Gegevensbron

Een gegevens bron is een systeem of container die gegevensassets beheert. Voor beelden zijn SQL Server-data bases, Oracle-data bases, SQL Server Analysis Services-data bases (tabellaire of multidimensionale) en SQL Server Reporting Services servers.

## <a name="data-asset"></a>Gegevens Asset

Gegevensassets zijn objecten die zijn opgenomen in gegevens bronnen die kunnen worden geregistreerd bij de catalogus. Voor beelden zijn SQL Server tabellen en weer gaven, Oracle-tabellen en-weer gaven, SQL Server Analysis Services metingen, dimensies en Kpi's en SQL Server Reporting Services rapporten.

## <a name="data-asset-location"></a>Locatie van gegevens activum

De catalogus slaat de locatie van een gegevens bron of gegevensasset op, die kan worden gebruikt om verbinding te maken met de bron met behulp van een client toepassing. De indeling en Details van de locatie variëren afhankelijk van het type gegevens bron. Een SQL Server tabel kan bijvoorbeeld worden geïdentificeerd aan de hand van de vier onderdeel naam: Server naam, database naam, schema naam, object naam, terwijl een SQL Server Reporting Services rapport kan worden geïdentificeerd met behulp van de URL.

## <a name="structural-metadata"></a>Structurele meta gegevens

Structured meta data is de meta gegevens die worden geëxtraheerd uit een gegevens bron waarmee de structuur van een gegevensasset wordt beschreven. Dit omvat de locatie van de activa, de object naam en het bijbehorende type en aanvullende specifieke kenmerken. De structurele meta gegevens voor tabellen en weer gaven bevatten bijvoorbeeld de namen en gegevens typen voor de kolommen van het object.

## <a name="descriptive-metadata"></a>Beschrijvende meta gegevens

Beschrijvende meta gegevens zijn meta gegevens waarmee het doel of de bedoeling van een gegevensasset wordt beschreven. Meestal worden beschrijvende meta gegevens toegevoegd door catalogus gebruikers met behulp van de Azure Data Catalog Portal, maar ze kunnen ook worden geëxtraheerd uit de gegevens bron tijdens de registratie. Het hulp programma voor het registreren van Azure Data Catalog haalt bijvoorbeeld beschrijvingen op uit de eigenschap Description in SQL Server Analysis Services en SQL Server Reporting Services, en van de [uitgebreide eigenschap ms_description](/previous-versions/sql/sql-server-2008-r2/ms190243(v=sql.105)) in SQL server data bases, als deze eigenschappen zijn gevuld met waarden.

## <a name="request-access"></a>Toegang aanvragen

De beschrijvende meta gegevens van een gegevens activum kunnen informatie bevatten over het aanvragen van toegang tot de gegevens Asset of gegevens bron. Deze informatie wordt weer gegeven met de locatie van de gegevens Asset en kan een of meer van de volgende opties bevatten:

* Het e-mail adres van de gebruiker of het team dat verantwoordelijk is voor het verlenen van toegang tot de gegevens bron.
* De URL van het gedocumenteerde proces dat gebruikers moeten volgen om toegang te krijgen tot de gegevens bron.
* De URL van een hulp programma voor identiteits-en toegangs beheer (zoals Microsoft Identity Manager) dat kan worden gebruikt om toegang te krijgen tot de gegevens bron.
* Een vermelding in een vrije tekst die beschrijft hoe gebruikers toegang kunnen krijgen tot de gegevens bron.

## <a name="preview"></a>Preview

Een preview in Azure Data Catalog is een moment opname van Maxi maal twintig records die tijdens de registratie kunnen worden opgehaald uit de gegevens bron en worden opgeslagen in de catalogus met de meta gegevens van de gegevensasset. Met de preview-versie kunt u gebruikers die een gegevens Asset ontdekken, de functie en het doel beter begrijpen. Met andere woorden, het bekijken van voorbeeld gegevens kan waardevoler zijn dan alleen de kolom namen en gegevens typen.
Previews worden alleen ondersteund voor tabellen en weer gaven en moeten door de gebruiker expliciet worden geselecteerd tijdens de registratie.

## <a name="data-profile"></a>Gegevensprofiel

Een gegevens profiel in Azure Data Catalog is een moment opname van meta gegevens op tabel-en kolom niveau over een geregistreerde gegevens Asset die tijdens de registratie kunnen worden opgehaald uit de gegevens bron en worden opgeslagen in de catalogus met de meta gegevens van de gegevensasset. Het gegevens profiel kan gebruikers helpen bij het ontdekken van een gegevens activum en de functie en het doel ervan beter begrijpen. Net als voor vertoningen moeten gegevens profielen tijdens de registratie expliciet worden geselecteerd door de gebruiker.

> [!NOTE]
> Het uitpakken van een gegevens profiel kan een kost bare bewerking zijn voor grote tabellen en weer gaven, en kan de tijd die nodig is voor het registreren van een gegevens bron aanzienlijk verhogen.


## <a name="user-perspective"></a>Gebruikers perspectief

In Azure Data Catalog kan elke gebruiker beschrijvende meta gegevens voor een geregistreerde gegevensasset opgeven. Elke gebruiker heeft een verschillend perspectief over de gegevens en het gebruik ervan. De beheerder die verantwoordelijk is voor een-server kan bijvoorbeeld de details van de service level agreement (SLA) of de back-upvensters van Windows geven. een data steward kan koppelingen bevatten naar documentatie voor de bedrijfs processen die door de gegevens worden ondersteund. en een analist kan een beschrijving bieden in de voor waarden die het meest relevant zijn voor andere analisten en die het nuttigst kan zijn voor gebruikers die de gegevens moeten ontdekken en begrijpen.

Elk van deze perspectieven is opzettelijk waardevol en met Azure Data Catalog elke gebruiker de informatie kan leveren die relevant voor hen is, terwijl alle gebruikers die informatie kunnen gebruiken om inzicht te krijgen in de gegevens en het doel ervan.

## <a name="expert"></a>Expert

Een expert is een gebruiker die is geïdentificeerd als een "expert"-perspectief voor een gegevensasset. Elke gebruiker kan zichzelf of een andere gebruiker toevoegen als een expert voor een Asset. Als een expert wordt vermeld, worden er geen aanvullende bevoegdheden in Azure Data Catalog overgedragen. Hiermee kunnen gebruikers eenvoudig deze perspectieven vinden die handig zijn bij het controleren van de beschrijvende meta gegevens van een activum.

## <a name="owner"></a>Eigenaar

Een eigenaar is een gebruiker met aanvullende bevoegdheden voor het beheren van een gegevensasset in Azure Data Catalog. Gebruikers kunnen eigenaar worden van geregistreerde gegevensassets en eigen aren kunnen andere gebruikers toevoegen als mede-eigen aars. Zie [gegevens assets beheren](data-catalog-how-to-manage.md) voor meer informatie  

> [!NOTE]
> Eigendom en beheer zijn alleen beschikbaar in de Standard-editie van Azure Data Catalog.

## <a name="registration"></a>Registratie

Registratie is de handeling van het ophalen van meta gegevens van gegevensassets uit een gegevens bron en het kopiëren naar de Azure Data Catalog-service. Geregistreerde gegevensassets kunnen vervolgens worden aantekend en gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

[Quickstart: Een Azure Data Catalog maken](data-catalog-get-started.md)
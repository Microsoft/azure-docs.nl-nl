---
title: Verbinding maken met gegevens bronnen in Azure Data Catalog
description: Instructies voor het maken van een verbinding met gegevens bronnen die zijn gedetecteerd met Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: da3302ac25aaac6202f42cee400b307fc701fe50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674968"
---
# <a name="how-to-connect-to-data-sources"></a>Verbinding maken met gegevensbronnen

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introductie
**Microsoft Azure Data Catalog** is een volledig beheerde Cloud service die fungeert als registratie systeem en detectie systeem voor zakelijke gegevens bronnen. Met andere woorden, het **Azure Data Catalog** is alles wat helpt mensen bij het detecteren, begrijpen en gebruiken van gegevens bronnen, en helpt organisaties bij het verkrijgen van meer waarde dan hun bestaande gegevens. Een belang rijk aspect van dit scenario is het gebruik van de gegevens: wanneer een gebruiker een gegevens bron detecteert en het doel ervan begrijpt, is de volgende stap om verbinding te maken met de gegevens bron om de gegevens te gebruiken.

## <a name="data-source-locations"></a>Gegevens bron locaties
Tijdens de registratie van de gegevens bron ontvangt **Azure Data Catalog** meta gegevens over de gegevens bron. Deze meta gegevens bevatten de details van de locatie van de gegevens bron. De details van de locatie variëren van de gegevens bron tot de gegevens bron, maar bevatten altijd de informatie die nodig is om verbinding te maken. De locatie voor een SQL Server tabel bevat bijvoorbeeld de server naam, de database naam, de schema naam en de tabel naam, terwijl de locatie van een SQL Server Reporting Services rapport de server naam en het pad naar het rapport bevat. Andere gegevens bron typen hebben locaties die de structuur en mogelijkheden van het bron systeem weer spie gelen.

## <a name="integrated-client-tools"></a>Geïntegreerde client hulpprogramma's
De eenvoudigste manier om verbinding te maken met een gegevens bron is met behulp van de ' openen in... ' in de **Azure Data Catalog** Portal. Dit menu bevat een lijst met opties voor het maken van verbinding met de geselecteerde gegevensasset.
Wanneer u de standaard tegel weergave gebruikt, is dit menu beschikbaar op elke tegel.

 ![Een SQL Server tabel in Excel openen vanuit de tegel gegevens element](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Wanneer u de lijst weergave gebruikt, is het menu beschikbaar in de zoek balk bovenin het portal-venster.

 ![Een SQL Server Reporting Services rapport openen in Report Manager](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Ondersteunde client toepassingen
Bij gebruik van de ' openen in... ' voor gegevens bronnen in de Azure Data Catalog Portal moet de juiste client toepassing op de client computer zijn geïnstalleerd.

| In toepassing openen | Bestands extensie/Protocol | Ondersteunde toepassings versies |
| --- | --- | --- |
| Excel |. ODC |Excel 2010 of hoger |
| Excel (top 1000) |. ODC |Excel 2010 of hoger |
| Power Query |.xlsx |Excel 2016 of Excel 2010 of Excel 2013 met de Power Query voor Excel-invoeg toepassing geïnstalleerd |
| Power BI Desktop |pbix |Power BI Desktop juli 2016 of hoger |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 update 4 of hoger met SQL Server tool is geïnstalleerd |
| Report Manager |http:// |Zie [browser vereisten voor SQL Server Reporting Services](/sql/reporting-services/browser-support-for-reporting-services-and-power-view) |

## <a name="your-data-your-tools"></a>Uw gegevens, uw hulp middelen
De opties die beschikbaar zijn in het menu, zijn afhankelijk van het type gegevens activum dat momenteel is geselecteerd. Niet alle mogelijke hulpprogram ma's worden uiteraard opgenomen in de ' openen in... ' , maar het is nog steeds eenvoudig om verbinding te maken met de gegevens bron met behulp van elk client programma. Wanneer een gegevensasset is geselecteerd in de **Azure Data Catalog** Portal, wordt de volledige locatie weer gegeven in het deel venster Eigenschappen.

 ![Verbindings gegevens voor een SQL Server tabel](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

De gegevens van de verbindings informatie verschillen van het gegevens bron type tot het gegevens bron type, maar met de informatie in de portal krijgt u alles wat u nodig hebt om verbinding te maken met de gegevens bron in elk client programma. Gebruikers kunnen de verbindings gegevens kopiëren voor de gegevens bronnen die ze hebben gedetecteerd met behulp van **Azure Data Catalog**, waardoor ze kunnen werken met de gegevens in het hulp programma van de keuze.

## <a name="connecting-and-data-source-permissions"></a>Verbinding maken en gegevens bron machtigingen
Hoewel **Azure Data Catalog** gegevens bronnen detecteerbaar maakt, blijft de toegang tot de gegevens zelf onder controle van de gegevens bron eigenaar of beheerder. Het detecteren van een gegevens bron in **Azure Data Catalog** geeft geen gebruiker machtigingen voor toegang tot de gegevens bron zelf.

Om het eenvoudiger te maken voor gebruikers die een gegevens bron hebben gedetecteerd, maar geen toegang hebben tot de gegevens van de gebruiker, kunnen gebruikers informatie opgeven in de eigenschap toegang aanvragen wanneer ze een gegevens bron aantekent. Informatie die hier wordt weer gegeven, inclusief koppelingen naar het proces of contact punt voor het verkrijgen van toegang tot gegevens bronnen – wordt gepresenteerd naast de gegevens bron locatie-informatie in de portal.

 ![Verbindings gegevens met instructies voor het opvragen van de toegang](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Samenvatting
Als u een gegevens bron registreert met **Azure Data Catalog** , kunnen de gegevens worden gedetecteerd door structurele en beschrijvende meta gegevens te kopiëren van de gegevens bron naar de catalogus service. Nadat een gegevens bron is geregistreerd en gedetecteerd, kunnen gebruikers verbinding maken met de gegevens bron vanuit het **Azure Data Catalog** portal ' openen in... ' menu's of gebruiken van de gewenste gegevens hulpprogramma's.

## <a name="see-also"></a>Zie ook
* [Aan de slag met Azure Data Catalog](data-catalog-get-started.md) -zelf studie voor stapsgewijze informatie over het maken van verbinding met gegevens bronnen.
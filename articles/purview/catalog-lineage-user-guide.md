---
title: Gebruikers handleiding voor Data Catalog afkomst (preview-versie)
description: Dit artikel bevat een overzicht van de afkomst-functie van de catalogus van Azure controle sfeer liggen.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 8b08a60d484aa3d52600b8aef2f53d6ca8a04f9b
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952164"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Gebruikers handleiding voor Azure controle sfeer liggen Data Catalog afkomst

Dit artikel bevat een overzicht van de functies voor gegevens afkomst in azure controle sfeer liggen Data Catalog.

## <a name="background"></a>Achtergrond

Een van de platform functies van Azure controle sfeer liggen is de mogelijkheid om de afkomst te tonen tussen gegevens sets die zijn gemaakt door gegevens processen. Systemen als Data Factory, gegevens share en Power BI de afkomst van gegevens vastleggen tijdens het verplaatsen. Aangepaste afkomst-rapportage wordt ook ondersteund via Atlas hooks en REST API.

## <a name="lineage-collection"></a>Afkomst-verzameling 
 Meta gegevens die zijn verzameld in azure controle sfeer liggen van ENTER prise Data Systems, worden weer gegeven in een end-to-end gegevens afkomst. Gegevens systemen die afkomst in controle sfeer liggen verzamelen, worden breed onderverdeeld in de volgende drie typen.

### <a name="data-processing-system"></a>Systeem voor gegevens verwerking
De hulpprogram ma's voor gegevens integratie en ETL kunnen tijdens de uitvoering afkomst naar Azure controle sfeer liggen pushen. Hulpprogram ma's zoals Data Factory, gegevens share, Synapse, Azure Databricks, enzovoort, behoren tot deze categorie gegevens systemen. De gegevensverwerkings systemen verwijzen naar gegevens sets als bron van verschillende data bases en opslag oplossingen om doel gegevens sets te maken. De lijst met gegevens verwerkings systemen die momenteel zijn geïntegreerd met controle sfeer liggen voor afkomst, worden weer gegeven in de onderstaande tabel.


| Systeem voor gegevens verwerking | Ondersteund bereik |
| ---------------------- | ------------|
| Azure Data Factory | [Kopieeractiviteit](how-to-link-azure-data-factory.md#data-factory-copy-activity-support) <br> [Activiteit gegevens stroom](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [SSIS-pakket activiteit uitvoeren](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [Moment opname delen](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Systemen voor gegevens opslag
Data bases & opslag oplossingen, zoals SQL Server, Teradata en SAP, hebben query-engines voor het transformeren van gegevens met behulp van een script taal. Gegevens afkomst van opgeslagen procedures worden verzameld in voor controle sfeer liggen en worden beheerd met afkomst van andere systemen.

| Systeem voor gegevens opslag | Ondersteund bereik |
| ---------------------- | ------------|
| Teradata | Opgeslagen procedures

### <a name="data-analytics--reporting-systems"></a>& rapportage systemen voor gegevens analyse
Gegevens systemen als Azure ML en Power BI rapport afkomst in azure controle sfeer liggen. Deze systemen gebruiken de gegevens sets van opslag systemen en verwerken via hun meta model voor het maken van BI-Dash boards, ML experimenten enzovoort.

| Rapportage systeem voor gegevens analyse & | Ondersteund bereik |
| ---------------------- | ------------|
| Power BI | [Gegevens sets, gegevens stromen, rapporten & Dash boards](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Aan de slag met afkomst

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWxTAK]

Afkomst in controle sfeer liggen omvat gegevens sets en processen. Gegevens sets worden ook wel knoop punten genoemd, terwijl processen ook randen kunnen worden genoemd:

* **Gegevensset (knoop punt)**: een gegevensset (gestructureerd of niet-structureel) die is opgegeven als invoer voor een proces. Bijvoorbeeld, een SQL-tabel, Azure-Blob en bestanden (zoals. CSV en. XML), worden alle gegevens sets die worden beschouwd. In de sectie afkomst van controle sfeer liggen worden gegevens sets vertegenwoordigd door rechthoekige vakken.

* **Proces (Edge)**: een activiteit of trans formatie die wordt uitgevoerd op een gegevensset wordt een proces genoemd. Bijvoorbeeld ADF Copy-activiteit, moment opname van gegevens delen, enzovoort. In het gedeelte afkomst van controle sfeer liggen worden processen weer gegeven door middel van een ronde rand.

Voer de volgende stappen uit om toegang te krijgen tot afkomst-informatie voor een asset in controle sfeer liggen:

1. Ga in het Azure Portal naar de [pagina Azure controle sfeer liggen-accounts](https://aka.ms/purviewportal).

1. Selecteer uw Azure controle sfeer liggen-account in de lijst en selecteer vervolgens **controle sfeer liggen-account starten** op de pagina **overzicht** .

1. Zoek op de **Start** pagina van Azure controle sfeer liggen naar de naam van een gegevensset of de naam van het proces, zoals ADF copy of de activiteit gegevens stroom. En druk op ENTER.

1. Selecteer in de zoek resultaten de Asset en selecteer het tabblad **afkomst** .

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Scherm afbeelding die laat zien hoe u het tabblad afkomst selecteert." border="true":::

## <a name="asset-level-lineage"></a>Afkomst op bedrijfs niveau

Azure controle sfeer liggen ondersteunt afkomst op activa niveau voor de gegevens sets en processen. Als u de afkomst wilt bekijken, gaat u naar het tabblad **afkomst** van de huidige asset in de catalogus. Selecteer het huidige knoop punt voor gegevensset-elementen. Standaard wordt de lijst met kolommen die deel uitmaken van de gegevens weer gegeven in het linkerdeel venster.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Scherm afbeelding die laat zien hoe u weergave kolommen selecteert op de pagina afkomst" border="true":::

## <a name="dataset-column-lineage"></a>Gegevensset-kolom afkomst

Als u afkomst op kolom niveau van een gegevensset wilt zien, gaat u naar het tabblad **afkomst** van de huidige asset in de catalogus en volgt u de onderstaande stappen:

1. Als u zich op het tabblad afkomst bevindt, schakelt u in het linkerdeel venster het selectie vakje in naast elke kolom die u wilt weer geven in de gegevens afkomst.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Scherm afbeelding die laat zien hoe u kolommen selecteert om weer te geven op de pagina afkomst." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. Beweeg de muis aanwijzer over een geselecteerde kolom in het linkerdeel venster of in de gegevensset van het afkomst-canvas om de kolom toewijzing te bekijken. Alle kolom instanties zijn gemarkeerd.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Scherm afbeelding die laat zien hoe u de muis aanwijzer boven een kolom naam houdt om de kolom stroom in een pad naar een Data afkomst te markeren." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. Als het aantal kolommen groter is dan wat er in het linkerdeel venster kan worden weer gegeven, gebruikt u de filter optie om een specifieke kolom op naam te selecteren. U kunt ook de muis gebruiken om door de lijst te bladeren.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Scherm afbeelding die laat zien hoe u kolommen op kolom naam kunt filteren op de pagina afkomst." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. Als het afkomst-canvas meer knoop punten en randen bevat, gebruikt u het filter om gegevens activa te selecteren of knoop punten verwerken op naam. U kunt ook de muis gebruiken om het afkomst-venster te pannen.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Scherm opname van gegevens Asset knooppunten op naam op de pagina afkomst." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. Gebruik de wissel knop in het linkerdeel venster om de lijst met gegevens sets in het afkomst-canvas te markeren. Als u de wissel knop uitschakelt, wordt alle activa weer gegeven die ten minste één van de geselecteerde kolommen bevatten. Als u de wissel knop inschakelt, worden alleen gegevens sets weer gegeven die alle kolommen bevatten.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Scherm afbeelding die laat zien hoe u de wissel knop kunt gebruiken om de lijst met knoop punten op de pagina afkomst te filteren." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>Proces kolom afkomst
Het gegevens proces kan een of meer invoer gegevens sets hebben voor het produceren van een of meer uitvoer. In controle sfeer liggen is op kolom niveau afkomst beschikbaar voor proces knooppunten. 
1. Scha kelen tussen invoer-en uitvoer gegevens sets vanuit een vervolg keuzelijst in het deel venster kolommen.
2. Selecteer kolommen uit een of meer tabellen om de afkomst-stroom van de invoer gegevensset naar de bijbehorende uitvoer gegevensset weer te geven.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="Scherm opname met de kolommen afkomst van een proces knooppunt." lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>Door assets bladeren in afkomst
1. Selecteer **overschakelen naar activa** op een wille keurige Asset om de bijbehorende meta gegevens te bekijken in de weer gave afkomst. Dit is een doel matige manier om naar een ander activum in de catalogus te bladeren vanuit de weer gave afkomst.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Scherm opname van het selecteren van een overschakeling naar een activum in een afkomst-gegevens Asset." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. Het afkomst-canvas kan complex worden voor populaire gegevens sets. De standaard weergave geeft alleen vijf afkomst weer voor de activa in de focus om wirwar te voor komen. De rest van de afkomst kan worden uitgebreid door te klikken op de bellen op het canvas afkomst. Gegevens gebruikers kunnen ook de activa verbergen op het canvas die geen belang hebben. Als u de wirwar verder wilt beperken, schakelt u de wissel knop **meer afkomst** boven aan het canvas afkomst uit. Met deze actie worden alle bellen in afkomst-canvas verborgen.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Scherm afbeelding die laat zien hoe u meer afkomst kunt in-of uitschakelen." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. Gebruik de slimme knoppen in het afkomst-canvas om een optimale weer gave van de afkomst te krijgen. Automatisch indelen, passend maken, in-en uitzoomen, volledig scherm en navigatie kaart zijn beschikbaar voor een overweldigende afkomste ervaring in de catalogus.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Scherm afbeelding die laat zien hoe u de afkomst-slimme knoppen selecteert." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Volgende stappen

* [Koppeling naar Azure Data Factory voor afkomst](how-to-link-azure-data-factory.md)
* [Koppeling naar de Azure-gegevens share voor afkomst](how-to-link-azure-data-share.md)
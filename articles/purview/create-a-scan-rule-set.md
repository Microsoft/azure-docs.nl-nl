---
title: Een set met scan regels maken
description: Maak een regel voor scannen in azure controle sfeer liggen om snel gegevens bronnen in uw organisatie te scannen.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553049"
---
# <a name="create-a-scan-rule-set"></a>Een set met scan regels maken

In een Azure controle sfeer liggen-catalogus kunt u scan regel sets maken om snel gegevens bronnen in uw organisatie te scannen.

Een scan regelset is een container voor het groeperen van een set scan regels, zodat u ze eenvoudig kunt koppelen aan een scan. U kunt bijvoorbeeld een standaardsorteervolgordeset voor elk van uw gegevens bron typen maken en vervolgens deze scan regel sets standaard voor alle scans binnen uw bedrijf gebruiken. Misschien wilt u ook gebruikers met de juiste machtigingen om andere scan regel sets te maken met verschillende configuraties op basis van zakelijke behoeften.

## <a name="steps-to-create-a-scan-rule-set"></a>Stappen voor het maken van een set met scan regels

Een set met scan regels maken:

1. Selecteer in de Azure controle sfeer liggen-catalogus **beheer centrum**.

1. Selecteer **regel sets scannen** in het linkerdeel venster en selecteer vervolgens **Nieuw**.

1. Selecteer op de pagina **nieuwe regel voor scannen** de gegevens bronnen die door de catalogus scanner worden ondersteund in de vervolg keuzelijst **bron type** . U kunt een set scan regels maken voor elk type gegevens bron dat u wilt scannen.

1. Geef een **naam** op voor de scan regel. De maximale lengte is 63 tekens, zonder spaties toegestaan. Voer desgewenst een **Beschrijving** in. De maximale lengte is 256 tekens.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Scherm afbeelding van de pagina met de scan regel." border="true":::

1. Selecteer **Doorgaan**.

   De pagina **Bestands typen selecteren** wordt weer gegeven. U ziet dat de opties voor bestands typen op deze pagina variëren afhankelijk van het type gegevens bron dat u op de vorige pagina hebt gekozen. Alle bestands typen zijn standaard ingeschakeld.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Scherm afbeelding van de pagina bestands typen selecteren.":::

   Met de **document bestands typen** die u selecteert op deze pagina, kunt u de volgende Office-bestands typen opnemen of uitsluiten:. doc,. DOCM,. docx,. dot,. ODP,. ODS,. ODT,. PDF,. pot,. PPS,. PPSX,. ppt,. PPTM,. PPTX,. XLC,. xls,. xlsb,. xlsm,. XLSX en. xlt.

1. Schakel een bestands type tegel in of uit door het bijbehorende selectie vakje in of uit te scha kelen. Als u een gegevens bron voor Data Lake type kiest (bijvoorbeeld Azure Data Lake Storage Gen2 of Azure Blob), schakelt u de bestands typen in waarvoor het schema moet worden geëxtraheerd en geclassificeerd.

1. Voor bepaalde typen gegevens bronnen kunt u ook [een aangepast bestands type maken](#create-a-custom-file-type).

1. Selecteer **Doorgaan**.

   De pagina **classificatie regels selecteren** wordt weer gegeven. Op deze pagina worden de geselecteerde **systeem regels** en **aangepaste regels** en het totale aantal geselecteerde classificatie regels weer gegeven. Standaard zijn alle selectie vakjes voor **systeem regels** ingeschakeld

1. Voor de regels die u wilt opnemen of uitsluiten, kunt u de selectie vakjes voor de classificatie regel voor **systeem regels** globaal per categorie in-of uitschakelen.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Scherm afbeelding van de pagina classificatie regels selecteren.":::

1. U kunt het categorie knooppunt uitvouwen en afzonderlijke selectie vakjes selecteren of wissen. Als de regel voor **Argentinië. DNI nummer** bijvoorbeeld een hoge fout-positieve waarde heeft, kunt u die specifieke selectie vakjes uitschakelen.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Scherm afbeelding die laat zien hoe u systeem regels selecteert.":::

1. Selecteer **maken** om de set met scan regels te maken.

### <a name="create-a-custom-file-type"></a>Een aangepast bestands type maken

Azure controle sfeer liggen biedt ondersteuning voor het toevoegen van een aangepaste extensie en het definiëren van een aangepast kolom scheidings teken in een set met scan regels.

Een aangepast bestands type maken:

1. Volg de stappen 1 tot en met 5 in [stappen voor het maken van een scan regel set](#steps-to-create-a-scan-rule-set) of bewerk een bestaande set met scan regels.

1. Selecteer op de pagina **Bestands typen selecteren** de optie **Nieuw bestands type** om een nieuw aangepast bestands type te maken.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Scherm afbeelding die laat zien hoe u een nieuw bestands type selecteert op de pagina bestands typen selecteren.":::

1. Voer een **bestands extensie** en een optionele **Beschrijving** in.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Scherm opname met de pagina nieuw aangepast bestands type." border="true":::

1. Maak een van de volgende selecties voor **Bestands inhoud in** om het type bestands inhoud in het bestand op te geven:

   - Selecteer **aangepast scheidings** teken en voer uw eigen **aangepaste scheidings** teken in (alleen één teken).

   - Selecteer **type systeem bestand** en kies een systeem bestands type (bijvoorbeeld XML) in de vervolg keuzelijst **systeem bestands type** .

1. Selecteer **maken** om het aangepaste bestand op te slaan.

   Het systeem keert terug naar de pagina **Bestands typen selecteren** en voegt het nieuwe aangepaste bestands type in als een nieuwe tegel.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Scherm opname van de tegel nieuw aangepast bestands type op de pagina bestands typen selecteren.":::

1. Selecteer **bewerken** in de tegel nieuw bestands type als u deze wilt wijzigen of verwijderen.

1. Selecteer **door gaan** om het configureren van de set met scan regels te volt ooien.

## <a name="system-scan-rule-sets"></a>Regel sets voor systeem scan

Regel sets voor systeem scans zijn door micro soft gedefinieerde scan regel sets die automatisch worden gemaakt voor elke Azure controle sfeer liggen-catalogus. Elke regelset van een systeem scan is gekoppeld aan een specifiek gegevens bron type. Wanneer u een scan maakt, kunt u deze koppelen aan een regelset van een systeem scan. Telkens wanneer micro soft een update uitvoert voor deze systeem regel sets, kunt u deze bijwerken in uw catalogus en de update Toep assen op alle bijbehorende scans.

1. Als u de lijst met regel sets voor systeem scans wilt weer geven, selecteert u **regel sets scannen** in het **beheer centrum** en kiest u het tabblad **systeem** .

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Scherm opname van de lijst met regel sets voor systeem scans." lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Elke regel set voor systeem scans heeft een **naam**, **bron type** en een **versie**. Als u het versie nummer selecteert van een scan regel die in de kolom **versie** is ingesteld, ziet u de regels die zijn gekoppeld aan de huidige versie en de vorige versies (indien van toepassing).

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Scherm opname van een pagina met een regel voor systeem scans." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Als er een update beschikbaar is voor een regel voor systeem scan, kunt u **bijwerken** selecteren in de kolom **versie** . Kies op de pagina systeem scan regel een versie in de vervolg keuzelijst **Selecteer een nieuwe versie om te updaten** . De pagina bevat een lijst met de systeem classificatie regels die zijn gekoppeld aan de nieuwe versie en de huidige versie.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Scherm opname waarin wordt getoond hoe de versie van een regel voor een systeem scan moet worden gewijzigd." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Een scan koppelen aan een regelset van een systeem scan

Wanneer u [een scan maakt](tutorial-scan-data.md#scan-data-into-the-catalog), kunt u deze als volgt koppelen aan een regel voor systeem scan:

1. Selecteer op de pagina **een set scan regels selecteren** de regel set systeem scan.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Scherm afbeelding waarin wordt getoond hoe een regel voor een systeem scan moet worden geselecteerd voor een scan." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Selecteer **door gaan** en selecteer vervolgens **opslaan en uitvoeren**.

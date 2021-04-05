---
title: 'Zelfstudie: Woordenlijsttermen maken en importeren in Azure Purview (preview)'
description: In deze zelfstudie wordt beschreven hoe u woordenlijsttermen maakt, hoe u woordenlijsttermen aan een asset toevoegt en hoe u woordenlijsttermen kunt importeren.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 0ea6fcaff1ec699431da8b67adee68735a8611a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696086"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Zelfstudie: Woordenlijsttermen maken en importeren in Azure Purview (preview)

> [!IMPORTANT]
> Azure Purview is momenteel in PREVIEW. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

Een woordenlijst is een belangrijk hulpmiddel voor het onderhouden en organiseren van uw catalogus. U stelt uw woordenlijst samen door nieuwe termen te definiëren of door een lijst met termen te importeren en vervolgens deze termen op uw assets toe te passen.

Deze zelfstudie is *deel 5 van een vijfdelige reeks zelfstudies* waarin u leert wat u moet weten als u met behulp van Azure Purview gegevensbeheer voor een volledig gegevensdomein wilt uitvoeren. Deze zelfstudie borduurt voort op het werk dat u hebt voltooid in [Deel 4: Resourcesets, gegevens, schema's en classificaties verkennen in Azure Purview (preview)](tutorial-schemas-and-classifications.md).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Woordenlijsttermen maken.
> * Woordenlijsttermen aan een asset toevoegen.
> * Woordenlijsttermen importeren.

## <a name="prerequisites"></a>Vereisten

* Voltooi de [Zelfstudie: Resourcesets, gegevens, schema's en classificaties verkennen in Azure Purview (preview)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Woordenlijsttermen maken

U kunt bedrijfstermen of technische termen voor de woordenlijst maken. U kunt ook aantekeningen toevoegen aan uw assets door er termen op toe te passen.

Hier volgt een samenvatting van een aantal van de meest voorkomende velden op de pagina **Woordenlijstterm**:

* **Status**: Wijs een status toe aan de term (**Concept**, **Goedgekeurd**, **Verlopen** of **Waarschuwing**).

* **Definitie**: Voer een definitie in voor de term.

* **Acroniem**: Voer een afgekorte versie van de term in door de eerste letters van elk woord te gebruiken.

* **Synoniemen**: Selecteer andere termen met dezelfde of vergelijkbare definities.

* **Gerelateerde termen**: Selecteer andere termen in de woordenlijst die aan deze term zijn gerelateerd maar andere definities hebben. Voorbeelden zijn technische termen die zijn gekoppeld aan een bedrijfsterm, een codenaam of andere termen die met de term in verband moeten worden gebracht.

Volg deze stappen om een woordenlijstterm te maken:

1. Navigeer naar uw Azure Purview-resource in Azure Portal en selecteer **Purview Studio openen**. U wordt automatisch naar de startpagina van Purview Studio geleid.

1. Selecteer **Woordenlijst** in het linkerdeelvenster om de pagina **Woordenlijsttermen** te openen.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Schermopname van de pagina Woordenlijsttermen.":::

1. Selecteer **Nieuwe term** > **Systeemstandaard** > **Doorgaan**.

1. Voer op het tabblad **Overzicht** van de pagina **Nieuwe term** de **naam** in voor de nieuwe term: *Financieel*.

1. Voer de **definitie** in: *Deze term vertegenwoordigt financiële informatie voor Contoso Inc.*

1. Selecteer **Goedgekeurd** in de vervolgkeuzelijst **Status**.

1. Wanneer u klaar bent, selecteert u **Maken**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Schermopname die laat zien hoe u een nieuwe woordenlijstterm maakt.":::

## <a name="add-glossary-terms-to-an-asset"></a>Woordenlijsttermen aan een asset toevoegen

1. Volg de stappen die u hebt geleerd in [deel 1 van deze serie zelfstudies](tutorial-scan-data.md) om een asset te vinden. Bijvoorbeeld **Contoso_CashFlow_{N}.csv**.

1. Selecteer op de detailpagina van de asset de optie **Bewerken**.

1. Selecteer in de vervolgkeuzelijst **Woordenlijsttermen** op het tabblad **Overzicht** de optie **Financieel** en selecteer vervolgens **Opslaan**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Schermopname die laat zien hoe een woordenlijstterm aan een asset kan worden toegevoegd.":::

1. Ga naar de sectie **Woordenlijsttermen** op het tabblad **Overzicht** en kijk of de term *Financieel* op de asset is toegepast.

## <a name="import-glossary-terms"></a>Woordenlijsttermen importeren

Als u termen bulkgewijs wilt importeren of bestaande termen bulkgewijs wilt bijwerken, uploadt u een vooraf ingevulde sjabloon naar uw woordenlijst.

In deze procedure importeert u de termen uit de woordenlijst via een CSV-bestand:

1. Houd er rekening mee dat u het bestand met de naam *StarterKitTerms.csv* hebt opgeslagen. Dit bestand maakt deel uit van de starterskit die u hebt gedownload in [deel 1 van deze reeks zelfstudies](tutorial-scan-data.md).

   Dit bestand bevat een lijst met vooraf ingevulde termen die relevant zijn voor uw gegevensdomein.

1. Als u wilt gaan importeren, selecteert u **Woordenlijst** en vervolgens **Termen importeren**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Schermopname die laat zien hoe woordenlijsttermen worden geïmporteerd.":::

1. Selecteer op de pagina **Termen importeren** de optie **Systeemstandaard** en selecteer vervolgens **Doorgaan**.

1. Selecteer **Bladeren**, ga naar de locatie waar u *StarterKitTerms.csv* hebt gedownload en selecteer **Openen**.

1. Selecteer **OK** om te beginnen met het importeren van de termen.

   Nadat het importeren is voltooid, worden de nieuwe Woordenlijsttermen weergegeven op de pagina **Woordenlijsttermen**.

1. Bekijk elk van de zojuist geïmporteerde termen om te zien hoe ze zijn gedefinieerd.

## <a name="create-custom-term-templates"></a>Aangepaste termsjablonen maken

In deze sectie leert u hoe u een aangepaste termsjabloon maakt met aangepaste kenmerken, en gegevens importeert met behulp van een sjabloon in CSV-indeling.

Er zijn verschillende bestaande sjablonen voor systeemtermen. U kunt deze weergeven door **Woordenlijst** > **Termsjablonen beheren** > **Systeem** te selecteren.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="systeemtermsjablonen.":::

Voer de volgende stappen uit om een nieuwe aangepaste termsjabloon te maken:

1. Selecteer **Woordenlijst** in het menu aan de linkerkant.
1. Selecteer **Termsjablonen beheren** > **Aangepast** > **Nieuwe termsjabloon**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="een nieuwe aangepaste termsjabloon maken.":::

Voer de volgende stappen uit op het scherm **Nieuwe termsjabloon**:

1. Voer de **sjabloonnaam** `Sensitivity level` in.
1. Voer uw gewenste beschrijving in, zoals `Indicates the level of sensitivity for this data.`
1. Selecteer **+ Nieuw kenmerk** om een dialoogvenster te openen voor het toevoegen van kenmerken.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="scherm nieuwe termsjabloon starten.":::

1. Voer in het scherm **Nieuw kenmerk** de volgende parameters in:

   |Instelling|Voorgestelde waarde|
   |---------|-----------|
   |Kenmerknaam|is gevoelige informatie|
   |Veldtype|vervolgkeuzelijst|Eén keuze|
   |Als vereist markeren|schakel dit selectievakje in.|
   |+ Een keuze toevoegen| Twee keuzes toevoegen. 'Ja' en 'Nee'.|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="een nieuw kenmerk maken.":::

1. Herhaal de vorige stappen om een ander kenmerk met de naam `can be shared externally`toe te voegen. Nadat beide kenmerken zijn toegevoegd, selecteert u ten slotte **Maken**.

## <a name="import-terms-from-a-template"></a>Termen uit een sjabloon importeren

Vervolgens importeert u een nieuwe term met behulp van de sjabloon **Vertrouwelijkheidsniveau** die u hebt gemaakt. 

1. Selecteer in het scherm **Woordenlijsttermen** de optie **Termen importeren**.

1. Selecteer **Vertrouwelijkheidsniveau** in het scherm **Termen importeren**. Selecteer **Doorgaan**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Vertrouwelijkheidsniveau selecteren.":::

1. De termsjabloon voor **Vertrouwelijkheidsniveau** bevat standaard systeemkenmerken, evenals de nieuwe kenmerken die u hebt toegevoegd: `can be shared externally` en `is sensitive information`. Selecteer **Een voorbeeld van een CSV-bestand downloaden**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Voorbeeld van een CSV-bestand downloaden.":::

1. Er wordt een sjabloonbestand voor u gedownload zodat u nieuwe woordenlijsttermen met klantkenmerken kunt bewerken en uploaden. Open het CSV-bestand dat u hebt gedownload. Voeg nieuwe termen en hun bijbehorende waarden als nieuwe rijen toe aan het CSV-bestand.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Term in CSV-bestand maken.":::

   Alle eventuele termen die worden vermeld in de kolom **Gerelateerde termen** of **Synoniemen**, maar nog niet bestaan, worden gemaakt wanneer het bestand wordt geüpload. Deze worden gemaakt met behulp van de sjabloon **Systeemstandaard**.

1. Als u het bestand wilt uploaden, gaat u terug naar het scherm **Termen importeren** en selecteert u **Bladeren** naast het vak **Het volledige CSV-bestand selecteren om te uploaden**. Selecteer het bestand in het dialoogvenster dat wordt geopend, en selecteer vervolgens **OK**.

1. De nieuwe termen worden nu weergegeven in het scherm **Woordenlijsttermen**. U kunt details over de termen weergeven door op de naam van de term in de lijst te klikken.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Woordenlijsttermen maken.
> * Woordenlijsttermen aan een asset toevoegen.
> * Woordenlijsttermen importeren.

Ga door naar het volgende artikel voor meer informatie over de verschillende catalogusinzichten.

> [!div class="nextstepaction"]
> [Inzichten in Azure Purview begrijpen](concept-insights.md)

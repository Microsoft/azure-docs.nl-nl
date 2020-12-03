---
title: Classificaties Toep assen op activa (preview-versie)
description: In dit document wordt beschreven hoe u classificaties toepast op activa.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553110"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Classificaties Toep assen op assets in azure controle sfeer liggen

In dit artikel wordt beschreven hoe u classificaties toepast op activa.

## <a name="introduction"></a>Inleiding

Classificaties kunnen systeem-of aangepaste typen zijn. Systeem classificaties zijn standaard aanwezig in controle sfeer liggen. Aangepaste classificaties kunnen worden gemaakt op basis van een reguliere-expressie patroon. Classificaties kunnen automatisch of hand matig worden toegepast op activa.

In dit document wordt uitgelegd hoe u classificaties toepast op uw gegevens.

## <a name="prerequisites"></a>Vereisten

- Maak aangepaste classificaties op basis van uw behoeften.
- Stel scan in voor uw gegevens bronnen.

## <a name="apply-classifications"></a>Classificaties Toep assen
U kunt in azure controle sfeer liggen systeem-of aangepaste classificaties Toep assen op een bestand, tabel of kolom element. In dit artikel worden de stappen beschreven voor het hand matig Toep assen van classificaties op uw activa.

### <a name="apply-classification-to-a-file-asset"></a>Classificatie Toep assen op een bestands Asset
Met Azure controle sfeer liggen kunnen documentatie bestanden worden gescand en automatisch geclassificeerd. Als u bijvoorbeeld een bestand met de naam *multiple.docx* hebt en dit een nationaal id-nummer in de inhoud heeft, voegt Azure controle sfeer liggen de classificatie id van het **EU-nummer** toe aan de detail pagina van het bestand Asset.

In sommige scenario's wilt u mogelijk hand matig classificaties toevoegen aan de bestands Asset. Als u meerdere bestanden hebt die zijn gegroepeerd in een resourceset, voegt u classificaties toe op het niveau van de resourceset.

Volg deze stappen om een aangepaste of systeem classificatie toe te voegen aan een partitie resourceset:

1. Zoek of blader naar de partitie en ga naar de pagina Asset detail.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Scherm opname van de pagina met activa Details.":::

1. Bekijk op het tabblad **overzicht** de sectie **classificaties** om te zien of er bestaande classificaties zijn. Selecteer **Bewerken**.

1. Selecteer in de vervolg keuzelijst **classificaties** de specifieke classificaties waarin u bent geïnteresseerd. Bijvoorbeeld, een creditcard **nummer**, een systeem classificatie en **CustomerAccountID**, een aangepaste classificatie.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Scherm opname waarin wordt getoond hoe classificaties moeten worden geselecteerd om aan een activum toe te voegen.":::

1. Selecteer **Opslaan**.

1. Controleer op het tabblad **overzicht** of de classificaties die u hebt geselecteerd, worden weer gegeven onder de sectie **classificaties** .

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Scherm afbeelding die laat zien hoe u classificaties kunt bevestigen die zijn toegevoegd aan een Asset.":::

### <a name="apply-classification-to-a-table-asset"></a>Classificatie Toep assen op een tabel Asset

Wanneer Azure controle sfeer liggen uw gegevens bronnen scant, worden classificaties niet automatisch toegewezen aan tabel activa. Als u wilt dat de tabel Asset een classificatie heeft, moet u deze hand matig toevoegen.

Een classificatie toevoegen aan een tabel Asset:

1. Een tabel activum zoeken waarin u geïnteresseerd bent. Bijvoorbeeld **klant** tabel.

1. Controleer of er geen classificaties zijn toegewezen aan de tabel. Selecteer **bewerken**

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Scherm afbeelding die laat zien hoe u de classificaties van een Table-activum kunt weer geven en bewerken.":::

1. Selecteer in de vervolg keuzelijst **classificaties** een of meer classificaties. In dit voor beeld wordt een aangepaste classificatie met de naam **CustomerInfo** gebruikt, maar u kunt een wille keurige classificaties voor deze stap selecteren.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Scherm opname waarin wordt getoond hoe classificaties moeten worden geselecteerd om aan een tabel element toe te voegen.":::

1. Selecteer **Opslaan** om de classificaties op te slaan.

1. Controleer op de pagina **overzicht** of Azure controle sfeer liggen uw nieuwe classificaties heeft toegevoegd.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Scherm afbeelding die laat zien hoe u kunt controleren of classificaties zijn toegevoegd aan een tabel Asset.":::

### <a name="add-classification-to-a-column-asset"></a>Classificatie toevoegen aan een kolom activum

Azure controle sfeer liggen scant automatisch classificaties en voegt deze toe aan alle activa van de kolom. Als u de classificatie echter wilt wijzigen, kunt u dit doen op kolom niveau.

Een classificatie toevoegen aan een kolom:

1. Zoek en selecteer het kolom element en selecteer vervolgens **bewerken** op het tabblad **overzicht** .

1. Selecteer het tabblad **schema**

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Scherm afbeelding die laat zien hoe u het schema van een kolom bewerkt.":::

1. Bepaal de kolommen waarin u bent geïnteresseerd en selecteer **een classificatie toevoegen**. In dit voor beeld wordt een **algemene wacht woord** classificatie toegevoegd aan de kolom **PasswordHash** .

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Scherm opname waarin wordt getoond hoe een classificatie aan een kolom moet worden toegevoegd.":::

1. Selecteer **Opslaan**.

1. Selecteer het tabblad **schema** en controleer of de classificatie is toegevoegd aan de kolom.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Scherm afbeelding die laat zien hoe u kunt bevestigen dat een classificatie is toegevoegd aan een kolom schema.":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Gevolgen van het opnieuw scannen op bestaande classificaties

Classificaties worden de eerste keer toegepast, op basis van een voor beeld van een controle van uw gegevens en het resultaat van het ingestelde regex-patroon. Als er op het moment van opnieuw scannen nieuwe classificaties van toepassing zijn, krijgt de kolom extra classificaties. Bestaande classificaties blijven in de kolom staan en moeten hand matig worden verwijderd.

## <a name="next-steps"></a>Volgende stappen
Zie [een aangepaste classificatie maken](create-a-custom-classification-and-classification-rule.md)voor meer informatie over het maken van een aangepaste classificatie.
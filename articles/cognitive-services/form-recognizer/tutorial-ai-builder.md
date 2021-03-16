---
title: 'Zelfstudie: Een app voor het verwerken van formulieren maken met AI Builder - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie gebruikt u AI Builder om een toepassing voor formulierverwerking te maken en te trainen.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: lajanuar
ms.openlocfilehash: 30c44ac60783132344cc018135d2c248f91f46bc
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467116"
---
# <a name="tutorial-create-a-form-processing-app-with-ai-builder"></a>Zelfstudie: Een app voor het verwerken van formulieren maken met AI Builder

[AI Builder](/ai-builder/overview) is een Power Platform-functie waarmee u processen kunt automatiseren en resultaten kunt voorspellen om de bedrijfsprestaties te verbeteren. U kunt formulierverwerking van AI Builder gebruiken om AI-modellen te maken waarmee sleutel-waardeparen en tabelgegevens uit formulierdocumenten worden geïdentificeerd en geëxtraheerd.

> [!NOTE]
> Dit project is ook beschikbaar als [Microsoft Learn-module](/learn/modules/get-started-with-form-processing/).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een AI-model voor formulierverwerking maken
> * Uw model trainen
> * Uw model publiceren voor gebruik in Azure Power Apps of Power Automate

## <a name="prerequisites"></a>Vereisten

* Een set van ten minste vijf formulieren van hetzelfde type voor het trainen en testen van gegevens. Zie [Een set met trainingsgegevens samenstellen](./build-training-data-set.md) voor tips en opties voor het samenstellen van uw set met trainingsgegevens. Voor deze quickstart kunt u de bestanden in de map **Trainen** van de [set met voorbeeldgegevens](https://go.microsoft.com/fwlink/?linkid=2128080) gebruiken.
* Een licentie voor Power Apps of Power Automate - zie de [licentiehandleiding](https://go.microsoft.com/fwlink/?linkid=2085130). De licentie moet [Common Data Service](https://powerplatform.microsoft.com/common-data-service/) bevatten.
* Een [invoegtoepassing of proefversie](https://go.microsoft.com/fwlink/?LinkId=2113956&clcid=0x409) van AI Builder.


## <a name="create-a-form-processing-project"></a>Een project voor formulierverwerking maken

1. Ga naar [Power Apps](https://make.powerapps.com/) of [Power Automate](https://flow.microsoft.com/signin) en meld u aan met het account van uw organisatie.
1. Selecteer **AI Builder** > **Bouwen** in het linkerdeelvenster.
1. Selecteer de kaart **Formulierverwerking**.
1. Voer een naam voor uw model in.
1. Selecteer **Maken**.

## <a name="upload-and-analyze-documents"></a>Documenten uploaden en analyseren

Op de pagina **Documenten toevoegen** moet u voorbeelddocumenten opgeven om het model te trainen voor het type formulier waaruit u gegevens wilt extraheren. Nadat u uw documenten hebt geüpload, analyseert AI Builder ze om te controleren of ze voldoende zijn voor het trainen van een model.

> [!NOTE]
> AI Builder biedt momenteel geen ondersteuning voor de volgende typen invoergegevens voor formulierverwerking:
>
> - Complexe tabellen (geneste tabellen, samengevoegde kopteksten of cellen, enzovoort)
> - Selectievakjes of keuzerondjes
> - PDF-documenten die langer zijn dan 50 pagina's
> - Invulbare PDF-documenten
>
> Zie [Vereisten voor invoer](./overview.md#input-requirements) voor meer informatie over de vereisten voor invoerdocumenten.

### <a name="upload-your-documents"></a>Uw documenten uploaden

1. Selecteer **Documenten toevoegen**, selecteer minimaal vijf documenten en selecteer vervolgens **Uploaden**.
1. Nadat het uploaden is voltooid, selecteert u **Sluiten**.
1. Selecteer vervolgens **Analyseren**.

> [!NOTE] 
> Nadat u deze documenten hebt geüpload, kunt u nog steeds een aantal documenten verwijderen of extra uploaden.

> [!div class="mx-imgBorder"]
> ![Schermopname met de knop Documenten toevoegen gemarkeerd.](./media/tutorial-ai-builder/add-documents-page.png)

### <a name="analyze-your-documents"></a>Uw documenten analyseren

Tijdens de analysestap onderzoekt AI Builder de documenten die u hebt geüpload en detecteert het programma de velden en tabellen. Hoe lang deze bewerking duurt, is afhankelijk van het aantal opgegeven documenten, maar in de meeste gevallen duurt het slechts een paar minuten.

Wanneer de analyse is voltooid, selecteert u de miniatuur om de veldselectie te openen.

> [!IMPORTANT]
> Als de analyse mislukt, komt dat waarschijnlijk omdat AI Builder geen gestructureerde tekst in uw documenten kan detecteren. Controleer of de documenten die u hebt bijgewerkt, voldoen aan de [invoervereisten](./overview.md#input-requirements).

## <a name="select-your-form-fields"></a>Uw formuliervelden selecteren

Op de pagina voor veldselectie kiest u de velden die voor u van belang zijn:

1. Als u een veld wilt selecteren, klikt u op een rechthoek die een gedetecteerd veld in het document aangeeft of selecteert u meerdere velden door te klikken en te slepen. U kunt ook velden selecteren met het deelvenster aan de rechterkant.
1. Klik op de naam van het geselecteerde veld als u de naam wilt aanpassen of de geëxtraheerde labels wilt normaliseren.

    Wanneer u op een gedetecteerd veld klikt, wordt de volgende informatie weergegeven:

    - **Veldnaam**: De naam van het label voor het gedetecteerde veld.
    - **Veldwaarde**: De waarde voor het gedetecteerde veld.

> [!div class="mx-imgBorder"]
> ![pagina voor tagging](./media/tutorial-ai-builder/select-fields-page.png)

### <a name="label-undetected-fields"></a>Niet-gedetecteerde velden labelen

Als het veld dat u wilt labelen niet automatisch door het model is gedetecteerd, kunt u een rechthoek rond de waarde ervan tekenen en een labelnaam in het weergegeven dialoogvenster typen.

## <a name="train-your-model"></a>Uw model trainen

1. Selecteer **Volgende** om de geselecteerde formuliervelden te controleren. Als alles er goed uitziet, selecteert u **Trainen** om uw model te trainen.

    > [!div class="mx-imgBorder"]
    > ![pagina voor training](./media/tutorial-ai-builder/summary-train-page.png)
1. Wanneer de training is voltooid, selecteert u **Ga naar de pagina Details** in het scherm **Training voltooid**.
## <a name="quick-test-your-model"></a>Snel uw model testen

Op de pagina Details kunt u uw model testen voordat u het publiceert of gebruikt:

1. Selecteer op de pagina Details **Snelle test**.
2. U kunt een document slepen en neerzetten of **Uploaden vanaf mijn apparaat** selecteren om uw testbestand te uploaden. De snelle test zou na slechts enkele seconden de resultaten moeten weergeven.
3. Als u klaar bent, kunt u **Opnieuw beginnen** selecteren om nog een test uit te voeren of **Sluiten** selecteren.

### <a name="troubleshooting-tips"></a>Tips voor probleemoplossing

Als u slechte resultaten of scores met een lage betrouwbaarheid voor bepaalde velden krijgt, kunt u de volgende tips proberen:

- Train opnieuw met formulieren met in elk veld verschillende waarden.
- Train opnieuw met een grotere set trainingsdocumenten. Hoe meer documenten u labelt, hoe beter AI Builder leert de velden te herkennen.
- U kunt PDF-bestanden optimaliseren door alleen bepaalde pagina's te selecteren om mee te trainen. Gebruik de optie **Afdrukken** > **Afdrukken naar PDF** om bepaalde pagina's in uw document te selecteren.

## <a name="publish-your-model"></a>Uw model publiceren

Als u tevreden bent met uw model, selecteert u **Publiceren** om het te publiceren. Wanneer het publiceren is voltooid, wordt uw model verhoogd naar het niveau **Gepubliceerd** en is het klaar voor gebruik.

> [!div class="mx-imgBorder"]
> ![pagina voor het publiceren van een model](./media/tutorial-ai-builder/model-page.png)

Nadat u uw model voor formulierverwerking hebt gepubliceerd, kunt u het gebruiken in een [Power Apps-canvas-app](/ai-builder/form-processor-component-in-powerapps) of in [Power Automate](/ai-builder/form-processing-model-in-flow).

## <a name="next-steps"></a>Volgende stappen

Volg de AI Builder-documentatie voor het gebruik van een model voor formulierverwerking.

* [Het onderdeel voor formulierverwerking gebruiken in Power Apps](/ai-builder/form-processor-component-in-powerapps)
* [Een model voor formulierverwerking gebruiken in Power Automate](/ai-builder/form-processing-model-in-flow)
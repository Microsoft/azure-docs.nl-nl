---
title: Model-en leer instellingen beheren-persoonlijker
description: De door de machine geleerde model-en leer instellingen kunnen worden geëxporteerd voor back-up in uw eigen broncode beheersysteem.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 48e954eab9da5dfc638f93a7c4e55c675c4f2ac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97797244"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Model-en leer instellingen beheren

De door de machine geleerde model-en leer instellingen kunnen worden geëxporteerd voor back-up in uw eigen broncode beheersysteem.

## <a name="export-the-personalizer-model"></a>Het Personalr model exporteren

Bekijk in het gedeelte resource beheer voor **model-en leer instellingen** het maken van het model en de datum van de laatste update en exporteer het huidige model. U kunt de Azure Portal of de Personaler-Api's gebruiken voor het exporteren van een model bestand voor archiverings doeleinden.

![Huidig Personal model exporteren](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Gegevens voor uw leer proces wissen

1. Selecteer in de Azure Portal voor uw Personaler-resource op de pagina **model-en leer instellingen** de optie **gegevens wissen**.
1. Schakel alle selectie vakjes 3 in om alle gegevens te wissen en de learning-lus opnieuw in te stellen op de oorspronkelijke staat.

    ![In Azure Portal gegevens wissen uit de resource personaliseren.](./media/settings/clear-data-from-personalizer-resource.png)

    |Waarde|Doel|
    |--|--|
    |Vastgelegde persoonlijke instellingen en belonings gegevens.|Deze logboek registratie gegevens worden gebruikt in offline-evaluaties. Wis de gegevens als u uw resource opnieuw instelt.|
    |Stel het persoonlijke model opnieuw in.|Dit model wordt bij elke retraining gewijzigd. Deze frequentie van training is opgegeven bij **Upload model frequentie** op de **configuratie** pagina. |
    |Stel het trainings beleid in op standaard.|Als u het trainings beleid hebt gewijzigd als onderdeel van een offline-evaluatie, wordt dit opnieuw ingesteld op het oorspronkelijke leer beleid.|

1. Selecteer **geselecteerde gegevens wissen** om het wissen te starten. De status wordt gerapporteerd in azure-meldingen in de rechter navigatie.

## <a name="import-a-new-learning-policy"></a>Een nieuw leer beleid importeren

De [leer beleids](concept-active-learning.md#understand-learning-policy-settings) instellingen bepalen de _Hyper parameters_ van de model training. Voer een [offline-evaluatie](how-to-offline-evaluation.md) uit om een nieuw leer beleid te vinden.

1. Open de [Azure Portal](https://portal.azure.com)en selecteer uw persoonlijke resource.
1. Selecteer **model en leer instellingen** in het gedeelte **resource beheer** .
1. Selecteer voor de **leer instellingen voor importeren** het bestand dat u hebt gemaakt met de hierboven opgegeven JSON-indeling en selecteer vervolgens de knop **uploaden** .

    Wacht tot de melding dat het trainings beleid is geüpload.

## <a name="export-a-learning-policy"></a>Een leer beleid exporteren

1. Open de [Azure Portal](https://portal.azure.com)en selecteer uw persoonlijke resource.
1. Selecteer **model en leer instellingen** in het gedeelte **resource beheer** .
1. Selecteer de knop **Learning Settings exporteren** voor de **leer instellingen voor importeren** . Hiermee slaat `json` u het bestand op uw lokale computer op.

## <a name="next-steps"></a>Volgende stappen

[Analyseer uw leer proces met een offline-evaluatie](how-to-offline-evaluation.md)

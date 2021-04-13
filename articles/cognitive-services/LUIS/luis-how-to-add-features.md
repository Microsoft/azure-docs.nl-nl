---
title: Functies gebruiken om de LUIS woorden lijst te verbeteren
titleSuffix: Azure Cognitive Services
description: Gebruik Language Understanding (LUIS) om app-functies toe te voegen die de detectie of voor spelling van intenties en entiteiten die categorieën en patronen kunnen verbeteren
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c1a0fb15be92e25b576cd29e671eed3c6a5b948f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313263"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Functies gebruiken om het signaal van een woorden lijst te verhogen

U kunt functies toevoegen aan uw LUIS-app om de nauw keurigheid te verbeteren. Met functies kunt u LUIS dat bepaalde woorden en zinsdelen deel uitmaken van een app-domein woordenlijst.

Bekijk de [concepten](luis-concept-feature.md) om te begrijpen wanneer en waarom een functie wordt gebruikt.

## <a name="add-phrase-list-as-a-feature"></a>Woordgroepen lijst toevoegen als een functie

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Open uw app door de naam ervan op **mijn apps** -pagina te selecteren.
1. Selecteer **Build** en selecteer vervolgens **functies** in het linkerdeel venster van uw app.

1. Selecteer op de pagina **onderdelen** de optie **+ maken**.

1. Voer in het dialoog venster **nieuwe woordgroepen lijst maken** een naam in, bijvoorbeeld `Cities` . Voer in het vak **waarde** voor beelden van de steden in, bijvoorbeeld `Seattle` . U kunt één waarde per keer typen, of een reeks waarden gescheiden door komma's, en vervolgens op **Enter** drukken.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding van het toevoegen van de functie (woordgroepen lijst) plaatsen](./media/luis-add-features/add-phrase-list-cities.png)

    Zodra u voldoende waarden hebt ingevoerd voor LUIS, worden er suggesties weer gegeven. U kunt **+ alle** voorgestelde waarden toevoegen of afzonderlijke voor waarden selecteren.

1. Behoud **deze waarden zijn verwisselbaar** ingeschakeld als de woord groepen door elkaar kunnen worden gebruikt. De lijst functie van de omwisselende woordgroepen fungeert als een lijst met synoniemen voor training. De lijst met niet-Verwissel bare woordgroepen fungeert als afzonderlijke functies voor training (wat betekent dat de functies vergelijkbaar zijn, maar dat de intentie verandert wanneer u zinsdelen verwisselt).

1. De lijst met zinsdelen kan worden toegepast op de volledige app met de **algemene** instelling of op een specifiek model (intentie of entiteit). Als u de woordgroepen lijst maakt als een _functie_ van een intentie of entiteit, wordt de wissel knop niet ingesteld voor globaal. In dit geval is de betekenis van de wissel knop dat de functie alleen lokaal is voor dat model, dus _niet globaal_ voor de toepassing.

1. Selecteer **Gereed**. De nieuwe functie wordt toegevoegd aan de pagina **ml-functies** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> U kunt een woordgroepen lijst verwijderen of deactiveren op de contextuele werk balk op de pagina **ml-functies** .

## <a name="global-phrase-list-applies-to-entire-app"></a>Algemene woordgroepen lijst is van toepassing op volledige app

Een woordgroepen lijst moet worden toegepast op de intentie of entiteit waarvoor het is bedoeld om u te helpen, maar het kan voor komen dat een woordgroepen lijst moet worden toegepast op de hele app als een **globale** functie.

Selecteer op de pagina ML-onderdelen de lijst woordgroepen en selecteer vervolgens **globaal maken** in de bovenste contextuele werk balk.

## <a name="model-as-a-feature"></a>Model als onderdeel

Een entiteit kan een [functie zijn voor een intentie of entiteit](luis-concept-feature.md).

Als u een entiteit wilt toevoegen als een functie voor een opzet, selecteert u de intentie op de pagina intenties en selecteert u **+ functie toevoegen** boven de contextuele werk balk. De lijst bevat alle woordgroepen lijsten en entiteiten die als functies kunnen worden toegepast.

Als u een entiteit wilt toevoegen als een functie aan een andere entiteit, kunt u de functie toevoegen op de detail pagina Details met behulp van het [entiteits palet](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) of u kunt [de functie toevoegen](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) op de pagina Details van entiteit.

## <a name="next-steps"></a>Volgende stappen

Na het toevoegen, bewerken, verwijderen of deactiveren van een functie, [Train en test u de app](luis-interactive-test.md) opnieuw om te zien of de prestaties verbeteren.

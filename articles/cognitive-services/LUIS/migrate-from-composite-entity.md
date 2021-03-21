---
title: Upgrade samengestelde entiteit-LUIS
description: Upgrade samengestelde entiteit naar machine learning-entiteit met een upgrade proces in de LUIS-Portal.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 46e9ece70d9f980065c719ee1205eb46591b45c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025239"
---
# <a name="upgrade-composite-entity-to-machine-learning-entity"></a>Upgrade van samengestelde entiteit naar machine-learning-entiteit

Voer een upgrade uit voor de samengestelde entiteit naar de machine learning-entiteit om een entiteit te bouwen die meer complete voor spellingen ontvangt met een betere opbouw baarheid voor het opsporen van fouten in de entiteit.

## <a name="current-version-model-restrictions"></a>Huidige versie model beperkingen

Het upgrade proces maakt machine learning-entiteiten, op basis van de bestaande samengestelde entiteiten die in uw app zijn gevonden, naar een nieuwe versie van uw app. Dit omvat de onderliggende entiteiten en rollen van de samengestelde entiteit. Het proces verandert ook de labels in voor beeld uitingen om de nieuwe entiteit te gebruiken.

## <a name="upgrade-process"></a>Upgrade proces

Het upgrade proces:
* Hiermee maakt u een nieuwe machine learning-entiteit voor elke samengestelde entiteit.
* Onderliggende entiteiten:
    * Als onderliggende entiteit alleen wordt gebruikt in samengestelde entiteit, wordt deze alleen toegevoegd aan de machine learning-entiteit.
    * Als onderliggende entiteit wordt gebruikt in Composite _en_ als een afzonderlijke entiteit (gelabeld in voor beeld uitingen), wordt deze toegevoegd aan de versie als een entiteit en als subentiteit aan de nieuwe machine learning-entiteit.
    * Als de onderliggende entiteit een rol gebruikt, wordt elke rol geconverteerd naar een subentiteit met dezelfde naam.
    * Als de onderliggende entiteit een niet-machine learning-entiteit is (reguliere expressie, lijst entiteit of vooraf samengestelde entiteit), wordt een nieuwe subentiteit gemaakt met dezelfde naam en de nieuwe subentiteit heeft een functie met de niet-machine learning-entiteit waarvoor de vereiste functie is toegevoegd.
* Namen worden bewaard, maar moeten uniek zijn op hetzelfde niveau van subentiteiten/dochter items. Raadpleeg de [unieke naamgevings limieten](./luis-limits.md#name-uniqueness).
* Labels in voor beelden uitingen worden overgeschakeld naar een nieuwe machine learning-entiteit met subentiteiten.

Gebruik de volgende tabel om te begrijpen hoe uw model wordt gewijzigd:

|Oud object|Nieuw object|Notities|
|--|--|--|
|Samengestelde entiteit|machine learning-entiteit met structuur|Beide objecten zijn bovenliggende objecten.|
|De onderliggende entiteit van de samengestelde eenheid is een **eenvoudige entiteit**|subentiteit|Beide objecten zijn onderliggende objecten.|
|De onderliggende entiteit van de samengestelde eenheid is een **vooraf gebouwde entiteit** zoals een getal|subentiteit met de naam van de vooraf gedefinieerde entiteit, zoals nummer, en subentiteit bevat een _functie_ van een vooraf gebouwde nummer entiteit waarvoor de optie voor de beperking is ingesteld op _waar_.|subentiteit bevat een functie met een beperking op het niveau van de subentiteit.|
|De onderliggende entiteit van de samengestelde eenheid is een **vooraf samengestelde entiteit** , zoals een nummer, en de vooraf samengestelde entiteit heeft een **rol**|subentiteit met de naam Role en subentiteit bevat een functie van een vooraf opgebouwde nummer entiteit waarvoor de optie voor de beperking is ingesteld op waar.|subentiteit bevat een functie met een beperking op het niveau van de subentiteit.|
|Rol|subentiteit|De rolnaam wordt de naam van de subentiteit. De subentiteit is een direct Descendant van de machine learning-entiteit.|

## <a name="begin-upgrade-process"></a>Upgrade proces starten

Voordat u een update uitvoert, moet u het volgende doen:

* Versies wijzigen als u niet de juiste versie hebt om te upgraden


1. Begin het upgrade proces vanuit de melding of u kunt wachten tot de volgende geplande prompt.

    > [!div class="mx-imgBorder"]
    > ![Upgrade van meldingen starten](./media/update-composite-entity/notification-begin-update.png)

1. Selecteer **Nu bijwerken** in het pop-upvenster.

1. Bekijk **wat er gebeurt wanneer u gegevens bijwerkt** en selecteer vervolgens **door gaan**.

1. Selecteer de samengestelde entiteiten in de lijst die u wilt bijwerken en selecteer vervolgens **door gaan**.

1. U kunt alle niet-getrainde wijzigingen van de huidige versie naar de bijgewerkte versie verplaatsen door het selectie vakje in te scha kelen.

1. Selecteer **door gaan** om het upgrade proces te starten.

1. De voortgangs balk geeft de status van het upgrade proces aan.

1. Wanneer het proces is voltooid, bevindt u zich in een nieuwe getrainde versie met de nieuwe machine learning-entiteiten. Selecteer **uw nieuwe entiteiten proberen** om de nieuwe entiteit te zien.

    Als de upgrade of training is mislukt voor de nieuwe entiteit, bevat een melding meer informatie.

1. Op de pagina entiteits lijst worden de nieuwe entiteiten gemarkeerd met **Nieuw** naast de type naam.

## <a name="next-steps"></a>Volgende stappen

* [Auteurs en samenwerkers](luis-how-to-collaborate.md)
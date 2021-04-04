---
title: Leer gedrag configureren
description: In de modus van de les krijgt u een vertrouwen in de Personaler-service en de mogelijkheden van de machine learning en worden er metrische gegevens gegeven die de service verzendt, zonder risico voor online verkeer.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94579288"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Het leer gedrag van Personaler configureren

In de les [modus](concept-apprentice-mode.md) krijgt u vertrouwen en vertrouwen in de personaler-service en de mogelijkheden van de machine learning. u kunt er zeker van zijn dat de service informatie verzendt die kan worden geleerd zonder dat dit een risico voor online verkeer is.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>De modus leerlingen configureren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)voor uw persoonlijke resource.

1. Selecteer op de pagina **configuratie** op het tabblad **leer gedrag** de optie **basislijn actie retour neren, leer als leerlingen** en selecteer vervolgens **Opslaan**.

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding van het leer gedrag van de leerlingen-modus configureren in Azure Portal](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Wijzigingen in de bestaande toepassing

De bestaande toepassing hoeft niet te wijzigen hoe acties worden geselecteerd om te worden weer gegeven of hoe de toepassing de waarde **bepaalt, wat** de actie is. De enige wijziging van de toepassing is mogelijk de volg orde van de acties die worden verzonden naar de classificatie-API van Personaler. De actie die door uw toepassing wordt weer gegeven, wordt als _eerste actie_ in de lijst met acties verzonden. De [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) gebruikt deze eerste actie om uw personalr model te trainen.

### <a name="configure-your-application-to-call-the-rank-api"></a>Uw toepassing configureren voor het aanroepen van de classificatie-API

Als u persoonlijker wilt toevoegen aan uw toepassing, moet u de prioriteits-en belonings-Api's aanroepen.

1. Voeg de [positie-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) -aanroep toe na het punt in uw bestaande toepassings logica, waar u de lijst met acties en de bijbehorende functies bepaalt. De eerste actie in de lijst met acties moet de actie zijn die door uw bestaande logica is geselecteerd.

1. Configureer uw code om de actie weer te geven die is gekoppeld aan de **Actie-id** van de positie-API-reactie.

### <a name="configure-your-application-to-call-reward-api"></a>Uw toepassing configureren voor het aanroepen van belonings-API

1. Gebruik uw bestaande bedrijfs logica om de **beloning** van de weer gegeven actie te berekenen. De waarde moet tussen 0 en 1 liggen. Verstuur deze vergoeding naar Personaler met behulp van de [belonings-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward). De belonings waarde wordt niet onmiddellijk verwacht en kan worden vertraagd voor een bepaalde periode, afhankelijk van uw bedrijfs logica.

1. Als u de beloning niet binnen de geconfigureerde **wacht tijd** van de beloning retourneert, wordt in plaats daarvan de standaard beloning gebruikt.

## <a name="evaluate-apprentice-mode"></a>De leerling-modus evalueren

Bekijk in de Azure Portal op de pagina met **beoordelingen** voor uw persoonlijker resource de **huidige prestaties van het leer gedrag**.

> [!div class="mx-imgBorder"]
> ![Scherm opname van het beoordelen van de evaluatie van het leer gedrag van de leerlingen-modus in Azure Portal](media/settings/evaluate-apprentice-mode.png)

De leerling-modus biedt de volgende **metrische gegevens** over de evaluatie:
* **Basis lijn**: gemiddelde beloning: gemiddelde beloningen van de standaard waarde van de toepassing (basis lijn).
* **Persoonlijkere – gemiddelde beloning**: gemiddelde van het totale aantal beloningen personaler zou mogelijk zijn bereikt.
* **Belonings prestaties ten opzichte van de meest recente 1000 gebeurtenissen**: verhouding van de prijs van de basis lijn en personalisatie, genormaliseerd via de meest recente 1000-gebeurtenissen.

## <a name="switch-behavior-to-online-mode"></a>Gedrag van Switch naar online modus

Wanneer u bepaalt dat Personaler is getraind met een gemiddelde van 75-85%, is het model klaar om over te scha kelen naar de online modus.

Selecteer in de Azure Portal voor uw persoonlijke resource op de pagina **configuratie** op het tabblad **leer gedrag** **de optie de beste actie retour neren** en selecteer vervolgens **Opslaan**.

U hoeft geen wijzigingen aan te brengen in de classificatie-en belonings-API-aanroepen.

## <a name="next-steps"></a>Volgende stappen

* [Model- en leerinstellingen beheren](how-to-manage-model.md)

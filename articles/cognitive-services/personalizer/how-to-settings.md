---
title: Personalizer configureren
description: Service configuratie bevat de manier waarop de service beloningen behandelt, hoe vaak de service bekijkt, hoe vaak het model opnieuw wordt getraind en hoeveel gegevens worden opgeslagen.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91292605"
---
# <a name="configure-personalizer-learning-loop"></a>De learning-lus van Personaler configureren

Service configuratie bevat de manier waarop de service beloningen behandelt, hoe vaak de service bekijkt, hoe vaak het model opnieuw wordt getraind en hoeveel gegevens worden opgeslagen.

Configureer de learning-lus op de pagina **configuratie** , in de Azure portal voor die persoonlijker-resource.

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>Configuratie wijzigingen plannen

Omdat sommige configuratie wijzigingen [uw model opnieuw instellen](#settings-that-include-resetting-the-model), moet u de configuratie wijzigingen plannen.

Als u de leerling- [modus](concept-apprentice-mode.md)wilt gebruiken, moet u de configuratie van uw persoonlijker controleren voordat u overschakelt naar de modus van de leerling.

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>Instellingen voor het opnieuw instellen van het model

Met de volgende acties wordt een retraining van het model geactiveerd met behulp van gegevens die beschikbaar zijn in de afgelopen twee dagen.

* Communityleden
* Verkennen

Als u al uw gegevens wilt [wissen](how-to-manage-model.md) , gebruikt u de pagina **model-en leer instellingen** .

## <a name="configure-rewards-for-the-feedback-loop"></a>Beloningen voor de feedback-lus configureren

Configureer de service voor het gebruik van beloningen van uw leer proces. Als u de volgende waarden wijzigt, wordt het huidige personalisatie model opnieuw ingesteld en wordt het opnieuw getraind met de laatste 2 dagen aan gegevens.

> [!div class="mx-imgBorder"]
> ![De belonings waarden voor de feedback-lus configureren](media/settings/configure-model-reward-settings.png)

|Waarde|Doel|
|--|--|
|Wachttijd voor beloning|Hiermee stelt u de periode in waarin Personaler belonings waarden voor een classificatie oproep verzamelt, vanaf het moment dat de classificatie oproep plaatsvindt. Deze waarde wordt ingesteld door te vragen: ' hoe lang moet Personaler wachten op de aanroepen van beloningen? ' Elke beloning die wordt binnengekomen nadat dit venster wordt geregistreerd, wordt vastgelegd, maar niet voor Learning.|
|Standaardbeloning|Als er geen belonings oproep wordt ontvangen door Personaler tijdens het venster belonings wachttijd dat is gekoppeld aan een rang nummer, zal Personaler de standaard beloning toewijzen. In de meeste scenario's is de standaard beloning standaard nul (0).|
|Belonings aggregatie|Als er meerdere beloningen worden ontvangen voor dezelfde positie-API-aanroep, wordt deze samenvoegings methode gebruikt: **Sum** of **oudste**. De eerste keer kiest de eerste ontvangen Score en wordt de rest verwijderd. Dit is handig als u een unieke beloning wilt voor mogelijk dubbele aanroepen. |

Nadat u deze waarden hebt gewijzigd, moet u **Opslaan** selecteren.

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>Verkennen configureren zodat de learning-lus kan worden aangepast

Personalisatie kan nieuwe patronen ontdekken en de wijzigingen in het gebruikers gedrag in de loop van de tijd aanpassen door alternatieven te gebruiken in plaats van de voor spelling van het getrainde model. De waarde voor **verkennen** bepaalt welk percentage van de positie aanroepen wordt beantwoord met verkennen.

Als u deze waarde wijzigt, wordt het huidige personalisatie model opnieuw ingesteld en wordt het opnieuw getraind met de laatste 2 dagen aan gegevens.

![De waarde voor verkennen bepaalt welk percentage van de Rangings aanroepen met verkennen wordt beantwoord](media/settings/configure-exploration-setting.png)

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan** selecteren.

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>De model update frequentie voor model training configureren

Met de frequentie van de **model updates** wordt aangegeven hoe vaak het model wordt getraind.

|Frequentie-instelling|Doel|
|--|--|
|1 minuut|Update frequenties van één minuut zijn handig bij het **opsporen van fouten in** de code van een toepassing met behulp van personaler, het uitvoeren van demo's of het interactief testen van machine learning aspecten.|
|15 minuten|High model-update frequenties zijn nuttig voor situaties waarin u de wijzigingen in het gedrag van gebruikers **nauw keurig wilt bijhouden** . Voor beelden zijn onder andere sites die worden uitgevoerd op Live nieuws, virale inhoud of Live-product biedingen. In deze scenario's kunt u een frequentie van 15 minuten gebruiken. |
|1 uur|Voor de meeste gebruiks voorbeelden is een lagere update frequentie van kracht.|

![Met de model update frequentie stelt u in hoe vaak een nieuw Personalr model opnieuw wordt getraind.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan** selecteren.

## <a name="data-retention"></a>Gegevensretentie

**Gegevens retentie periode** instellen hoeveel dagen persoonlijke gegevens logboeken worden bewaard. Eerdere gegevens logboeken zijn vereist voor het uitvoeren van [offline-evaluaties](concepts-offline-evaluation.md), die worden gebruikt om de effectiviteit van personaler te meten en het trainings beleid te optimaliseren.

Nadat u deze waarde hebt gewijzigd, moet u **Opslaan** selecteren.



## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het beheren van uw model](how-to-manage-model.md)

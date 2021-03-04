---
title: 'Snelstartgids: een app HoloLens maken met Unity en MRTK'
description: In deze Quick Start leert u hoe u een app voor het maken van een HoloLens-eenheid maakt met behulp van MRTK en object ankers.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 1fd42f7b2da82da17dc19f2a57ea9b64f78f3fe0
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049695"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Snelstartgids: een HoloLens-app maken met Azure object-ankers, in eenheid met MRTK

In deze Quick Start maakt u een unit HoloLens-app die gebruikmaakt van [Azure object-ankers](../overview.md). Azure object-ankers is een beheerde Cloud service waarmee 3D-assets naar AI-modellen worden geconverteerd waarmee objecten met gemengde realiteiten voor de HoloLens kunnen worden ingeschakeld. Wanneer u klaar bent, hebt u een app HoloLens gemaakt met unit met een eenheid waarmee objecten in de fysieke wereld kunnen worden gedetecteerd.

U leert het volgende:

> [!div class="checklist"]
> * Build-instellingen voor Unity voorbereiden.
> * Het HoloLens Visual Studio-project exporteren.
> * Implementeer de app en voer deze uit op een HoloLens 2-apparaat.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Voorbeeldproject openen

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Open het project in Unity `quickstarts/apps/unity/mrtk` .

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Wanneer u in het dialoog venster ' TMP-import programma ' wordt gevraagd om TextMesh Pro-resources te importeren, selecteert u ' TMP-kernen importeren '.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="TextMesh Pro-resources importeren":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Schakel het apparaat in, selecteer **alle apps**, zoek de app en start deze. Na het openings scherm van de eenheid ziet u een wit selectie kader. U kunt uw hand gebruiken om het selectie kader te verplaatsen, schalen of draaien. Plaats het vak voor het object dat u wilt detecteren.

Open het <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menu hand</a> en selecteer **Lock SearchArea** om verdere verplaatsing van het selectie kader te voor komen. Selecteer **Zoeken starten** om object detectie te starten. Wanneer het object wordt gedetecteerd, wordt een net weer gegeven op het object. Details van een gedetecteerd exemplaar worden weer gegeven op het scherm, zoals bijgewerkte tijds tempel en dekkings verhouding voor Opper vlakken. Selecteer **stoppen met zoeken** om het bijhouden te stoppen en alle gedetecteerde instanties worden verwijderd.

#### <a name="the-app-menus"></a>De app-menu's

U kunt ook andere acties uitvoeren met behulp van het menu met de <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">hand</a>.

##### <a name="primary-menu"></a>Primair menu

* **Zoek actie starten/stoppen** : Hiermee wordt het proces voor object detectie gestart of gestopt.
* **Ruimtelijke toewijzing in-/uitschakelen** : weer gave van ruimtelijke toewijzing tonen/verbergen. Deze optie kan worden gebruikt om fouten op te lossen als de scan is voltooid of niet.
* **Instellingen voor vastleggen** : Hiermee schakelt u de activering van het menu instellingen voor traceren in.
* **Instellingen voor het zoek gebied** : Hiermee schakelt u de activering van het menu instellingen van het zoek gebied in.
* **Tracering starten** : diagnostische gegevens vastleggen en opslaan op het apparaat. Meer informatie vindt u in sectie problemen bij het **detecteren van fouten en het vastleggen van diagnostische gegevens**.
* **Upload tracering** : diagnostische gegevens uploaden naar de object ankers-service. Een gebruiker moet zijn abonnements account opgeven in `subscription.json` en uploaden naar de `LocalState` map. Hieronder vindt u een voor beeld van een `subscription.json` bestand.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Het primaire hand menu van Unity":::

##### <a name="tracker-settings-menu"></a>Menu instellingen voor vastleggen

* **Hoge nauw keurigheid** : een experimentele functie die wordt gebruikt om een nauw keurigere pose te krijgen. Als u deze optie inschakelt, zijn er meer systeem bronnen nodig tijdens de object detectie. De object-mesh wordt in deze modus roze gerenderd. Selecteer deze knop opnieuw om terug te scha kelen naar de modus normaal volgen.
* **Beperkte verticale uitlijning** : wanneer deze functie is ingeschakeld, kan een object worden gedetecteerd op een niet-verticale hoek. Dit is handig voor het detecteren van objecten op hellingen.
* **Schaal wijziging toestaan** : Hiermee kunt u de grootte van het gedetecteerde object wijzigen op basis van milieu gegevens.
* **Schuif regelaar dekkings graad** : Hiermee past u het deel van de Opper vlakken aan dat moet overeenkomen met de tracker om een object te detecteren.  Met lagere waarden kan de tracker beter objecten detecteren die lastig zijn om de HoloLens-Sens oren te detecteren, zoals donkere objecten of zeer reflecterende objecten. Bij hogere waarden wordt de frequentie van onregelmatige detecties verminderd.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Het menu hands-vastleggen voor Unity":::

##### <a name="search-area-settings-menu"></a>Menu instellingen voor gebied zoeken

* **Zoek gebied vergren delen** -selectie kader vergren delen om onbedoelde verplaatsing door handen te voor komen.
* **Zoek gebied automatisch aanpassen** : Hiermee kan het zoek gebied zichzelf verplaatsen tijdens de object detectie.
* **Recyclen-net** : rondt door de geladen netten in het zoek gebied te visualiseren.  Met deze optie kunnen gebruikers het zoekvak uitlijnen zodat er moeilijk objecten kunnen worden gedetecteerd.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Reactief menu voor Unity-zoek gebied":::

Voor beeld `subscription.json` :

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Concepten: overzicht van de SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Veelgestelde vragen](../faq.md)

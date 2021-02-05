---
title: Een model weergeven met Unity
description: Quickstart waarin de stappen voor het genereren van een model worden beschreven
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 525872ca3ad2558c327b7b856254319d3db2dc7f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593990"
---
# <a name="quickstart-render-a-model-with-unity"></a>Quickstart: Een model weergeven met Unity

In deze quickstart leest u hoe u een Unity-voorbeeld uitvoert, waarmee een ingebouwd model extern wordt gegenereerd met behulp van de ARR-service (Azure Remote Rendering).

We laten hier de ARR API zelf of het instellen van een nieuw Unity-project verder buiten beschouwing. Deze onderwerpen worden behandeld in [Zelfstudie: Extern gegenereerde modellen bekijken](../tutorials/unity/view-remote-models/view-remote-models.md).

In deze snelstart leert u het volgende:
> [!div class="checklist"]
>
>* De lokale ontwikkelomgeving instellen
>* De ARR Quickstart-voorbeeld-app voor Unity ophalen en ontwikkelen
>* Een model genereren in de ARR Quickstart-voorbeeld-app

## <a name="prerequisites"></a>Vereisten

Als u toegang wilt krijgen tot de Azure Remote Rendering-service, moet u eerst [een account maken](../how-tos/create-an-account.md).

De volgende software moet zijn geïnstalleerd:

* Windows SDK 10.0.18362.0 [(download)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* De nieuwste versie van Visual Studio 2019 [(download)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools voor Mixed Reality](/windows/mixed-reality/install-the-tools). Met name de volgende *workload*-installaties zijn verplicht:
  * **Desktopontwikkeling met C++**
  * **Universal Windows Platform (UWP)-ontwikkeling**
* Git [(downloaden)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(download)](https://unity3d.com/get-unity/download)
  * Installeer de volgende modules in Unity:
    * **UWP**: ondersteuning voor UWP-builds (Universeel Windows-platform)
    * **IL2CPP**: ondersteuning voor Windows-builds (IL2CPP)

## <a name="clone-the-sample-app"></a>De voorbeeld-app klonen

Open een opdrachtprompt (typ `cmd` in het menu Start van Windows) en ga naar een map waarin u het ARR-voorbeeldproject wilt opslaan.

Voer de volgende opdrachten uit:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Met de laatste opdracht maakt u een submap in de ARR-map die de verschillende voorbeeldprojecten voor Azure Remote Rendering bevat.

De quickstart-voorbeeld-app voor Unity vindt u in de submap *Unity/Quickstart*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Een model genereren met het Unity-voorbeeldproject

Open de Unity Hub en voeg het voorbeeldproject toe. Dit is de map *ARR\azure-remote-rendering\Unity\Quickstart*.
Open het project. Sta Unity zo nodig toe om het project te upgraden naar de versie die u hebt geïnstalleerd.

Het standaardmodel dat we genereren, is een [ingebouwd voorbeeldmodel](../samples/sample-model.md). We laten u in de [volgende quickstart](convert-model.md) zien hoe u een aangepast model converteert met de ARR-conversieservice.

### <a name="enter-your-account-info"></a>Voer uw accountgegevens in

1. Ga in de browser voor Unity-activa naar de map *Scènes* en open de scène **Quickstart**.
1. Selecteer in *Hiërarchie* het gameobject **RemoteRendering**.
1. Voer in de *Inspector* uw [accountreferenties](../how-tos/create-an-account.md) in. Als u nog geen account hebt, [maakt u er een](../how-tos/create-an-account.md).

![ARR-accountgegevens](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Stel **RemoteRenderingDomain** in op `<region>.mixedreality.azure.com` , `<region>` waarbij [een van de beschik bare regio's bij u in de buurt](../reference/regions.md)is. \
> Stel **AccountDomain** in op [account domein](../how-tos/create-an-account.md#retrieve-the-account-information) zoals wordt weer gegeven in azure Portal.

Later willen we dit project implementeren op een HoloLens-apparaat en vanaf dat apparaat verbinding maken met de Azure Remote Rendering-service. Omdat er geen eenvoudige manier is om de referenties op het apparaat in te voeren, worden in het quickstart-voorbeeld **de referenties opgeslagen in de scène Unity**.

> [!WARNING]
> Check het project niet met uw opgeslagen referenties in een opslagplaats in waar geheime aanmeldingsgegevens kunnen weglekken.

### <a name="create-a-session-and-view-the-default-model"></a>Een sessie maken en het standaardmodel weergeven

Druk op de knop **Afspelen** van Unity om de sessie te starten. Er wordt een overlay weergegeven met statustekst, onderaan de viewport in het deelvenster *Game*. De sessie zal verschillende statussen doorlopen. In de status **Wordt gestart** wordt de server opgestart, hetgeen enkele minuten duurt. Wanneer dit is gelukt, gaat de status over naar **Gereed**. De sessie gaat vervolgens over in de status **Verbinding maken**, waarin wordt geprobeerd de renderingruntime op die server te bereiken. Wanneer dit is gelukt, gaat het voorbeeld over naar de status **Verbonden**. Op dat moment wordt begonnen met het downloaden van het model voor rendering. Vanwege de grootte van het model kan het downloaden een paar minuten duren. Vervolgens wordt het extern gegenereerde model weergegeven.

![Uitvoer uit het voorbeeld](media/arr-sample-output.png)

Gefeliciteerd! U bekijkt nu een model dat extern is gegenereerd.

## <a name="inspecting-the-scene"></a>De scène controleren

Wanneer de verbinding voor externe rendering actief is, wordt het deelvenster Inspector bijgewerkt met aanvullende statusinformatie: ![Unity-voorbeeld wordt afgespeeld](./media/arr-sample-configure-session-running.png)

U kunt de scèneafbeelding nu verkennen door het nieuwe knooppunt te selecteren en op **Onderliggende items tonen** in de Inspector te klikken.

![Unity-hiërarchie](./media/unity-hierarchy.png)

Er bevindt zich een [snijvlak](../overview/features/cut-planes.md)object in de scène. Probeer het in de eigenschappen in te schakelen en te verplaatsen:

![Het snijvlak wijzigen](media/arr-sample-unity-cutplane.png)

Als u transformaties wilt synchroniseren, klikt u op **Nu synchroniseren** of schakelt u de optie **Elk frame synchroniseren** in. Voor onderdeeleigenschappen volstaat het dat u deze wijzigt.

## <a name="next-steps"></a>Volgende stappen

In de volgende quickstart implementeren we het voorbeeld op een HoloLens-apparaat om het extern gegenereerde model in de oorspronkelijke grootte weer te geven.

> [!div class="nextstepaction"]
> [Snelstart: Unity-voorbeeld implementeren in HoloLens](deploy-to-hololens.md)

Het voorbeeld kan ook worden geïmplementeerd op een desktopcomputer.

> [!div class="nextstepaction"]
> [Snelstart: Unity-voorbeeld implementeren op een desktopcomputer](deploy-to-desktop.md)
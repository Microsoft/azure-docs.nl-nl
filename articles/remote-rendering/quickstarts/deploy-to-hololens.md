---
title: Unity-voorbeeld implementeren in HoloLens
description: Snelstart die laat zien hoe u het Unity-voorbeeld kunt ophalen op de HoloLens
author: jakrams
ms.author: jakras
ms.date: 02/14/2020
ms.topic: quickstart
ms.openlocfilehash: a5a44b9d27901fcd00f8813096de6bbae5f2310d
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179942"
---
# <a name="quickstart-deploy-unity-sample-to-hololens"></a>Quickstart: Unity-voorbeeld implementeren in HoloLens

In deze snelstart wordt beschreven hoe u de voorbeeld-app voor Unity implementeert en uitvoert op een HoloLens 2.

In deze snelstart leert u het volgende:

> [!div class="checklist"]
>
>* De voorbeeld-app uit de snelstart bouwen voor een HoloLens
>* Het voorbeeld implementeren op het apparaat
>* Het voorbeeld uitvoeren op het apparaat

## <a name="prerequisites"></a>Vereisten

In deze snelstart wordt het voorbeeldproject geïmplementeerd vanuit [Snelstart: een model weergeven met Unity](render-model.md).

Controleer of uw referenties correct zijn opgeslagen bij de scène en u verbinding kunt maken met een sessie vanuit de Unity-editor.

## <a name="build-the-sample-project"></a>Het voorbeeldproject bouwen

1. Open *Bestand -> Build-instellingen*.
1. Wijzig het *Platform* naar **Universeel Windows-platform**
1. Stel *Doelapparaat* in op **HoloLens**
1. Stel *Architectuur* in op **ARM64**
1. Stel *Buildtype* in op **D3D-project**\
    ![Build-instellingen](./media/unity-build-settings.png)
1. Selecteer **Overschakelen naar platform**
1. Wanneer u op **Build** drukt (of 'Bouwen en uitvoeren'), wordt u gevraagd om een map te selecteren waarin de oplossing moet worden opgeslagen
1. Open de gegenereerde **Quickstart.sln** met Visual Studio
1. Wijzig de configuratie in **Release** en **ARM64**
1. Schakel de foutopsporingsmodus in op **Externe machine**\
    ![Configuratie van de oplossing](media/unity-deploy-config.png)
1. De oplossing bouwen
1. Ga voor het project 'QuickStart' naar *Eigenschappen > Foutopsporing*
    1. Controleer of de configuratie *Release* actief is
    1. Stel *Op te starten foutopsporingsprogramma* in op **Externe computer**
    1. Wijzig *Computernaam* in het **IP-adres van uw HoloLens**

## <a name="launch-the-sample-project"></a>Het voorbeeldproject openen

1. Sluit de HoloLens via een USB-kabel aan op uw pc.
1. Start de foutopsporing in Visual Studio (F5). De app wordt automatisch geïmplementeerd op het apparaat.

De voorbeeld-app moet worden gestart en vervolgens een nieuwe sessie starten. Na een tijdje is de sessie gereed en wordt het extern gerenderde model weergegeven.
Als u het voorbeeld een tweede keer wilt starten, kunt u dit ook vinden in het startmenu van HoloLens.

## <a name="next-steps"></a>Volgende stappen

In de volgende snelstart gaan we kijken naar het converteren van een aangepast model.

> [!div class="nextstepaction"]
> [Snelstart: een model converteren voor rendering](convert-model.md)

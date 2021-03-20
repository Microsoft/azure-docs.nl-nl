---
title: Een hulp programma gebruiken om DTDL modellen te ontwerpen en valideren | Microsoft Docs
description: Installeer de DTDL-editor voor Visual Studio code of Visual Studio 2019 en gebruik deze om IoT Plug en Play-modellen te ontwerpen.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 40d1ae4da07e159c24970c065d1c39e22b89a29a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280205"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>De DTDL-hulpprogram ma's voor ontwerpen installeren en gebruiken

[DTDL-modellen (Digital Apparaatdubbels Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) zijn json-bestanden. U kunt een extensie voor Visual Studio code of Visual Studio 2019 gebruiken om deze model bestanden te ontwerpen en te valideren.

## <a name="install-and-use-the-vs-code-extension"></a>De VS code-extensie installeren en gebruiken

De DTDL-extensie voor VS code voegt de volgende DTDL-ontwerp functies toe:

- Validatie van DTDL v2-syntaxis.
- IntelliSense, met inbegrip van AutoAanvullen, om u te helpen met de syntaxis van de taal.
- De mogelijkheid om interfaces vanuit het opdracht palet te maken.

Als u de DTDL-extensie wilt installeren, gaat u naar [DTDL editor voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). U kunt ook zoeken naar **DTDL** in de weer gave EXTENSIES in VS code.

Wanneer u de uitbrei ding hebt geïnstalleerd, kunt u deze gebruiken om u te helpen bij het maken van DTDL-model bestanden in VS code:

- De uitbrei ding biedt syntaxis validatie in DTDL-model bestanden, waarbij fouten worden gemarkeerd, zoals op de volgende scherm afbeelding wordt weer gegeven:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Model validatie in VS code":::

- Gebruik IntelliSense en automatisch aanvullen wanneer u DTDL-modellen bewerkt:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="IntelliSense gebruiken voor DTDL-modellen in VS code":::

- Maak een nieuwe DTDL-interface. Met de opdracht **DTDL: Create interface** wordt een JSON-bestand met een nieuwe interface gemaakt. De interface bevat voor beelden van telemetrie, eigenschappen en opdracht definities.

## <a name="install-and-use-the-visual-studio-extension"></a>De Visual Studio-extensie installeren en gebruiken

De DTDL-extensie voor Visual Studio 2019 voegt de volgende functies voor DTDL-ontwerp toe:

- Validatie van DTDL v2-syntaxis.
- IntelliSense, met inbegrip van AutoAanvullen, om u te helpen met de syntaxis van de taal.

Als u de DTDL-extensie wilt installeren, gaat u naar [DTDL taal ondersteuning voor VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). U kunt ook zoeken naar **DTDL** in **extensies beheren** in Visual Studio.

Wanneer u de uitbrei ding hebt geïnstalleerd, kunt u deze gebruiken om u te helpen bij het ontwerpen van DTDL-model bestanden in Visual Studio:

- De uitbrei ding biedt syntaxis validatie in DTDL-model bestanden, waarbij fouten worden gemarkeerd, zoals op de volgende scherm afbeelding wordt weer gegeven:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Model validatie in Visual Studio":::

- Gebruik IntelliSense en automatisch aanvullen wanneer u DTDL-modellen bewerkt:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="IntelliSense gebruiken voor DTDL-modellen in Visual Studio":::

## <a name="next-steps"></a>Volgende stappen

In dit procedure-artikel hebt u geleerd hoe u de DTDL-extensies voor Visual Studio code en Visual Studio 2019 kunt gebruiken om DTDL-model bestanden te ontwerpen en te valideren. Een voorgestelde volgende stap is om te leren hoe u [Azure IOT Explorer kunt gebruiken met uw modellen en apparaten](./howto-use-iot-explorer.md).

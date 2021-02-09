---
title: De Azure Cloud Shell editor gebruiken | Microsoft Docs
description: Overzicht van het gebruik van de Azure Cloud Shell editor.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 1e3ea9222b0f231250bde43fb86c07847ca4835e
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832332"
---
# <a name="using-the-azure-cloud-shell-editor"></a>De Azure Cloud Shell editor gebruiken

Azure Cloud Shell bevat een geïntegreerde bestands editor die is gebouwd op basis van de open-source- [Monaco-editor](https://github.com/Microsoft/monaco-editor). De Cloud Shell-editor ondersteunt functies, zoals het markeren van talen, het opdrachtpalet en een bestandsverkenner.

![Cloud Shell editor](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>De editor openen

Als u eenvoudig bestanden wilt maken en bewerken, start u de editor door `code .` uit te voeren in de Cloud Shell-terminal. Met deze actie wordt de editor geopend met uw actieve werkmapset in de terminal.

Als u een bestand rechtstreeks wilt openen voor snelle bewerking, voert u `code <filename>` uit om de editor te openen zonder de bestandsverkenner.

Als u de editor wilt openen via de gebruikersinterfaceknop, klikt u op het editor-pictogram `{}` op de werkbalk. Hiermee opent u de editor en maakt u van de `/home/<user>`-map de standaardmap voor de bestandsverkenner.

## <a name="closing-the-editor"></a>De editor sluiten

Als u de editor wilt sluiten, opent u het `...` paneel actie in de rechter bovenhoek van de editor en selecteert u `Close editor` .

![Editor sluiten](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Opdracht palet

Als u het opdracht palet wilt starten, gebruikt u de `F1` sleutel wanneer de focus is ingesteld op de editor. Het openen van het opdracht palet kan ook worden uitgevoerd via het deel venster actie.

![Cmd-palet](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Bijdragen aan de Monaco-editor

Ondersteuning voor taal markeringen in de Cloud Shell editor wordt ondersteund via de upstream-functionaliteit in het gebruik van Monarch-syntaxis definities van de [Monaco-editor](https://github.com/Microsoft/monaco-editor). Raadpleeg de gids voor de [Monaco-bijdrager](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)voor meer informatie over het maken van bijdragen.

## <a name="next-steps"></a>Volgende stappen

- [Probeer de Snelstartgids voor bash in Cloud Shell](quickstart.md)
- [Bekijk de volledige lijst met geïntegreerde Cloud Shell-hulpprogram ma's](features.md)

---
title: Azure dev Spaces-hulpprogram ma's bijwerken
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Meer informatie over het upgraden van de opdracht regel Programma's van Azure dev Spaces, Visual Studio code extension en de Visual Studio-extensie
keywords: Docker, Kubernetes, azure, AKS, Azure Container Service, containers
ms.openlocfilehash: f17643e6130abbc9d5da8b484144c95b0e803f33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199234"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Azure dev Spaces-hulpprogram ma's bijwerken

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Als er een nieuwe release is en u al Azure dev Spaces gebruikt, moet u mogelijk uw Azure dev Spaces-client hulpprogramma's upgraden.

## <a name="update-the-azure-cli"></a>De Azure CLI bijwerken

Wanneer u de nieuwste Azure CLI bijwerkt, krijgt u ook de meest recente versie van de CLI-extensie voor dev Spaces.

U hoeft de vorige versie niet te verwijderen, maar u hoeft alleen de juiste down load te vinden in [Azure cli](/cli/azure/install-azure-cli).


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>De extensie en opdracht regel Programma's voor ontwikkelaars ruimten bijwerken

Voer de volgende opdracht uit:

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>De VS code-extensie bijwerken

Als de extensie eenmaal is geïnstalleerd, wordt deze automatisch bijgewerkt. Mogelijk moet u de uitbrei ding opnieuw laden om de nieuwe functies te kunnen gebruiken. Open in VS code het deel venster **uitbrei dingen** , kies de **Azure dev Space** -extensies en kies **opnieuw laden**.

## <a name="update-visual-studio"></a>Visual Studio bijwerken

Azure dev Spaces maakt deel uit van de Azure Development-werk belasting en is opgenomen in alle Visual Studio-updates.

## <a name="next-steps"></a>Volgende stappen

Test de nieuwe hulpprogram ma's door een nieuw cluster te maken. Probeer de Snelstartgids en zelf studies op [Azure dev Spaces](../index.yml).

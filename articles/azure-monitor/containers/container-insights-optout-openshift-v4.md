---
title: Stoppen met het bewaken van uw Azure en Red Hat open Shift v4-cluster | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de bewaking van uw Azure Red Hat open Shift-cluster en Red Hat open Shift versie 4 kunt stoppen met container Insights.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 09ca05a25ce9bb02b8a3d515acf060e2e9e7e8c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731796"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Het bewaken van uw Azure-en Red Hat open Shift v4-cluster stoppen

Nadat u de bewaking van uw Azure Red Hat open Shift en Red Hat open Shift versie 4. x hebt ingeschakeld, kunt u stoppen met het bewaken van het cluster met container Insights als u besluit dat u het niet meer wilt bewaken. Dit artikel laat u zien hoe u dit kunt doen.  

## <a name="how-to-stop-monitoring-using-helm"></a>Bewaking stoppen met helm

1. Voer de volgende helm-opdracht uit om eerst de helm-grafiek release van container Insights te identificeren die op uw cluster is geïnstalleerd.

    ```
    helm list
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* vertegenwoordigt de helm-grafiek release voor container Insights.

2. Als u de grafiek release wilt verwijderen, voert u de volgende helm-opdracht uit.

    `helm delete <releaseName>`

    Voorbeeld:

    `helm delete azmon-containers-release-1`

    Hiermee wordt de release uit het cluster verwijderd. U kunt dit controleren door de opdracht uit te voeren `helm list` :

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

Het kan enkele minuten duren voordat de configuratie is gewijzigd. Omdat helm uw releases registreert, zelfs nadat u ze hebt verwijderd, kunt u de geschiedenis van een cluster controleren en zelfs een release verwijderen met `helm rollback` .

## <a name="next-steps"></a>Volgende stappen

Als de Log Analytics-werk ruimte alleen is gemaakt ter ondersteuning van het bewaken van het cluster en het niet meer nodig is, moet u het hand matig verwijderen. Zie [een Azure log Analytics-werk ruimte verwijderen](../logs/delete-workspace.md)als u niet bekend bent met het verwijderen van een werk ruimte.
---
title: Ingangs controller upgraden met helm
description: Dit artikel bevat informatie over het bijwerken van een Application Gateway ingang met behulp van helm.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: f20302a4993da1754255254ce6d69c000750d4ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84806779"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Een upgraden uitvoeren van de controller voor inkomend verkeer van Application Gateway met behulp van Helm 

De Azure-toepassing gateway ingangs controller voor Kubernetes (AGIC) kan worden bijgewerkt met behulp van een helm-opslag plaats die wordt gehost op Azure Storage.

Voordat u met de upgrade procedure begint, moet u ervoor zorgen dat u de vereiste opslag plaats hebt toegevoegd:

- Bekijk uw momenteel toegevoegde helm-opslag plaatsen met:

    ```bash
    helm repo list
    ```

- Voeg de AGIC-opslag plaats toe met:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Upgraden

1. Vernieuw de AGIC helm-opslag plaats om de nieuwste versie te verkrijgen:

    ```bash
    helm repo update
    ```

1. Beschik bare versies van de grafiek weer geven `application-gateway-kubernetes-ingress` :

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Voorbeeld antwoord:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    De meest recente beschik bare versie van de bovenstaande lijst is: `0.7.0-rc1`

1. Bekijk de helm-grafieken die momenteel zijn geïnstalleerd:

    ```bash
    helm list
    ```

    Voorbeeld antwoord:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    De helm-grafiek installatie van het bovenstaande voorbeeld antwoord heeft de naam `odd-billygoat` . Deze naam wordt gebruikt voor de rest van de opdrachten. De daad werkelijke implementatie naam kan waarschijnlijk verschillen.

1. Voer een upgrade uit van de helm-implementatie naar een nieuwe versie:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Terugdraaiactie

Als de implementatie van de helm mislukt, kunt u terugdraaien naar een eerdere versie.

1. Het laatst bekende gezonde release nummer ophalen:

    ```bash
    helm history odd-billygoat
    ```

    Voorbeelduitvoer:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    Vanuit de voorbeeld uitvoer van de opdracht lijkt het alsof `helm history` de laatste geslaagde implementatie van onze `odd-billygoat` revisie `1`

1. Herstellen naar de laatste geslaagde revisie:

    ```bash
    helm rollback odd-billygoat 1
    ```

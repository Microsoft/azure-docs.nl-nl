---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623313"
---
* Wanneer u een nieuwe werk ruimte maakt, kunt u automatisch Services maken die nodig zijn voor de werk ruimte of bestaande Services gebruiken. Als u __bestaande services van een ander Azure-abonnement__ wilt gebruiken dan de werk ruimte, moet u de Azure machine learning naam ruimte registreren in het abonnement dat deze services bevat. Als u bijvoorbeeld een werk ruimte maakt in abonnement A die gebruikmaakt van een opslag account uit abonnement B, moet u de Azure Machine Learning naam ruimte registreren in abonnement B voordat u het opslag account met de werk ruimte kunt gebruiken.

    De resource provider voor Azure Machine Learning is __micro soft. MachineLearningServices__. Zie het artikel [Azure-resource providers en-typen](../articles/azure-resource-manager/management/resource-providers-and-types.md)  voor meer informatie over hoe u kunt controleren of het is geregistreerd en hoe u dit registreert.

    > [!IMPORTANT]
    > Dit geldt alleen voor resources die worden gebruikt tijdens het maken van de werk ruimte. Azure Storage accounts, Azure container REGI ster, Azure Key Vault en Application Insights.

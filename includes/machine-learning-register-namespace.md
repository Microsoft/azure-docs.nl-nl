---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: 6e9f349f2fd90fdb12d8d26ca904e504b75d9db2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445205"
---
* Wanneer u een nieuwe werk ruimte maakt, kunt u automatisch Services maken die nodig zijn voor de werk ruimte of bestaande Services gebruiken. Als u __bestaande services van een ander Azure-abonnement__ wilt gebruiken dan de werk ruimte, moet u de Azure machine learning naam ruimte registreren in het abonnement dat deze services bevat. Als u bijvoorbeeld een werk ruimte maakt in abonnement A die gebruikmaakt van een opslag account uit abonnement B, moet u de Azure Machine Learning naam ruimte registreren in abonnement B voordat u het opslag account met de werk ruimte kunt gebruiken.

    De resource provider voor Azure Machine Learning is __micro soft. MachineLearningService__. Zie het artikel [Azure-resource providers en-typen](../articles/azure-resource-manager/management/resource-providers-and-types.md)  voor meer informatie over hoe u kunt controleren of het is geregistreerd en hoe u dit registreert.

    > [!IMPORTANT]
    > Dit geldt alleen voor resources die worden gebruikt tijdens het maken van de werk ruimte. Azure Storage accounts, Azure container REGI ster, Azure Key Vault en Application Insights.
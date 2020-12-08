---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 354811b1db31731913f2a99287b0e4a71b2aff61
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464134"
---
Zorg voordat u begint voor het volgende:

* Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-resource. Zorg dat een ondersteund abonnement gebruikt zoals [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp) of [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
* U hebt toegang als eigenaar of inzender op het niveau van de resourcegroep voor de Azure Stack Edge-/Azure Storage-gateway, IoT Hub en Azure Storage-resources.

  * Als u een Azure Stack Edge-resource wilt maken, moet u machtigingen hebben als inzender (of hoger) op het niveau van de resourcegroep. U moet er ook voor zorgen dat de `Microsoft.DataBoxEdge` provider is geregistreerd. Ga voor meer informatie over het registreren naar [Resourceprovider registreren](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Als u een IoT Hub-resource wilt maken, moet u ervoor zorgen dat de provider Microsoft.Devices is geregistreerd. Ga voor meer informatie over het registreren naar [Resourceprovider registreren](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Als u een resource voor een Storage-account wilt maken, moet u een toegangsbereik van een inzender of hoger hebben op het niveau van de resourcegroep. Azure Storage is standaard een geregistreerde resourceprovider.
* U hebt beheerder-of gebruikerstoegang tot Azure Active Directory Graph API. Zie [Azure Active Directory Graph API](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-) voor meer informatie.
* U hebt een Microsoft Azure Storage-account met toegangsreferenties.


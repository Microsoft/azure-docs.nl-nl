---
title: Azure-abonnementen maken via een programma
description: In dit artikel worden de opties beschreven als u programmatisch Azure-abonnementen wilt maken.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9ec0ffeb930fd9285f34ad9ba9e6aa606b15b5a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593885"
---
# <a name="create-azure-subscriptions-programmatically"></a>Azure-abonnementen maken via een programma

In dit artikel worden de opties beschreven als u programmatisch Azure-abonnementen wilt maken.

Met behulp van diverse REST API's kunt u een abonnement maken voor de volgende typen Azure-overeenkomsten:

- Enterprise Agreement (EA)
- Microsoft-klantovereenkomst (MCA)
- Microsoft Partner-overeenkomst (MPA)

U kunt niet programmatisch extra abonnementen maken voor andere overeenkomst typen met REST-Api's.

De vereisten en details voor het maken van abonnementen verschillen per overeenkomst en API-versie. Raadpleeg de volgende artikelen die van toepassing zijn op uw situatie:

Meest recente API's:

- [EA-abonnementen maken](programmatically-create-subscription-enterprise-agreement.md)
- [MCA-abonnementen maken](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA-abonnementen maken](programmatically-create-subscription-microsoft-partner-agreement.md)

In deze artikelen ziet u ook hoe u abonnementen kunt maken met een Azure Resource Manager sjabloon (ARM-sjabloon). Een ARM-sjabloon helpt bij het automatiseren van het proces voor het maken van het abonnement.

Als u nog steeds [Preview-api's](programmatically-create-subscription-preview.md)gebruikt, kunt u er abonnementen mee blijven maken. 

## <a name="next-steps"></a>Volgende stappen

* Nadat u een abonnement hebt gemaakt, kunt u ervoor zorgen dat andere gebruikers en service-principals ook deze mogelijkheid hebben. Zie [Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)](grant-access-to-create-subscription.md) voor meer informatie.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen.

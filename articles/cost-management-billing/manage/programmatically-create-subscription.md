---
title: Programmatisch Azure-abonnementen maken
description: In dit artikel worden de opties beschreven als u programmatisch Azure-abonnementen wilt maken.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ce08ebf473b11eecae327c7de050c791f5bc1b1a
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379006"
---
# <a name="create-azure-subscriptions-programmatically"></a>Programmatisch Azure-abonnementen maken

In dit artikel worden de opties beschreven als u programmatisch Azure-abonnementen wilt maken.

Met behulp van diverse REST API's kunt u een abonnement maken voor de volgende typen Azure-overeenkomsten:

- Enterprise Agreement (EA)
- Microsoft-klantovereenkomst (MCA)
- Microsoft Partner-overeenkomst (MPA)

U kunt geen programmatisch extra abonnementen maken voor andere typen overeenkomst met REST API's.

De vereisten en details voor het maken van abonnementen verschillen per overeenkomst en API-versie. Raadpleeg de volgende artikelen die van toepassing zijn op uw situatie:

Meest recente API's:

- [EA-abonnementen maken](programmatically-create-subscription-enterprise-agreement.md)
- [MCA-abonnementen maken](programmatically-create-subscription-microsoft-customer-agreement.md)
- [MPA-abonnementen maken](programmatically-create-subscription-microsoft-partner-agreement.md)

Deze artikelen laten ook zien hoe u abonnementen maakt met een Azure Resource Manager sjabloon (ARM-sjabloon). Een ARM-sjabloon helpt bij het automatiseren van het proces voor het maken van abonnementen.

Als u nog steeds [preview-API's gebruikt,](programmatically-create-subscription-preview.md)kunt u er abonnementen mee blijven maken. 

## <a name="next-steps"></a>Volgende stappen

* Nadat u een abonnement hebt gemaakt, kunt u ervoor zorgen dat andere gebruikers en service-principals ook deze mogelijkheid hebben. Zie [Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)](grant-access-to-create-subscription.md) voor meer informatie.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen.

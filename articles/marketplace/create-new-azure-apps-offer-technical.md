---
title: Technische gegevens voor uw Azure-toepassing aanbieding toevoegen
description: Meer informatie over het toevoegen van technische gegevens voor uw Azure-toepassings aanbieding in Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370079"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Technische gegevens voor uw Azure-toepassing aanbieding toevoegen

In dit artikel wordt beschreven hoe u technische gegevens kunt invoeren waarmee micro soft Commercial Marketplace verbinding kan maken met uw oplossing. Met deze verbinding kunnen wij uw aanbieding voor de klant inrichten als ze ervoor kiezen om deze te verkrijgen en te beheren.

Voltooi deze sectie alleen als uw aanbieding een beheerde toepassing bevat die meter gebeurtenissen gaat verzenden met behulp van de [facturerings-api's met Marketplace-data](partner-center-portal/marketplace-metering-service-apis.md) limiet en een service heeft die wordt geverifieerd met een Azure AD-beveiligings token. Zie voor meer informatie strategieën voor de [verificatie van Marketplace-meet service](partner-center-portal/marketplace-metering-service-authentication.md) voor de verschillende verificatie opties.

## <a name="technical-configuration-offer-level"></a>Technische configuratie (aanbod niveau)

Het tabblad **technische configuratie** is alleen op u van toepassing als u een beheerde toepassing gaat maken die meter gebeurtenissen verzendt met behulp van de [Marketplace-facturerings-api's met data](partner-center-portal/marketplace-metering-service-apis.md)limieten. Als dit het geval is, voert u de volgende stappen uit. Anders gaat u naar [volgende stappen](#next-steps). 

Zie [een Azure-toepassing aanbieding plannen voor de commerciële Marketplace](plan-azure-application-offer.md#technical-configuration)voor meer informatie over deze velden.

1. Geef op het tabblad **technische configuratie** de **Azure Active Directory Tenant-id** en **Azure Active Directory toepassings-id** die wordt gebruikt om de verbinding tussen de twee services te valideren, zich achter een geverifieerde communicatie bevindt.

1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: plan overzicht.

## <a name="next-steps"></a>Volgende stappen

- [Plannen maken voor uw Azure-toepassing aanbieding](create-new-azure-apps-offer-plans.md)

---
title: De service-principal van een beheerde identiteit weer geven in de Azure Portal-Azure AD
description: Stapsgewijze instructies voor het weer geven van de service-principal van een beheerde identiteit in de Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b25f2691646192faf4d4c133835b456038abd94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96003732"
---
# <a name="view-the-service-principal-of-a-managed-identity-in-the-azure-portal"></a>De service-principal van een beheerde identiteit weer geven in de Azure Portal

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u hoe u de service-principal van een beheerde identiteit kunt weer geven met behulp van de Azure Portal.

 > [!NOTE] 
 > Service-principals zijn bedrijfs toepassingen. 

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [meldt u zich aan voor een gratis account](https://azure.microsoft.com/free/).
- [Systeem toegewezen identiteit inschakelen op een virtuele machine](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) of [toepassing](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).

## <a name="view-the-service-principal"></a>De Service-Principal weer geven

In deze procedure ziet u hoe u de service-principal van een virtuele machine kunt weer geven waarvoor door het systeem toegewezen identiteit is ingeschakeld (dezelfde stappen zijn van toepassing op een toepassing).

1. Klik op **Azure Active Directory** en klik vervolgens op **bedrijfs toepassingen**.
2. Kies onder **toepassings type** **alle toepassingen** en klik vervolgens op **Toep assen**.
3. In het vak Zoek filter typt u de naam van de Azure-resource waarvoor beheerde identiteit is ingeschakeld of kiest u deze in de weer gegeven lijst.

   ![Service-Principal beheerde identiteit weer geven in de portal](./media/how-to-view-managed-identity-service-principal-portal/view-managed-identity-service-principal-portal.png)

## <a name="next-steps"></a>Volgende stappen

[Beheerde identiteiten voor Azure-resources](./overview.md)
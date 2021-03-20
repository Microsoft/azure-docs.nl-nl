---
title: Een beheerde identiteits toegang tot een resource toewijzen met behulp van de Azure Portal-Azure AD
description: Stapsgewijze instructies voor het toewijzen van een beheerde identiteit aan één bron toegang tot een andere resource, met behulp van de Azure Portal.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6584754edf3ff7ae31c3b9ace72baf16459dbc44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359987"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Een beheerde identiteits toegang tot een bron toewijzen met behulp van de Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Nadat u een Azure-resource met een beheerde identiteit hebt geconfigureerd, kunt u de beheerde identiteit toegang geven tot een andere resource, net zoals elke beveiligingsprincipal. Dit artikel laat u zien hoe u met behulp van de Azure Portal een Azure virtual machine of de beheerde identiteits toegang van een virtuele machine kunt instellen voor een Azure-opslag account.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC gebruiken om een beheerde identiteits toegang toe te wijzen aan een andere resource

Nadat u de beheerde identiteit voor een Azure-resource hebt ingeschakeld, zoals een [Azure VM](qs-configure-portal-windows-vm.md) of [virtuele-machine schaalset van Azure](qs-configure-portal-windows-vmss.md):

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement waaronder u de beheerde identiteit hebt geconfigureerd.

2. Ga naar de gewenste resource waarvoor u toegangs beheer wilt wijzigen. In dit voor beeld geven we een virtuele Azure-machine toegang tot een opslag account, dus gaan we naar het opslag account.

3. Selecteer de pagina **toegangs beheer (IAM)** van de resource en selecteer **+ roltoewijzing toevoegen**. Geef vervolgens de **rol** op, **wijs toegang toe aan** en geef het bijbehorende **abonnement** op. Onder het gebied zoek criteria ziet u de resource. Selecteer de resource en selecteer **Opslaan**. 

   ![Scherm opname van toegangs beheer (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md)
- Als u beheerde identiteit wilt inschakelen op een virtuele machine van Azure, raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een VM met behulp van de Azure Portal](qs-configure-portal-windows-vm.md).
- Als u beheerde identiteit wilt inschakelen voor een virtuele-machine schaalset van Azure, raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een schaalset voor virtuele machines met behulp van de Azure Portal](qs-configure-portal-windows-vmss.md).



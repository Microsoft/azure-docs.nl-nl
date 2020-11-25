---
title: Beheerde identiteiten configureren op VMSS - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een virtuele-machineschaalset met behulp van Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 43b986b6bb25dff1227247b1d249ce565553877a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997406"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Beheerde identiteiten voor Azure-resources configureren op een virtuele-machineschaalset met behulp van Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u om met behulp van PowerShell de volgende beheerde identiteiten uit te voeren voor bewerkingen van Azure-resources op een virtuele-machineschaalset:

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Voor het uitvoeren van de beheerbewerkingen in dit artikel zijn voor uw account de volgende Azure-roltoewijzingen nodig:

    > [!NOTE]
    > Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-map.

    - [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) voor het inschakelen en verwijderen van een door het systeem toegewezen beheerde identiteit op een virtuele-machineschaalset.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie wordt beschreven hoe u de door het systeem toegewezen beheerde identiteit in- en uitschakelt met behulp van Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele-machineschaalset

Op dit moment biedt Azure Portal geen ondersteuning voor het inschakelen van een door het systeem toegewezen beheerde identiteit tijdens het maken van een virtuele-machineschaalset. Raadpleeg in plaats daarvan het artikel in de quickstart over het maken van virtuele-machineschaalsets om eerst een virtuele-machineschaalset te maken. Ga verder met de volgende sectie voor meer informatie over het inschakelen van een door het systeem toegewezen beheerde identiteit op een virtuele-machineschaalset:

- [Een schaalset voor virtuele machines maken in Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit inschakelen op een bestaande virtuele-machineschaalset

Als u de door het systeem toegewezen beheerde identiteit wilt inschakelen op een virtuele-machineschaalset die oorspronkelijk zonder is ingericht:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement dat de virtuele-machineschaalset bevat.

2. Navigeer naar de gewenste virtuele-machineschaalset.

3. Selecteer onder **Door systeem toegewezen**, **Status** de optie **Aan** en klik op **Opslaan**:

   ![Schermafbeelding met de pagina 'Identiteit (preview)', met 'Systeem toegewezen' geselecteerd, de status 'Aan' en de knop 'Opslaan' gemarkeerd.](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit uit een virtuele-machineschaalset verwijderen

Als u een virtuele-machineschaalset hebt waarvoor geen door het systeem toegewezen beheerde identiteit meer nodig is, doet u het volgende:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de virtuele-machineschaalset bevat. Zorg er ook voor dat uw account deel uitmaakt van een rol waarmee u schrijfmachtigingen hebt voor de virtuele-machineschaalset.

2. Navigeer naar de gewenste virtuele-machineschaalset.

3. Selecteer onder **Door systeem toegewezen**, **Status** de optie **Uit** en klik op **Opslaan**:

   ![Schermopname van configuratiepagina](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie leert u hoe u een door een gebruiker toegewezen beheerde identiteit kunt toevoegen aan/verwijderen uit een virtuele-machineschaalset met behulp van Azure Portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Door de gebruiker toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele-machineschaalset

Op dit moment ondersteunt Azure Portal niet het toewijzen van een door de gebruiker toegewezen beheerde identiteit tijdens het maken van een virtuele-machineschaalset. Raadpleeg in plaats daarvan het artikel in de quickstart over het maken van virtuele-machineschaalsets om eerst een virtuele-machineschaalset te maken. Ga vervolgens verder met de volgende sectie voor meer informatie over het eraan toewijzen van een door een gebruiker toegewezen beheerde identiteit:

- [Een schaalset voor virtuele machines maken in Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Een door een gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande virtuele-machineschaalset

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de virtuele-machineschaalset bevat.
2. Ga naar de gewenste virtuele-machineschaalset en klik op **Identiteit**, **Door gebruiker toegewezen** en **\+Toevoegen**.

   ![Schermafbeelding met de pagina 'Identiteit', met 'Door de gebruiker toegewezen' geselecteerd en de knop 'Toevoegen' gemarkeerd.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Klik op de door de gebruiker toegewezen identiteit die u wilt toevoegen aan virtuele-machineschaalset en klik op **Toevoegen**.
   
   ![Door gebruiker toegewezen identiteit aan VMSS toevoegen](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit uit een virtuele-machineschaalset verwijderen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.
2. Ga naar de gewenste virtuele-machineschaalset en klik op **Identiteit**, **Door gebruiker toegewezen**, de naam van de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen en klik op **Verwijderen** (Klik op **Ja** in het bevestigingsvenster).

   ![Door gebruiker toegewezen identiteit uit een VMSS verwijderen](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Volgende stappen

- Met Azure Portal geeft u een door een virtuele-machineschaalset beheerde identiteit van Azure [toegang tot een andere Azure-resource](howto-assign-access-portal.md).
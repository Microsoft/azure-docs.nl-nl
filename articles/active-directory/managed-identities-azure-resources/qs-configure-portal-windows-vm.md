---
title: Beheerde identiteiten configureren met Azure Portal - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een virtuele Azure-machine met behulp Azure Portal.
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
ms.date: 11/10/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e35366e35fc127057020906ac6d3dfb0f207ab72
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997423"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Beheerde identiteiten voor Azure-resources op een VM configureren met Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben. 

In dit artikel leert u hoe u door het systeem en door de gebruiker toegewezen beheerde identiteiten voor een virtuele Azure-machine (VM) kunt in- en uitschakelen met behulp van Azure Portal. 

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md).
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie leert u hoe u de door het systeem toegewezen beheerde identiteit voor VM in- en uitschakelt met behulp van Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een VM

Als u de door het systeem toegewezen beheerde identiteit op een VM tijdens het maken wilt inschakelen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

- Ga naar het tabblad **Beheer** in het gedeelte **Identiteit** en schakel **Beheerde service-identiteit** in op **Aan**.  

![Door het systeem toegewezen identiteit inschakelen tijdens het maken van een VM](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Raadpleeg de volgende quickstarts om een VM te maken: 

- [Een virtuele Windows-machine maken met Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Een virtuele Linux-machine maken met Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen op een bestaande VM

Als u een door het systeem toegewezen beheerde identiteit wilt inschakelen op een VM die oorspronkelijk zonder deze identiteit werd ingericht, heeft uw account de roltoewijzing [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) nodig.  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.

2. Navigeer naar de gewenste VM en selecteer **Identiteit**.

3. Selecteer onder **Door systeem toegewezen**, **Status** de optie **Aan** en klik op **Opslaan**:

   ![Schermafbeelding met de pagina 'Identiteit (voorbeeld)' met de status 'Systeem toegewezen' ingesteld op 'Aan'.](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Door het systeem toegewezen beheerde identiteit uit een VM verwijderen

Als u de door het systeem toegewezen beheerde identiteit uit een VM wilt verwijderen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).  Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

Als u een virtuele machine hebt waarvoor geen door het systeem toegewezen beheerde identiteit meer nodig is, doet u het volgende:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat. 

2. Navigeer naar de gewenste VM en selecteer **Identiteit**.

3. Selecteer onder **Door systeem toegewezen**, **Status** de optie **Uit** en klik op **Opslaan**:

   ![Schermopname van configuratiepagina](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

 In deze sectie leert u hoe u een door een gebruiker toegewezen beheerde identiteit kunt toevoegen aan/verwijderen van een virtuele machine met behulp van Azure Portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Door de gebruiker toegewezen identiteit inschakelen tijdens het maken van een VM

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM, moet uw account beschikken over de roltoewijzingen [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator van de beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

Op dit moment ondersteunt Azure Portal niet het toewijzen van een door de gebruiker toegewezen beheerde identiteit tijdens het maken van een virtuele machine. Raadpleeg in plaats daarvan een van de volgende artikelen in de quickstart over het maken van een VM om eerst een virtuele machine te maken. Ga vervolgens verder met de volgende sectie voor meer informatie over het eraan toewijzen van een door een gebruiker toegewezen beheerde identiteit aan de VM:

- [Een virtuele Windows-machine maken met Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Een virtuele Linux-machine maken met Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een bestaande VM

Als u een door een gebruiker toegewezen identiteit wilt toewijzen aan een VM, moet uw account beschikken over de roltoewijzingen [Inzender van de virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) en [Operator van de beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.
2. Ga naar de gewenste VM en klik op **Identiteit**, **Door de gebruiker toegewezen** en vervolgens op **\+Toevoegen**.

   ![Schermafbeelding met de pagina 'Identiteit', met 'Door de gebruiker toegewezen' geselecteerd en de knop 'Toevoegen' gemarkeerd.](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Klik op de door de gebruiker toegewezen identiteit die u wilt toevoegen aan de VM en klik op **Toevoegen**.

    ![Door de gebruiker toegewezen beheerde identiteit toevoegen aan VM](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>De door de gebruiker toegewezen beheerde identiteit verwijderen van een VM

Als u de door de gebruiker toegewezen identiteit uit een VM wilt verwijderen, moet uw account beschikken over de roltoewijzing [Inzender voor virtuele machine](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor). Er zijn geen aanvullende roltoewijzingen vereist voor de Azure AD-directory.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat.
2. Ga naar de gewenste virtuele machine en klik op **Identiteit**, **Door gebruiker toegewezen**, de naam van de door de gebruiker toegewezen beheerde identiteit die u wilt verwijderen en klik op **Verwijderen** (Klik op **Ja** in het bevestigingsvenster).

   ![De door de gebruiker toegewezen beheerde identiteit verwijderen van een VM](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Volgende stappen

- Met Azure Portal geeft u een beheerde identiteit van Azure VM [toegang tot een andere Azure-resource](howto-assign-access-portal.md).
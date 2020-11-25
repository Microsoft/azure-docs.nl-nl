---
title: Een door de gebruiker toegewezen beheerde identiteit beheren in de Azure Portal-Azure AD
description: Stapsgewijze instructies voor het maken, weer geven, verwijderen en toewijzen van een rol aan een door de gebruiker toegewezen beheerde identiteit.
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
ms.date: 08/26/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ad91c916a6134f2507e74df6e87478421a00f43
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003001"
---
# <a name="create-list-delete-or-assign-a-role-to-a-user-assigned-managed-identity-using-the-azure-portal"></a>Een rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel leert u hoe u een rol kunt maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement om de door de gebruiker toegewezen beheerde identiteit te maken.
2. Typ *beheerde identiteiten* in het zoekvak en klik onder **Services** op **beheerde identiteiten**.
3. Klik op **toevoegen** en voer waarden in de volgende velden in het deel venster door **gebruiker toegewezen beheerde identiteit maken** in:
    - **Abonnement**: Kies het abonnement om de door de gebruiker toegewezen beheerde identiteit onder te maken.
    - **Resource groep**: Kies een resource groep voor het maken van de door de gebruiker toegewezen beheerde identiteit in of klik op **Nieuw maken** om een nieuwe resource groep te maken.
    - **Regio**: Kies een regio voor het implementeren van de door de gebruiker toegewezen beheerde identiteit, bijvoorbeeld **VS-West**.
    - **Naam**: dit is de naam voor de door de gebruiker toegewezen beheerde identiteit, bijvoorbeeld UAI1.
    ![Een door de gebruiker toegewezen beheerde identiteit maken](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
4. Klik op **beoordeling + maken** om de wijzigingen te bekijken.
5. Klik op **Create**.

## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weer geven

Als u een door de gebruiker toegewezen beheerde identiteit wilt weer geven/lezen, moet uw account beschikken over de rol [Managed Identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) of [Managed id contributor](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement om de door de gebruiker toegewezen beheerde identiteiten weer te geven.
2. Typ *beheerde identiteiten* in het zoekvak en klik onder Services op **beheerde identiteiten**.
3. Er wordt een lijst geretourneerd met de door de gebruiker toegewezen beheerde identiteiten voor uw abonnement.  Als u de details van een door de gebruiker toegewezen beheerde identiteit wilt bekijken, klikt u op de naam.

![Door de gebruiker toegewezen beheerde identiteit weer geven](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

Als u een door de gebruiker toegewezen identiteit verwijdert, wordt deze niet verwijderd van de VM of de resource waaraan deze is toegewezen.  Zie een door de [gebruiker toegewezen beheerde identiteit verwijderen uit een virtuele machine](./qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm)om de door de gebruiker toegewezen identiteit te verwijderen uit een virtuele machine.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement om een door de gebruiker toegewezen beheerde identiteit te verwijderen.
2. Selecteer de door de gebruiker toegewezen beheerde identiteit en klik op **verwijderen**.
3. Kies **Ja** in het bevestigings venster.

![Door de gebruiker toegewezen beheerde identiteit verwijderen](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Een rol toewijzen aan een door de gebruiker toegewezen beheerde identiteit 

Als u een rol wilt toewijzen aan een door de gebruiker toegewezen beheerde identiteit, moet uw account beschikken over de toewijzing van de rol beheerder van de [gebruiker](../../role-based-access-control/built-in-roles.md#user-access-administrator) .

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een account dat is gekoppeld aan het Azure-abonnement om de door de gebruiker toegewezen beheerde identiteiten weer te geven.
2. Typ *beheerde identiteiten* in het zoekvak en klik onder Services op **beheerde identiteiten**.
3. Er wordt een lijst geretourneerd met de door de gebruiker toegewezen beheerde identiteiten voor uw abonnement.  Selecteer de door de gebruiker toegewezen beheerde identiteit waaraan u een rol wilt toewijzen.
4. Selecteer **Toegangsbeheer (IAM)** en selecteer vervolgens **Roltoewijzing toevoegen**.

   ![Start van door de gebruiker toegewezen beheerde identiteit](./media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

5. Configureer op de Blade roltoewijzing toevoegen de volgende waarden en klik vervolgens op **Opslaan**:
   - **Rol** : de rol die u wilt toewijzen
   - **Toegang toewijzen tot**  de resource om de door de gebruiker toegewezen beheerde identiteit toe te wijzen
   - **Selecteer** -het lid om toegang toe te wijzen
   
   ![IAM van door de gebruiker toegewezen beheerde identiteit](./media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)

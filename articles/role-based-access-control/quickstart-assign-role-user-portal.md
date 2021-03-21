---
title: 'Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van de Azure-portal - Azure RBAC'
description: In deze zelfstudie leert u hoe u gebruikers toegang geeft tot Azure-resources met behulp van de Azure-portal en op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: fcba9cad208c2ac170f91cc06a6db22e271f2a70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559315"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van de Azure-portal

Met [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](overview.md) kunt u de toegang tot Azure-resources beheren. In deze zelfstudie verleent u een gebruiker toegang tot het maken en beheren van virtuele machines in een resourcegroep.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Toegang voor een gebruiker toewijzen op het niveau van een resourcegroep
> * Toegang intrekken

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

1. Klik in de navigatielijst op **Resourcegroepen**.

1. Klik op **Toevoegen** om de blade **Resourcegroep** te openen.

   ![Een nieuwe resourcegroep toevoegen](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Voer bij **Resourcegroepnaam** **rbac-resource-group** in.

1. Selecteer een abonnement en een locatie.

1. Klik op **Maken** om de resourcegroep te maken.

1. Klik op **Vernieuwen** om de lijst met resourcegroepen te vernieuwen.

   De nieuwe resourcegroep wordt weergegeven in de lijst met resourcegroepen.

   ![Resourcegroeplijst](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Toegang verlenen

In azure RBAC wijst u een Azure-rol toe om toegang te verlenen.

1. Klik in de lijst **Resourcegroepen** op de nieuwe resourcegroep **rbac-resource-group**.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **Roltoewijzingen** om de huidige lijst met roltoewijzingen te zien.

   ![De blade Toegangsbeheer (IAM) voor een resourcegroep](./media/quickstart-assign-role-user-portal/access-control.png)

1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen.

   Als u niet bent gemachtigd voor het toewijzen van rollen, is de optie Roltoewijzing toevoegen uitgeschakeld.

   ![Menu Roltoewijzing toevoegen](./media/shared/add-role-assignment-menu.png)

    Het deelvenster Roltoewijzing toevoegen wordt geopend.

   ![Deelvenster Roltoewijzing toevoegen](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Selecteer in de vervolgkeuzelijst **Rol** **Inzender voor virtuele machines**.

1. Selecteer in de lijst **Selecteren** uzelf of een andere gebruiker.

1. Klik op **Opslaan** om de rol toe te wijzen.

   Na enkele ogenblikken krijgt de gebruiker de rol Inzender voor virtuele machines toegewezen in het bereik van de resourcegroep rbac-resource-group.

   ![Roltoewijzing Inzender voor virtuele machines](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Toegang intrekken

Als u in Azure RBAC de toegang wilt intrekken voor een rol, verwijdert u de roltoewijzing.

1. Plaats in de lijst met roltoewijzingen een vinkje naast de gebruiker met de rol Inzender voor virtuele machines.

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

## <a name="clean-up"></a>Opruimen

1. Klik in de navigatielijst op **Resourcegroepen**.

1. Klik op **rbac-resource-group** om de resourcegroep te openen.

1. Klik op **Resourcegroep verwijderen** om de resourcegroep te verwijderen.

   ![Resourcegroep verwijderen](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. Typ in de blade **Weet u zeker dat u wilt verwijderen** de naam van de resourcegroep: **rbac-resource-group**.

1. Klik op **Verwijderen** om de resourcegroep te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Toegang tot Azure-resources verlenen aan een gebruiker met behulp van Azure PowerShell](tutorial-role-assignments-user-powershell.md)

---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/19/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: a00291182059506aeab9cde965fa4cbd5177ecf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80132183"
---
Als een gebruiker niet beschikt over de vereiste toegang voor het toepassen van tags, kunt u de rol **Inzender van tag** toewijzen aan de gebruiker. Zie [Zelfstudie: Verleen een gebruiker toegang tot Azure-resources verlenen met behulp van RBAC en Azure Portal](../articles/role-based-access-control/quickstart-assign-role-user-portal.md).

1. Als u de tags voor een resource of resourcegroep wilt weergeven, selecteert u het pictogram Tags in het overzicht. Als u nog niet eerder tags hebt toegepast, is de lijst leeg.

   ![Tags weergeven voor de resource of resourcegroep.](./media/resource-manager-tag-resources/view-tags.png)

1. Als u een tag wilt toevoegen, selecteert u **Klik hier om tags toe te voegen**.

1. Geef een naam en waarde op.

   ![Tag toevoegen](./media/resource-manager-tag-resources/add-tag.png)

1. Ga verder met het toevoegen van tags als dat nodig is. Selecteer **Opslaan** wanneer u klaar bent.

   ![Tags opslaan](./media/resource-manager-tag-resources/save-tags.png)

1. De tags worden nu weergegeven in het overzicht.

   ![Tags weergeven](./media/resource-manager-tag-resources/view-new-tags.png)

1. Selecteer **wijzigen** om een tag toe te voegen of te verwijderen.

1. Als u een tag wilt verwijderen, selecteert u het prullenbakpictogram. Selecteer vervolgens **Opslaan**.

   ![Tag verwijderen](./media/resource-manager-tag-resources/delete-tag.png)

Tags bulksgewijs toewijzen aan meerdere resources:

1. Schakel in een lijst met resources het selectievakje in voor de resources die u wilt toewijzen aan de tag. Selecteer **Tags toewijzen**.

   ![Selecteer Meerdere resources maken.](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Voeg een naam en waarde toe. Selecteer **Opslaan** wanneer u klaar bent.

   ![Selecteer Toewijzen](./media/resource-manager-tag-resources/select-assign.png)

Alle resources met een tag weergeven:

1. Zoek in het menu Azure Portal naar **Tags**. Selecteer Tags bij de beschikbare opties.

   ![Zoeken op tag](./media/resource-manager-tag-resources/find-tags-general.png)

1. Selecteer de tag om de resources weer te geven.

   ![Tag selecteren](./media/resource-manager-tag-resources/select-tag.png)

1. Alle resources met die tag worden weergegeven.

   ![Resources zoeken op tag](./media/resource-manager-tag-resources/view-resources-by-tag.png)

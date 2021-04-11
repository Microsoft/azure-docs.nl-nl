---
title: Azure Monitor werkmappen uw eigen opslag plaatsen
description: Meer informatie over het beveiligen van uw werkmap door de inhoud van de werkmap op te slaan in uw opslag
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: lagayhar
author: lgayhardt
ms.openlocfilehash: 16dbd7f7cd178a76b34b58f4bc6f9a0bc00fac73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100611673"
---
# <a name="bring-your-own-storage-to-save-workbooks"></a>Uw eigen opslag ruimte maken voor het opslaan van werkmappen

Het kan zijn dat u een query of een aantal bedrijfs logica hebt die u wilt beveiligen. Werkmappen biedt een optie voor het beveiligen van de werkmappen door de inhoud van de werkmappen op te slaan in uw opslag. Het opslag account kan vervolgens worden versleuteld met door micro soft beheerde sleutels of u kunt de versleuteling beheren door uw eigen sleutels op te geven. [Zie de Azure-documentatie over Storage Service Encryption.](../../storage/common/storage-service-encryption.md)

## <a name="saving-workbook-with-managed-identities"></a>Werkmap opslaan met beheerde identiteiten

1. Voordat u de werkmap in uw opslag ruimte kunt opslaan, moet u een beheerde identiteit maken (alle services-> beheerde identiteiten) en deze toegang geven tot `Storage Blob Data Contributor` uw opslag account. [Zie de Azure-documentatie over beheerde identiteiten.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

    [![Scherm opname van het toevoegen van een roltoewijzing](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png)](./media/workbooks-bring-your-own-storage/add-identity-role-assignment.png#lightbox)

2. Een nieuwe werkmap maken.
3. Selecteer de knop **Opslaan** om de werkmap op te slaan.
4. Er is een optie voor `Save content to an Azure Storage Account` , schakel het selectie vakje in om naar een Azure Storage-account op te slaan.

    ![Scherm opname met het opgeslagen dialoog venster](./media/workbooks-bring-your-own-storage/saved-dialog-default.png)

5. Selecteer het gewenste opslag account en de container. De lijst met opslag accounts is afkomstig van het abonnement dat hierboven is geselecteerd.

    ![Scherm opname van het dialoog venster opslaan met opslag optie](./media/workbooks-bring-your-own-storage/save-dialog-with-storage.png)

6. Selecteer vervolgens **wijzigen** om een beheerde identiteit te selecteren die u eerder hebt gemaakt.

    [![Scherm opname van dialoog venster identiteit wijzigen](./media/workbooks-bring-your-own-storage/change-managed-identity.png)](./media/workbooks-bring-your-own-storage/change-managed-identity.png#lightbox)

7. Nadat u uw opslag opties hebt geselecteerd, drukt u op **Opslaan** om uw werkmap op te slaan.

## <a name="limitations"></a>Beperkingen

- Wanneer u opslaat naar aangepaste opslag, kunt u afzonderlijke delen van de werkmap niet aan een dash board vastmaken, omdat de afzonderlijke pincodes beveiligde informatie in het dash board zelf bevatten. Wanneer u aangepaste opslag gebruikt, kunt u alleen koppelingen naar de werkmap zelf vastmaken aan dash boards.
- Zodra een werkmap is opgeslagen in een aangepaste opslag, wordt deze altijd opgeslagen in de aangepaste opslag en kan deze niet worden uitgeschakeld. Als u ergens anders wilt opslaan, kunt u ' opslaan als ' gebruiken en ervoor kiezen om het kopiëren naar de aangepaste opslag niet op te slaan.
- Werkmappen in Application Insights resource zijn ' verouderde ' werkmappen en bieden geen ondersteuning voor aangepaste opslag. De meest recente werkmappen in Application Insights resource is de '... Meer "selectie. Bij het opslaan van een verouderde werkmap zijn er geen abonnements opties.

   ![Scherm opname van oude werkmap](./media/workbooks-bring-your-own-storage/legacy-workbooks.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van een [kaart](workbooks-map-visualizations.md) visualisatie in werkmappen.
- Meer informatie over het gebruik [van groepen in werkmappen](../visualize/workbooks-groups.md).
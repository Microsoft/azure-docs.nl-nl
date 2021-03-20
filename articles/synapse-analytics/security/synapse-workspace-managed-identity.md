---
title: Beheerde identiteit in Synapse-werkruimte
description: Een artikel waarin de beheerde identiteit in de Azure Synapse-werkruimte wordt toegelicht
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 7790bc2895449e8ab21cbd30d7da0e5529eb0562
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670679"
---
# <a name="azure-synapse-workspace-managed-identity"></a>Beheerde identiteit voor Azure Synapse-werkruimte

In dit artikel vindt u meer informatie over beheerde identiteiten in de Azure Synapse-werkruimte.

## <a name="managed-identities"></a>Beheerde identiteiten

Beheerde identiteit voor Azure-resources is een functie van Azure Active Directory. De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de functie Beheerde identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie.

Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had. Zie [Beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.

## <a name="azure-synapse-workspace-managed-identity"></a>Beheerde identiteit voor Azure Synapse-werkruimte

Er wordt een door het systeem toegewezen beheerde identiteit gemaakt voor uw Azure Synapse-werkruimte wanneer u de werkruimte maakt.

>[!NOTE]
>Deze beheerde identiteit van de werkruimte wordt aangeduid als beheerde identiteit in de rest van dit document.

Azure Synapse gebruikt de beheerde identiteit om pijplijnen te integreren. De levenscyclus van beheerde identiteiten is rechtstreeks gekoppeld aan de Azure Synapse-werkruimte. Als u de Azure Synapse-werkruimte verwijdert, wordt de beheerde identiteit ook opgeschoond.

De beheerde identiteit van de werkruimte heeft machtigingen nodig om bewerkingen uit te voeren in de pijplijnen. U kunt de object-id of de naam van uw Azure Synapse-werkruimte gebruiken om naar de beheerde identiteit te zoeken wanneer u machtigingen verleent.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Beheerde identiteit ophalen in de Azure-portal

U kunt de beheerde identiteit ophalen in de Azure-portal. Open de Azure Synapse-werkruimte in de Azure-portal en selecteer **Overzicht** in de linkernavigatievenster. De object-id van de beheerde identiteit wordt weergegeven in het hoofdscherm.

![Object-id van beheerde identiteit](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

De informatie over de beheerde identiteit wordt ook weergegeven wanneer u een gekoppelde service maakt die verificatie van beheerde identiteiten ondersteunt vanuit Azure Synapse Studio.

Start **Azure Synapse Studio** en selecteer het tabblad **Beheren** in het linkernavigatievenster. Selecteer nu **Gekoppelde services** en kies de optie **+ Nieuw** om een nieuwe gekoppelde service te maken.

![Gekoppelde service maken 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

Typ *Azure Data Lake Storage Gen2* in het venster **Nieuwe gekoppelde service**. Selecteer in de onderstaande lijst het resourcetype **Azure Data Lake Storage Gen2** en kies **Doorgaan**.

![Gekoppelde service maken 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

Kies in het volgende venster **Beheerde identiteit** bij **Verificatiemethode**. U ziet de **Naam** en de **Object-id** van de beheerde identiteit.

![Gekoppelde service maken 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Machtigingen verlenen voor de beheerde identiteit van de Azure Synapse-werkruimte](./how-to-grant-workspace-managed-identity-permissions.md)

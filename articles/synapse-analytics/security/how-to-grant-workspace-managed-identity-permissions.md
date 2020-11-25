---
title: Machtigingen verlenen voor beheerde identiteit in de Synapse-werk ruimte
description: Een artikel met uitleg over het configureren van machtigingen voor beheerde identiteit in de Azure Synapse-werk ruimte.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 54612bee5715cdb78141a8aacfa5d24c814269d1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014717"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Machtigingen verlenen voor beheerde identiteit van werk ruimte (preview-versie)

In dit artikel leert u hoe u machtigingen kunt verlenen aan de beheerde identiteit in de Azure Synapse-werk ruimte. Machtigingen hebben op hun beurt toegang tot toegewezen SQL-groepen in de werk ruimte en ADLS Gen2 opslag account via de Azure Portal.

>[!NOTE]
>Deze beheerde identiteit van de werkruimte wordt aangeduid als beheerde identiteit in de rest van dit document.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Beheerde identiteits machtigingen verlenen aan de toegewezen SQL-groep

De beheerde identiteit verleent machtigingen voor de toegewezen SQL-groepen in de werk ruimte. Met machtigingen die zijn verleend, kunt u pijp lijnen indelen die toegewezen activiteiten op basis van SQL-groep uitvoeren. Wanneer u een Azure Synapse-werk ruimte maakt met behulp van Azure Portal, kunt u de machtigingen voor beheerde identiteits controle verlenen voor toegewezen SQL-groepen.

Selecteer **beveiliging** bij het maken van de Azure Synapse-werk ruimte. Selecteer vervolgens **pijp lijnen toestaan (die worden uitgevoerd als de systeem-id van de werk ruimte) om toegang te krijgen tot SQL-groepen.**

![Machtiging beheren voor toegewezen SQL-groepen](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>De beheerde identiteits machtigingen verlenen aan ADLS Gen2-opslag account

Een ADLS Gen2 Storage-account is vereist voor het maken van een Azure Synapse-werk ruimte. Voor het starten van Spark-groepen in de Azure Synapse-werk ruimte, moet de beheerde Azure Synapse-identiteit de rol van *BLOB voor gegevens opslag* voor dit opslag account hebben. Pipeline-indeling in azure Synapse biedt ook voor delen van deze rol.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Machtigingen verlenen voor beheerde identiteit tijdens het maken van de werk ruimte

Azure Synapse probeert de rol Storage BLOB data contributor toe te kennen aan de beheerde identiteit nadat u de Azure Synapse-werk ruimte hebt gemaakt met behulp van Azure Portal. U geeft de gegevens van het ADLS Gen2 Storage-account op het tabblad **basis beginselen** .

![Tabblad basis informatie in de werk ruimte maken stroom](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Kies het ADLS Gen2 Storage-account en het bestands systeem in **account naam** en **bestandssysteem naam**.

![Details van een ADLS Gen2 Storage-account opgeven](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Als de maker van de werk ruimte ook de **eigenaar** van het ADLS Gen2-opslag account is, wijst Azure Synapse de rol van *BLOB voor gegevens opslag* toe aan de beheerde identiteit. U ziet het volgende bericht onder de gegevens van het opslag account die u hebt ingevoerd.

![Inzender toewijzing van BLOB-gegevens opslag is geslaagd](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Als de maker van de werk ruimte niet de eigenaar van het ADLS Gen2 Storage-account is, wijst Azure Synapse niet de rol van BLOB-gegevens van de *opslag groep* toe aan de beheerde identiteit. In het bericht dat wordt weer gegeven onder de gegevens van het opslag account wordt de maker van de werk ruimte gewaarschuwd dat ze niet voldoende machtigingen hebben om de rol van *BLOB-gegevensinzender voor opslag* toe te kennen aan de beheerde identiteit.

![De Inzender toewijzing voor de opslag van BLOB-gegevens is mislukt](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Als bericht status is het niet mogelijk om Spark-groepen te maken, tenzij de *Inzender voor opslag-blobs* is toegewezen aan de beheerde identiteit.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Machtigingen verlenen voor beheerde identiteit na het maken van de werk ruimte

Als u tijdens het maken van de werk ruimte de *Inzender voor gegevens van blobs* niet toewijst aan de beheerde identiteit, wijst de **eigenaar** van de ADLS Gen2 Storage-account deze rol hand matig toe aan de identiteit. De volgende stappen helpen u hand matige toewijzing uit te voeren.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Stap 1: Navigeer naar het ADLS Gen2 Storage-account in Azure Portal

Open in Azure Portal het ADLS Gen2 Storage-account en selecteer **overzicht** in het linkernavigatievenster. U hoeft alleen de rol *Storage BLOB data Inzender* toe te wijzen op het niveau van de container of het bestands systeem. Selecteer **Containers**.  
![Overzicht van ADLS Gen2 Storage-account](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Stap 2: de container selecteren

De beheerde identiteit moet gegevens toegang hebben tot de container (bestands systeem) die is ingevoerd tijdens het maken van de werk ruimte. U kunt deze container of dit bestands systeem vinden in Azure Portal. Open de Azure Synapse-werk ruimte in Azure Portal en selecteer het tabblad **overzicht** vanuit het navigatie venster aan de linkerkant.
![Container voor ADLS Gen2-opslag account](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Selecteer diezelfde container of het bestands systeem om de rol van *BLOB voor gegevens opslag* te verlenen aan de beheerde identiteit.
![Scherm opname van de container of het bestands systeem dat u moet selecteren.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Stap 3: Navigeer naar Access Control

Selecteer **Access Control (IAM)**.

![Toegangs beheer (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Stap 4: een nieuwe roltoewijzing toevoegen

Selecteer **+ Toevoegen**.

![Nieuwe roltoewijzing toevoegen](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>Stap 5: de Azure-rol selecteren

Selecteer de rol voor het overdragen van **BLOB-gegevens** .

![Selecteer de Azure-rol](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Stap 6: Selecteer de Azure AD-beveiligings-principal

Selecteer **Azure AD-gebruiker,-groep of Service-Principal** in de vervolg keuzelijst **toegang toewijzen** .

![AAD-beveiligingsprincipal selecteren](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Stap 7: zoeken naar de beheerde identiteit

De naam van de beheerde identiteit is ook de naam van de werk ruimte. Zoek uw beheerde identiteit door de naam van de Azure Synapse-werk ruimte op te geven in **Select**. De beheerde identiteit wordt weer gegeven.

![De beheerde identiteit zoeken](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Stap 8: de beheerde identiteit selecteren

De beheerde identiteit voor de **geselecteerde leden** selecteren. Selecteer **Opslaan** om de roltoewijzing toe te voegen.

![De beheerde identiteit selecteren](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Stap 9: controleren of de rol BLOB gegevensinzender voor opslag is toegewezen aan de beheerde identiteit

Selecteer **Access Control (IAM)** en selecteer vervolgens **roltoewijzingen**.

![Roltoewijzing controleren](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

De beheerde identiteit wordt weer gegeven onder de sectie **Storage BLOB data contributor** waaraan de rol voor *BLOB data* contributor voor opslag is toegewezen. 
![Selectie ADLS Gen2 Storage-account container](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over door [werk ruimte beheerde identiteit](./synapse-workspace-managed-identity.md)

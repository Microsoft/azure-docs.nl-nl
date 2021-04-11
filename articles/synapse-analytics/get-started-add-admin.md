---
title: 'Zelf studie: aan de slag een beheerder toevoegen'
description: In deze zelf studie leert u hoe u een andere gebruiker met beheerders rechten kunt toevoegen aan uw werk ruimte.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553495"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Een beheerder toevoegen aan uw Synapse-werk ruimte

In deze zelf studie leert u hoe u een beheerder kunt toevoegen aan uw Synapse-werk ruimte. Deze gebruiker heeft volledige controle over de werk ruimte.

## <a name="overview"></a>Overzicht

Tot nu toe hebben we in de hand leiding aan de slag de activiteiten gericht *die u* in de werk ruimte doet. Omdat u de werk ruimte in stap 1 hebt gemaakt, bent u een beheerder van de Synapse-werk ruimte. Nu gaan we een andere gebruiker Ryan ( `ryan@contoso.com` ) een beheerder maken. Als we klaar zijn, kan Joop alles doen wat u in de werk ruimte kunt doen.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: rol eigenaar voor de werk ruimte

Toewijzen aan voor `ryan@contoso.com` de rol Azure RBAC- **eigenaar** in de werk ruimte.

1. Open de Azure Portal en open de Synapse-werk ruimte.
1. Selecteer aan de linkerkant **Access Control (IAM)**.
1. Voeg `ryan@contoso.com` aan de rol **eigenaar** toe. 
1. Klik op **Opslaan**.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC: Synapse-beheerdersrol voor de werk ruimte

Toewijzen aan `ryan@contoso.com` Synapse RBAC **Synapse** -beheerdersrol in de werk ruimte.

1. Open uw werk ruimte in Synapse Studio.
1. Klik aan de linkerkant op **beheren** om de hub beheren te openen.
1. Klik onder **beveiliging** op **toegangs beheer**.
1. Klik op **Add**.
1. **Bereik van scope** instellen op **werk ruimte**.
1. Voeg toe `ryan@contoso.com` aan de beheerdersrol **Synapse** . 
1. Klik vervolgens op **Toep assen**.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Azure RBAC: roltoewijzingen voor het primaire opslag account

Wijs toe aan `ryan@contoso.com` de rol van **eigenaar** voor het primaire opslag account van de werk ruimte.
Toewijzen aan `ryan@contoso.com` **Azure Storage BLOB rol gegevensinzender** voor het primaire opslag account van de werk ruimte.

1. Open het primaire opslag account van de werk ruimte in de Azure Portal.
1. Klik aan de linkerkant op **Access Control (IAM)**.
1. Voeg `ryan@contoso.com` aan de rol **eigenaar** toe. 
1. Toevoegen `ryan@contoso.com` aan de rol **inzender voor Azure Storage BLOB gegevens**

## <a name="dedicated-sql-pools-db_owner-role"></a>Toegewezen SQL-groepen: db_owner rol

Toewijzen `ryan@contoso.com` aan de **db_owner** op elke toegewezen SQL-groep in de werk ruimte.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)

---
title: Verbinding maken met een Azure Purview-account 
description: Een Azure Purview-account verbinden met een Synapse-werkruimte.
author: julieMSFT
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ad9250d022f02db06c9c5251c1fccfbfb730af67
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567873"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Snelstartgids: Een Azure Purview-account verbinden met een Synapse-werkruimte 


In deze quickstart registreert u een Azure Purview-account bij een Synapse-werkruimte. Met deze verbinding kunt u Azure Purview-assets ontdekken en ermee werken via de mogelijkheden van Synapse. 

U kunt de volgende taken uitvoeren in Synapse: 
- Het zoekvak bovenaan gebruiken om Purview-assets te vinden op basis van trefwoorden 
- De gegevens begrijpen op basis van metagegevens, afkomst en aantekeningen 
- Deze gegevens verbinden met uw werkruimte met gekoppelde service of integratiegegevenssets 
- Deze gegevenssets analyseren met Synapse Apache Spark, Synapse SQL en Gegevensstroom 

## <a name="prerequisites"></a>Vereisten 
- [Azure Purview-account](../../purview/create-catalog-portal.md) 
- [Synapse-werkruimte](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>Aanmelden bij een Synapse-werkruimte 

Ga naar  [https://web.azuresynapse.net](https://web.azuresynapse.net) en meld u aan bij uw werkruimte. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Machtigingen voor verbinding maken met een Azure Purview-account 

- Als u een Azure Purview-account wilt verbinden met een Synapse-werkruimte, moet de rol **Inzender** aan u zijn toegewezen in de Synapse-werkruimte, via IAM in de Azure-portal, en hebt u toegang nodig tot dit Azure Purview-account. Zie Azure [Purview-machtigingen voor meer informatie.](../../purview/catalog-permissions.md)

## <a name="connect-an-azure-purview-account"></a>Verbinding maken met een Azure Purview-account  

- Ga in de Synapse-werkruimte naar **Beheren** -> **Azure Purview**. Selecteer **Verbinding maken met een Purview-account**. 
- U kunt kiezen voor **Vanuit een Azure-abonnement** of **Handmatig invoeren**. Bij **Vanuit een Azure-abonnement** kunt u het account selecteren waartoe u toegang hebt. 
- Zodra er verbinding is, ziet u de naam van het Purview-account op het tabblad **Azure Purview-account**. 
- U kunt de zoekbalk midden bovenaan de Synapse-werkruimte gebruiken om gegevens te zoeken. 

## <a name="nextsteps"></a>Volgende stappen 

[Azure Synapse-assets registreren en scannen in Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Gegevens detecteren, koppelen en verkennen in Synapse met behulp van Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   

---
title: Verbinding maken met een Azure Purview-account 
description: Een Azure Purview-account verbinden met een Synapse-werkruimte.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: f85a76a7ce01c11255f54c6b956088f9328dfc9f
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918462"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>Snelstartgids: Een Azure Purview-account verbinden met een Synapse-werkruimte 

> [!IMPORTANT]
> De integratie tussen Azure Synapse Analytics en Azure Purview bevindt zich momenteel in de preview-fase. Als u interesse hebt in het uitproberen van Azure Purview in Synapse, neemt u contact op met de Microsoft-verkoopmedewerker.

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

Ga naar https://web.azuresynapse.net en meld u aan bij uw werkruimte. 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Machtigingen voor verbinding maken met een Azure Purview-account 

- Als u een Azure Purview-account wilt verbinden met een Synapse-werkruimte, moet de rol **Inzender** aan u zijn toegewezen in de Synapse-werkruimte, via IAM in de Azure-portal, en hebt u toegang nodig tot dit Azure Purview-account.

## <a name="connect-an-azure-purview-account"></a>Verbinding maken met een Azure Purview-account  

- Ga in de Synapse-werkruimte naar **Beheren** -> **Azure Purview**. Selecteer **Verbinding maken met een Purview-account**. 
- U kunt kiezen voor **Vanuit een Azure-abonnement** of **Handmatig invoeren**. Bij **Vanuit een Azure-abonnement** kunt u het account selecteren waartoe u toegang hebt. 
- Zodra er verbinding is, ziet u de naam van het Purview-account op het tabblad **Azure Purview-account**. 
- U kunt de zoekbalk midden bovenaan de Synapse-werkruimte gebruiken om gegevens te zoeken. 

## <a name="nextsteps"></a>Volgende stappen 

[Azure Synapse-assets registreren en scannen in Azure Purview](../../purview/register-scan-azure-synapse-analytics.md)

[Gegevens detecteren, koppelen en verkennen in Synapse met behulp van Azure Purview](how-to-discover-connect-analyze-azure-purview.md)   
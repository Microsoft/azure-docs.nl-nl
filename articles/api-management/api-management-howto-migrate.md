---
title: Azure API Management migreren over regio's
description: Meer informatie over het migreren van een API Management-exemplaar van de ene regio naar een andere.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250223"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Azure API Management migreren over regio's
Als u API Management exemplaren van de ene Azure-regio naar de andere wilt migreren, kunt u de functie [back-up en herstellen](api-management-howto-disaster-recovery-backup-restore.md) gebruiken. U moet dezelfde API Management Prijs categorie in de bron-en doel regio's kiezen. 

> [!NOTE]
> Het maken van back-ups en herstellen werkt niet tijdens de migratie tussen verschillende Cloud typen. Hiervoor moet u de resource exporteren [als een sjabloon](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Pas vervolgens de geëxporteerde sjabloon voor de Azure-doel regio aan en maak de resource opnieuw. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Optie 1: een andere API Management exemplaar naam gebruiken

1. Maak in de doel regio een nieuw exemplaar van API Management met dezelfde prijs categorie als het bron API Management exemplaar. Het nieuwe exemplaar moet een andere naam hebben. 
1. Een back-up maken van een bestaande API Management-instantie naar een opslag account.
1. Herstel de back-up die u hebt gemaakt in stap 2 naar het nieuwe API Management exemplaar dat in de nieuwe regio is gemaakt in stap 1.
1. Als u een aangepast domein hebt dat verwijst naar de bron regio API Management exemplaar, werkt u de aangepaste domein-CNAME bij zodat deze naar de nieuwe API Management instantie wijst. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Optie 2: dezelfde API Management exemplaar naam gebruiken

> [!NOTE]
> Deze optie resulteert in downtime tijdens de migratie.

1. Maak een back-up van de API Management-instantie in de bron regio naar een opslag account.
1. Verwijder de API Management in de bron regio. 
1. Maak een nieuw exemplaar van de API Management-instantie in de doel regio met dezelfde naam als in de bron regio.
1. Herstel de back-up die u in stap 1 hebt gemaakt, naar het nieuwe API Management-exemplaar in de doel regio.  


## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen
* Zie [herstel na nood gevallen](api-management-howto-disaster-recovery-backup-restore.md)voor meer informatie over de functie voor het maken en terugzetten van back-ups.
* Zie voor meer informatie over het migreren van Azure [-resources Azure-ondersteuning voor migratie tussen regio's](https://github.com/Azure/Azure-Migration-Guidance).
* [Optimaliseer en bespaar in uw Cloud uitgaven](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

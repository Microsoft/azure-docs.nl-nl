---
title: Meerdere tenants beheren in azure Sentinel als een beheerde Security-serviceprovider | Microsoft Docs
description: Meerdere tenants in azure-Sentinel vrijgeven en beheren als een beheerde Security service provider (MSSP) met behulp van Azure Lighthouse.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2020
ms.author: yelevin
ms.openlocfilehash: 011ddb883c028a954a8b0683c220bf6341eddb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578140"
---
# <a name="manage-multiple-tenants-in-azure-sentinel-as-an-mssp"></a>Meerdere tenants beheren in azure Sentinel als een MSSP

Als u een MSSP (Managed Security service provider) bent en u [Azure Lighthouse](../lighthouse/overview.md) gebruikt om Soc-Services (Security Operations Center) aan uw klanten aan te bieden, kunt u Azure-Sentinel-bronnen van uw klanten rechtstreeks vanuit uw eigen Azure-Tenant beheren, zonder dat u verbinding hoeft te maken met de Tenant van de klant. 

## <a name="prerequisites"></a>Vereisten

- [Onboarding van Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)

- Voor een goede werking moet uw Tenant (de MSSP-Tenant) de Azure Sentinel resource providers hebben geregistreerd op ten minste één abonnement. Daarnaast moeten alle tenants van uw klanten de resource providers hebben geregistreerd. Als u Azure Sentinel hebt geregistreerd in uw Tenant en uw klanten in hun eigen gebruikers, bent u klaar om aan de slag te gaan. Voer de volgende stappen uit om de registratie te controleren:

    1. Selecteer **abonnementen** in het Azure Portal en selecteer vervolgens een relevant abonnement in het menu.

    1. Selecteer in het navigatie menu op het venster abonnement onder **instellingen** de optie **resource providers**.

    1. Uit de ***naam* van het abonnement | Bron providers** , zoek naar en selecteer *micro soft. OperationalInsights* en *micro soft. SecurityInsights* en controleer de kolom **status** . Als de status van de provider *NotRegistered* is, selecteert u **registreren**.
    
        :::image type="content" source="media/multiple-tenants-service-providers/check-resource-provider.png" alt-text="Resource providers controleren":::

## <a name="how-to-access-azure-sentinel-in-managed-tenants"></a>Toegang krijgen tot Azure Sentinel in beheerde tenants

1. Selecteer onder **adres lijst + abonnement** de gedelegeerde directory's (Directory = Tenant) en de abonnementen waar de Azure Sentinel-werk ruimten van uw klant zich bevinden.

    :::image type="content" source="media/multiple-tenants-service-providers/directory-subscription.png" alt-text="Tenants en abonnementen kiezen":::

1. Open Azure Sentinel. U ziet alle werk ruimten in de geselecteerde abonnementen, en u kunt deze probleemloos samen werken, zoals elke werk ruimte in uw eigen Tenant.

> [!NOTE]
> U kunt geen connectors implementeren in azure Sentinel vanuit een beheerde werk ruimte. Als u een connector wilt implementeren, moet u zich rechtstreeks aanmelden bij de Tenant waarop u een connector wilt implementeren en daar verifiëren met de vereiste machtigingen.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u meerdere Azure Sentinel-tenants naadloos kunt beheren. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).


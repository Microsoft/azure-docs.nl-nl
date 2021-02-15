---
title: Een werk ruimte maken waarop data exfiltration Protection is ingeschakeld
description: In dit artikel wordt uitgelegd hoe u een werk ruimte maakt met data exfiltration-beveiliging in azure Synapse Analytics
author: NanditaV
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 12d858488c4530e4b0d949cd36ed9ad2f7df4c59
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384483"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Een werk ruimte maken waarop data exfiltration Protection is ingeschakeld
In dit artikel wordt beschreven hoe u een werk ruimte maakt waarop data exfiltration Protection is ingeschakeld en hoe u de goedgekeurde Azure AD-tenants voor deze werk ruimte beheert.

>[!Note]
>U kunt de werkruimte configuratie voor beheerde virtuele netwerk-en gegevens exfiltration-beveiliging niet wijzigen nadat de werk ruimte is gemaakt.

## <a name="prerequisites"></a>Vereisten
- Machtigingen voor het maken van een werkruimte resource in Azure.
- Synapse-werkruimte machtigingen voor het maken van beheerde privé-eind punten.
- Geregistreerde abonnementen voor de netwerk bron provider. [Meer informatie.](../../azure-resource-manager/management/resource-providers-and-types.md)

Volg de stappen die worden beschreven in [Quick Start: een Synapse-werk ruimte maken](../quickstart-create-workspace.md) om aan de slag te gaan met het maken van uw werk ruimte. Voordat u uw werk ruimte maakt, gebruikt u de onderstaande informatie om gegevens exfiltration beveiliging aan uw werk ruimte toe te voegen.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Gegevens exfiltration beveiliging toevoegen bij het maken van uw werk ruimte
1. Schakel op het tabblad netwerken het selectie vakje beheerd virtueel netwerk inschakelen in.
1. Selecteer Ja voor de optie alleen uitgaand gegevens verkeer toestaan voor goedgekeurde doelen.
1. Kies de goedgekeurde Azure AD-tenants voor deze werk ruimte.
1. Controleer de configuratie en maak de werk ruimte.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Scherm opname van een Synapse-werk ruimte maken met de optie virtueel netwerk beheren ingeschakeld.":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Goedgekeurde Azure Active Directory tenants beheren voor de werk ruimte
1. Ga vanuit de Azure Portal van de werk ruimte naar ' goedgekeurde Azure AD-tenants '. De lijst met goedgekeurde Azure AD-tenants voor de werk ruimte wordt hier weer gegeven. De Tenant van de werk ruimte is standaard opgenomen en wordt niet weer gegeven.
1. Gebruik + toevoegen om nieuwe tenants op te geven in de lijst goedgekeurd.
1. Als u een Azure AD-Tenant uit de lijst goedgekeurd wilt verwijderen, selecteert u de Tenant en selecteert u verwijderen en vervolgens opslaan.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Een werk ruimte met gegevens exfiltration-beveiliging maken":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Verbinding maken met Azure-resources in goedgekeurde Azure AD-tenants

U kunt beheerde persoonlijke eind punten maken voor verbinding met Azure-resources die zich bevinden in azure AD-tenants, die zijn goedgekeurd voor een werk ruimte. Volg de stappen in de hand leiding voor het [maken van beheerde privé-eind punten](./how-to-create-managed-private-endpoints.md).

>[!IMPORTANT]
>Resources in andere tenants dan de Tenant van de werk ruimte mogen geen firewall regels blok keren, zodat de SQL-groepen er verbinding mee kunnen maken. Resources in het virtuele netwerk van de werk ruimte, zoals Spark-clusters, kunnen verbinding maken via beheerde persoonlijke koppelingen naar met firewalls beveiligde bronnen.

## <a name="known-limitations"></a>Bekende beperkingen
Gebruikers kunnen een omgevings configuratie bestand opgeven om Python-pakketten te installeren vanuit open bare opslag plaatsen zoals PyPI. In data exfiltration beveiligde werk ruimten worden verbindingen met uitgaande opslag plaatsen geblokkeerd. Als gevolg hiervan worden de python-bibliotheek die is geïnstalleerd vanuit open bare opslag plaatsen, zoals PyPI, niet ondersteund. 
  
## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Data exfiltration Protection in Synapse-werk ruimten](./workspace-data-exfiltration-protection.md)

Meer informatie over [Managed workspace Virtual Network](./synapse-workspace-managed-vnet.md)

Meer informatie over [beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md)

[Create Managed private endpoint to your data sources](./how-to-create-managed-private-endpoints.md) (Beheerde privé-eindpunten naar uw gegevensbronnen maken)

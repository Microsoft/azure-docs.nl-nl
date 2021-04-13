---
title: Automanage inschakelen voor virtuele machines via Azure Policy
description: Meer informatie over het inschakelen Azure Automanage voor VM's via een ingebouwde Azure Policy in de Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8846efa3619cec383809cdbd6efe70e3622fa007
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365192"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Automanage inschakelen voor virtuele machines via Azure Policy

Als u Automanage wilt inschakelen voor veel VM's, kunt u dat doen met behulp van een ingebouwde [Azure Policy.](..\governance\azure-management.md) In dit artikel wordt beschreven hoe u het juiste beleid kunt vinden en hoe u dit kunt toewijzen om Automanage in te Azure Portal.


## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proefversies hebben geen toegang tot de virtuele machines die in deze zelfstudie worden gebruikt. U moet upgraden naar een abonnement met betalen per gebruik.

> [!IMPORTANT]
> De volgende Azure RBAC-machtiging is nodig om Automatischmanage **in** teschakelen: Rol van eigenaar of Inzender,  samen met de rollen **Gebruikerstoegangbeheerder.**

## <a name="direct-link-to-policy"></a>Directe koppeling naar beleid
De beleidsdefinitie Automanage vindt u in de Azure Portal onder de naam Virtuele machines configureren die moeten worden [onboarden voor Azure Automanage.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3) Als u op deze koppeling klikt, gaat u direct naar stap 8 in [Zoeken en het onderstaande beleid](#locate-and-assign-the-policy) toewijzen.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Het beleid zoeken en toewijzen

1. **Navigeer naar Beleid** in de Azure Portal
1. Ga naar het **deelvenster Definities**
1. Klik op **de vervolgkeuzepagina** Categorieën om de beschikbare opties te bekijken
1. Selecteer de **optie Automanage inschakelen : best practices voor virtuele Azure-machines**
1. De lijst wordt nu bijgewerkt met een ingebouwd beleid met een naam die begint met *Automanage inschakelen...*
1. Klik op de ingebouwde beleidsnaam Voor het inschakelen van *automanage - Azure* virtual machine best practices
1. Nadat u op het beleid hebt geklikt, ziet u nu het **tabblad Definitie**

    > [!NOTE]
    > De Azure Policy wordt gebruikt voor het instellen van parameters voor automatisch gebruik, zoals het configuratieprofiel of het account. Er worden ook filters in die ervoor zorgen dat het beleid alleen van toepassing is op de juiste VM's.

1. Klik op de **knop Toewijzen** om een toewijzing te maken
1. Vul op **het tabblad** Basisinformatie Het bereik **in door** het abonnement *en* de *resourcegroep in te stellen*

    > [!NOTE]
    > Met Bereik kunt u definiëren op welke VM's dit beleid van toepassing is. U kunt de toepassing instellen op abonnementsniveau of op het niveau van de resourcegroep. Als u een resourcegroep instelt, wordt Automatisch beheer automatisch ingeschakeld voor alle VM's die zich momenteel in die resourcegroep of toekomstige VM's die we hieraan toevoegen.

1. Klik op het **tabblad Parameters** en stel het **Automanage-account en** het gewenste **configuratieprofiel in**
1. Controleer de **instellingen op het** tabblad Beoordelen en maken
1. Pas de toewijzing toe door te klikken op **Maken**
1. Bekijk uw toewijzingen op het **tabblad Toewijzingen** naast **Definitie**

> [!NOTE]
> Het duurt even voor dat beleid van kracht wordt op de VM's in de resourcegroep of het abonnement.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over een andere manier om Azure Automanage in te stellen voor virtuele machines via de Azure Portal.

> [!div class="nextstepaction"]
> [Automanage inschakelen voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)
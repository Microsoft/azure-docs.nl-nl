---
title: Schakel automanage in voor virtuele machines via Azure Policy
description: Meer informatie over het inschakelen van Azure automanage voor Vm's via een ingebouwde Azure Policy in de Azure Portal.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 8f679626b69bd855e86b94cdde51955edd068e8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91714904"
---
# <a name="enable-automanage-for-virtual-machines-through-azure-policy"></a>Schakel automanage in voor virtuele machines via Azure Policy

Als u automatisch beheer wilt inschakelen voor veel Vm's, kunt u dit doen met behulp van een ingebouwde [Azure Policy](..\governance\azure-management.md). In dit artikel wordt uitgelegd hoe u het juiste beleid kunt vinden en hoe u dit toewijst om automatisch beheer in te scha kelen in de Azure Portal.


## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proefversies hebben geen toegang tot de virtuele machines die in deze zelfstudie worden gebruikt. U moet upgraden naar een abonnement met betalen per gebruik.

> [!IMPORTANT]
> De volgende Azure RBAC-machtiging is nodig om automanage in te scha kelen: rol van **eigenaar** of **Inzender** samen met beheerders rollen van **gebruikers toegang** .


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com/).


## <a name="locate-and-assign-the-policy"></a>Het beleid zoeken en toewijzen

1. Ga in het Azure Portal naar **beleid**
1. Ga naar het deel venster **definities**
1. Klik op de vervolg keuzelijst **Categorieën** om de beschik bare opties weer te geven
1. Selecteer de optie automatisch **beheer – aanbevolen procedures voor virtuele Azure-machines inschakelen**
1. Nu wordt de lijst bijgewerkt om een ingebouwd beleid weer te geven met een naam die begint met automatisch *beheer inschakelen...*
1. Klik op de ingebouwde beleids naam aanbevolen procedures voor het samen *stellen van virtuele Azure-machines inschakelen*
1. Nadat u op het beleid hebt geklikt, ziet u nu het tabblad **definitie**

    > [!NOTE]
    > De definitie van de Azure Policy wordt gebruikt om para meters voor automanage in te stellen, zoals het configuratie profiel of het account. Er worden ook filters ingesteld waarmee wordt gegarandeerd dat het beleid alleen van toepassing is op de juiste Vm's.

1. Klik op de knop **toewijzen** om een toewijzing te maken
1. Vul op het tabblad **basis beginselen** het **bereik** in door het *abonnement* en de *resource groep* in te stellen

    > [!NOTE]
    > Met de scope kunt u definiëren op welke Vm's dit beleid van toepassing is. U kunt de toepassing instellen op abonnements niveau of op het niveau van de resource groep. Als u een resource groep instelt, hebben alle Vm's die zich momenteel in die resource groep bevinden of toekomstige Vm's die hieraan worden toegevoegd, automatisch beheer ingeschakeld. 

1. Klik op het tabblad **para meters** en stel het account voor automatische **beheer** en het gewenste **configuratie profiel** in 
1. Controleer de instellingen op het tabblad **controleren en maken**
1. Pas de toewijzing toe door op **maken** te klikken
1. Bekijk uw toewijzingen op het tabblad **toewijzingen** naast **definitie**

> [!NOTE]
> Het kan enige tijd duren voordat het beleid van kracht wordt op de virtuele machines in de resource groep of het abonnement.


## <a name="next-steps"></a>Volgende stappen 

Ontdek een andere manier om Azure automanage in te scha kelen voor virtuele machines via de Azure Portal. 

> [!div class="nextstepaction"]
> [Schakel automanage in voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)
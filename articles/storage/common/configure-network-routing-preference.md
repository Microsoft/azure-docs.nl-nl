---
title: Netwerkrouternigsvoorkeur configureren
titleSuffix: Azure Storage
description: Configureer voorkeurs netwerk routering voor uw Azure-opslag account om op te geven hoe netwerk verkeer via internet naar uw account wordt doorgestuurd.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 02/21/2020
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: fb427de170764e5cd1fca57f9fb2d1410829e521
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700843"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Voor keuren voor netwerk routering configureren voor Azure Storage

In dit artikel wordt beschreven hoe u de voor keuren voor netwerk Routering en route-specifieke eind punten voor uw opslag account kunt configureren. 

Met de voorkeurs methode voor netwerk routering geeft u op hoe netwerk verkeer via internet naar uw account wordt doorgestuurd. Route-specifieke eind punten zijn nieuwe eind punten die Azure Storage maken voor uw opslag account. Deze eind punten routeren verkeer via een gewenst pad zonder de standaard routerings voorkeur te wijzigen. Zie [netwerk routering voor Azure Storage voor](network-routing-preference.md)meer informatie.

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>De routerings voorkeur voor het standaard open bare eind punt configureren

De routerings voorkeur voor het open bare eind punt van het opslag account wordt standaard ingesteld op het globale netwerk van micro soft. U kunt kiezen tussen het globale netwerk van micro soft en Internet routering als de standaard routerings voorkeur voor het open bare eind punt van uw opslag account. Zie [netwerk routering voor Azure Storage voor](network-routing-preference.md)meer informatie over het verschil tussen deze twee typen route ring. 

De routerings voorkeur wijzigen in Internet routering:

1. Navigeer naar uw opslag account in de portal.

2. Kies onder **instellingen** de optie **netwerken**.

    > [!div class="mx-imgBorder"]
    > ![Menu optie netwerken](./media/configure-network-routing-preference/networking-option.png)

3.  Wijzig op het tabblad **firewalls en virtuele netwerken** onder **netwerk routering** de voorkeurs instelling voor **route ring** naar **Internet routering**.

4.  Klik op **Opslaan**.

    > [!div class="mx-imgBorder"]
    > ![routerings optie voor Internet](./media/configure-network-routing-preference/internet-routing-option.png)

## <a name="configure-a-route-specific-endpoint"></a>Een route-specifiek eind punt configureren

U kunt ook een route-specifiek eind punt configureren. U kunt bijvoorbeeld de routerings voorkeur voor het standaard eindpunt instellen op *Internet routering* en vervolgens een route-specifiek eind punt publiceren dat verkeer tussen clients op het Internet mogelijk maakt en uw opslag account kan worden gerouteerd via het globale micro soft-netwerk.

1.  Navigeer naar uw opslag account in de portal.

2.  Kies onder **instellingen** de optie **netwerken**.

3.  Kies op het tabblad **firewalls en virtuele netwerken** onder **route-specifieke eind punten publiceren** de routerings voorkeur van uw route-specifiek eind punt en klik vervolgens op **Opslaan**. Deze voor keur is alleen van invloed op het route-specifieke eind punt. Deze voor keur heeft geen invloed op uw standaard routerings voorkeur.  

    In de volgende afbeelding ziet u de geselecteerde **micro soft-netwerk routering** optie.

    > [!div class="mx-imgBorder"]
    > ![Routerings optie van micro soft-netwerk](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>De naam van het eind punt voor een route-specifiek eind punt zoeken

Als u een route-specifiek eind punt hebt geconfigureerd, kunt u het eind punt vinden in de eigenschappen van uw opslag account.

1.  Kies onder **instellingen** de optie **Eigenschappen**.

    > [!div class="mx-imgBorder"]
    > ![menu optie Eigenschappen](./media/configure-network-routing-preference/properties.png)

2.  Het eind punt van het **micro soft-netwerk routering** wordt weer gegeven voor elke service die ondersteuning biedt voor routerings voorkeuren. Deze afbeelding toont het eind punt voor de BLOB-en bestands Services.

    > [!div class="mx-imgBorder"]
    > ![Routerings optie van micro soft-netwerk voor route-specifieke eind punten](./media/configure-network-routing-preference/routing-url.png)


## <a name="see-also"></a>Zie ook

- [Voorkeurs netwerk routering](network-routing-preference.md)
- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md)
- [Beveiligings aanbevelingen voor Blob Storage](../blobs/security-recommendations.md)

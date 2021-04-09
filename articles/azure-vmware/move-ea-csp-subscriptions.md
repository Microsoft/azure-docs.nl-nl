---
title: Verplaats EA-en CSP Azure VMware-oplossings abonnementen
description: Meer informatie over hoe u de privécloud van het ene naar het andere abonnement kunt verplaatsen. De verplaatsing kan om verschillende redenen worden gemaakt, zoals facturering.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555389"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Verplaats EA-en CSP Azure VMware-oplossings abonnementen

In dit artikel leert u hoe u de privécloud van het ene naar het andere abonnement kunt verplaatsen. De verplaatsing kan om verschillende redenen worden gemaakt, zoals facturering. 

>[!IMPORTANT]
>U moet ten minste Inzender rechten hebben voor zowel de bron-als de doel abonnementen. VNet-en VNet-gateway kunnen niet van het ene abonnement naar het andere worden verplaatst. Bovendien heeft het verplaatsen van uw abonnementen geen invloed op het beheer en de werk belastingen, zoals de vCenter-, NSX-en workload-virtuele machines.

1. Meld u aan bij de Azure Portal en selecteer de privécloud die u wilt verplaatsen.

1. Selecteer de koppeling **abonnement (wijzigen)** .

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Scherm afbeelding van de details van de privécloud.":::

1. Geef de abonnements gegevens voor het **doel** op en selecteer **volgende**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Scherm afbeelding van de doel resource." lightbox="media/move-resources-subscription-target.png":::

1. Bevestig de validatie van de resources die u hebt geselecteerd om te verplaatsen en selecteer **volgende**. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Scherm afbeelding met de resource die wordt verplaatst." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Schakel het selectie vakje in om aan te geven dat de hulpprogram ma's en scripts die zijn gekoppeld, niet werken totdat u ze bijwerkt om de nieuwe resource-Id's te gebruiken. Selecteer vervolgens **verplaatsen**.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Scherm afbeelding met de samen vatting van de geselecteerde resource die wordt verplaatst. " lightbox="media/review-move-resources-subscription-target.png":::

   Er wordt een melding weer gegeven zodra de resource verplaatsing is voltooid. Het nieuwe abonnement wordt weer gegeven in het overzicht van de privécloud.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Scherm opname met een nieuw abonnement." lightbox="media/moved-subscription-target.png":::


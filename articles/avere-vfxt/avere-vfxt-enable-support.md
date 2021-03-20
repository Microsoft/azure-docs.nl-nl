---
title: Ondersteuning voor avere vFXT-Azure inschakelen
description: Meer informatie over het inschakelen van automatische upload van ondersteunings gegevens over uw cluster vanuit avere vFXT voor Azure om ondersteuning te bieden aan de klanten service.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/14/2019
ms.author: rohogue
ms.openlocfilehash: 93b99aa624a21d9312297e4279b1dcf053c79ae3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88272725"
---
# <a name="enable-support-uploads"></a>Ondersteuningsuploads inschakelen

AVERE vFXT voor Azure kan automatisch ondersteunings gegevens over uw cluster uploaden. Met deze uploads kunnen ondersteunings medewerkers de best mogelijke klanten service bieden.

## <a name="steps-to-enable-uploads"></a>Stappen voor het inschakelen van uploads

Voer de volgende stappen uit in het configuratie scherm van AVERE om ondersteuning te activeren. (Lees [toegang tot het vFXT-cluster](avere-vfxt-cluster-gui.md) voor meer informatie over het openen van het configuratie scherm.)

1. Ga bovenaan naar het tabblad **instellingen** .
1. Klik links op de koppeling **ondersteuning** en accepteer het privacybeleid.

   ![Scherm opname van het configuratie scherm van AVERE en het pop-upvenster met de knop bevestigen om het privacybeleid te accepteren](media/avere-vfxt-privacy-policy.png)

1. Open op de pagina ondersteunings configuratie de sectie **klant gegevens** door te klikken op de drie hoek aan de linkerkant.
1. Klik op de knop **Uploadgegevens opnieuw valideren**.
1. Stel de ondersteunings naam van het cluster in de **unieke naam** van het cluster in. Zorg ervoor dat deze naam uw cluster op unieke wijze identificeert ter ondersteuning van mede werkers.
1. Schakel de selectievakjes in voor **Controle van statistieken**, **Upload van algemene gegevens** en **Upload van crashgegevens**.
1. Klik op **Submit**

   ![Schermafbeelding met ingevulde sectie klantgegevens van de pagina instellingen voor ondersteuning](media/avere-vfxt-support-info.png)

1. Klik op de driehoek links naast **Secure Proactive Support (SPS)** om de sectie uit te vouwen.
1. Schakel het selectievakje **SPS Link inschakelen** in.
1. Klik op **Submit**

   ![Schermafbeelding met ingevulde sectie Secure Proactive Support op de pagina met ondersteuningsinstellingen](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Volgende stappen

Als u een on-premises of een bestaand Cloud opslag systeem aan het cluster wilt toevoegen, volgt u de instructies in [opslag configureren](avere-vfxt-add-storage.md).

Als u klaar bent om aan de slag te gaan met het koppelen van clients aan het cluster, lees dan [het avere vFXT-cluster koppelen](avere-vfxt-mount-clients.md).

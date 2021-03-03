---
title: Uw Azure Stack Edge Pro-apparaat opnieuw instellen en opnieuw activeren | Microsoft Docs
description: Meer informatie over het wissen van de gegevens van en het opnieuw activeren van uw Azure Stack Edge Pro-apparaat.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2020
ms.author: alkohli
ms.openlocfilehash: 4026bac9818b14c33c05d99caff4052adad196c3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745842"
---
# <a name="reset-and-reactivate-your-azure-stack-edge-pro-device"></a>Uw Azure Stack Edge Pro-apparaat opnieuw instellen en opnieuw activeren

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In dit artikel wordt beschreven hoe u een Azure Stack Edge Pro-apparaat opnieuw instelt, opnieuw configureert en opnieuw activeert als u problemen ondervindt met het apparaat of om een andere reden moet worden gestart.

Nadat u het apparaat opnieuw hebt ingesteld om de gegevens te verwijderen, moet u het apparaat opnieuw activeren als een nieuwe resource. Als u een apparaat opnieuw instelt, wordt de configuratie van het apparaat verwijderd. u moet het apparaat dus opnieuw configureren via de lokale webgebruikersinterface.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * De gegevens van de gegevens schijven op het apparaat wissen
> * Het apparaat opnieuw activeren door een nieuwe order te maken, het apparaat opnieuw te configureren en het te activeren

## <a name="reset-data-from-the-device"></a>Gegevens opnieuw instellen vanaf het apparaat

Als u de gegevens van de gegevens schijven van uw apparaat wilt wissen, moet u uw apparaat opnieuw instellen. 

Voordat u opnieuw instelt, maakt u indien nodig een kopie van de lokale gegevens op het apparaat. U kunt de gegevens van het apparaat naar een Azure Storage-container kopiëren.

>[!IMPORTANT]
> Als u uw apparaat opnieuw instelt, worden alle lokale gegevens en workloads van uw apparaat gewist en kunnen ze niet worden omgekeerd. Stel uw apparaat alleen opnieuw in als u afresh met het apparaat wilt starten.

U kunt uw apparaat opnieuw instellen in de lokale webgebruikersinterface of in Power shell. Zie [uw apparaat opnieuw instellen](./azure-stack-edge-connect-powershell-interface.md#reset-your-device)voor instructies voor Power shell.

[! INCLUDe] [gegevens van het apparaat opnieuw instellen](../../includes/azure-stack-edge-device-reset.md)

## <a name="reactivate-device"></a>Apparaat opnieuw activeren

Nadat u het apparaat opnieuw hebt ingesteld, moet u het apparaat opnieuw activeren als een nieuwe resource. Nadat u een nieuwe order hebt geplaatst, moet u de nieuwe resource opnieuw configureren en opnieuw activeren.

Voer de volgende stappen uit om uw bestaande apparaat opnieuw te activeren:

1. Maak een nieuwe volg orde voor het bestaande apparaat door de stappen in [een nieuwe resource maken](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#create-a-new-resource)te volgen. Selecteer op het tabblad **Verzend adres** de optie **Ik heb al een apparaat**.

   ![Geen nieuw apparaat opgeven in het verzend adres](./media/azure-stack-edge-reset-reactivate-device/create-resource-with-no-new-device.png)

1. [De activerings sleutel ophalen](azure-stack-edge-gpu-deploy-prep?tabs=azure-portal#get-the-activation-key).

1. [Verbinding maken met het apparaat](azure-stack-edge-gpu-deploy-connect.md).

1. [Configureer het netwerk voor het apparaat](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).

1. [Apparaatinstellingen configureren](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).

1. [Certificaten configureren](azure-stack-edge-gpu-deploy-configure-certificates.md).

1. [Activeer het apparaat](databox-online/azure-stack-edge-gpu-deploy-activate.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van verbinding met een Azure stack Edge Pro-apparaat](azure-stack-edge-gpu-deploy-connect.md).

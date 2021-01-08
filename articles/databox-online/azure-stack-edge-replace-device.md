---
title: Uw Azure Stack Edge Pro-apparaat vervangen | Microsoft Docs
description: Hierin wordt beschreven hoe u een vervangend Azure Stack Edge Pro-apparaat ophaalt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: 501136c6f5015dc8c68a1aba004432900675b11c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017966"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Uw Azure Stack Edge Pro-apparaat vervangen

In dit artikel wordt beschreven hoe u een vervangend Azure Stack Edge Pro-apparaat kunt ophalen. Er is een vervangend apparaat nodig wanneer het bestaande apparaat een hardwarestoring heeft of een upgrade nodig heeft. 


In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een ondersteunings ticket voor hardware-probleem openen
> * Een nieuwe resource maken voor een vervangend apparaat in Azure Portal
> * Installeren, het vervangende apparaat activeren
> * Het oorspronkelijke apparaat retour neren

## <a name="open-a-support-ticket"></a>Een ondersteunings ticket openen

Als uw bestaande apparaat een hardwarestoring heeft, opent u een ondersteunings ticket. Microsoft Ondersteuning stelt vast dat er geen FRU (vervangend onderdeel) beschikbaar is voor dit exemplaar, of dat het apparaat een hardware-upgrade nodig heeft. In beide gevallen zal ondersteuning een vervangend apparaat best Ellen.

1. Open een ondersteunings ticket met Microsoft Ondersteuning dat aangeeft dat u het apparaat wilt retour neren. Selecteer het probleem type als **Azure stack Edge Pro-hardware**.

    ![Ondersteuningsticket openen](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Een Microsoft Ondersteuning-Engineer neemt contact met u op. Geef de verzend gegevens op.
<!--3. If you need a return shipping box, you can request it. Answer **Yes** to the question **Need an empty box to return**.-->


## <a name="create-a-resource-for-replacement-device"></a>Een resource maken voor een vervangend apparaat

> [!NOTE]
> Maak een nieuwe resource voor de activering van uw vervangende apparaat. Het is niet mogelijk om een vervangend apparaat te activeren op basis van een bestaande resource.

Volg deze stappen om een resource te maken.

1. Volg de stappen in [een nieuwe resource maken](azure-stack-edge-deploy-prep.md#create-a-new-resource) om een resource te maken voor het vervangende apparaat. 

2. Zorg ervoor dat u het selectie vakje inschakelt voor **een Azure stack Edge Pro-apparaat**. 

    ![Resource voor vervangend apparaat](media/azure-stack-edge-replace-device/replace-resource-1.png)  

## <a name="install-and-activate-the-replacement-device"></a>Het vervangende apparaat installeren en activeren

Voer de volgende stappen uit om het vervangende apparaat te installeren en te activeren:

1. [Installeer uw apparaat](azure-stack-edge-deploy-install.md).

2. [Activeer uw apparaat](azure-stack-edge-deploy-connect-setup-activate.md) met de nieuwe resource die u eerder hebt gemaakt.

## <a name="return-your-existing-device"></a>Uw bestaande apparaat retour neren

Volg alle stappen om het oorspronkelijke apparaat te retour neren:

1. [De gegevens op het apparaat wissen](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. Het [retour neren van het apparaat starten](azure-stack-edge-return-device.md#initiate-device-return) voor het oorspronkelijke apparaat.
3. [Een ophaling plannen](azure-stack-edge-return-device.md#schedule-a-pickup).
4. [Verwijder de resource](azure-stack-edge-return-device.md#delete-the-resource) die aan het geretourneerde apparaat is gekoppeld.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [retour neren van een Azure stack Edge Pro-apparaat](azure-stack-edge-return-device.md).

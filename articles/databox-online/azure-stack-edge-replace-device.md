---
title: Uw Azure Stack Edge Pro-apparaat vervangen | Microsoft Docs
description: Hierin wordt beschreven hoe u een vervangend Azure Stack Edge Pro-apparaat ophaalt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 91cb446da31f353162f90778d855056a9697d455
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726566"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Uw Azure Stack Edge Pro-apparaat vervangen

In dit artikel wordt beschreven hoe u uw Azure Stack Edge Pro-apparaat vervangt. Er is een vervangend apparaat nodig wanneer het bestaande apparaat een hardwarestoring heeft of een upgrade nodig heeft. 


In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Een ondersteunings ticket voor hardware-probleem openen
> * Een nieuwe order maken voor een vervangend apparaat in de Azure Portal
> * Installeren, het vervangende apparaat activeren
> * Het oorspronkelijke apparaat retour neren

## <a name="open-a-support-ticket"></a>Een ondersteunings ticket openen

Als uw bestaande apparaat een hardwarestoring heeft, opent u een ondersteunings ticket door de volgende stappen uit te voeren:

1. Open een ondersteunings ticket met Microsoft Ondersteuning dat aangeeft dat u het apparaat wilt retour neren. Selecteer het type **Azure stack Edge Pro-hardwareprobleem** en kies het subtype **hardwareproblemen** .  

    ![Ondersteuningsticket openen](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. Een Microsoft Ondersteuning-Engineer neemt contact met u op om te bepalen of een veld vervangings eenheid (FRU) het probleem kan oplossen en beschikbaar is voor dit exemplaar. Als een FRU niet beschikbaar is of als er een hardware-upgrade voor het apparaat is vereist, wordt u door de ondersteuning geleid om een nieuwe order te plaatsen en uw oude apparaat te retour neren.

## <a name="create-a-new-order"></a>Een nieuwe order maken

Maak een nieuwe resource voor de activering van uw vervangende apparaat door de stappen in [een nieuwe resource maken](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource)te volgen.

> [!NOTE]
> Het is niet mogelijk om een vervangend apparaat te activeren op basis van een bestaande resource. De nieuwe resource wordt beschouwd als een nieuwe order. U begint 14 dagen na verzen ding van het apparaat te worden gefactureerd.

## <a name="install-and-activate-the-replacement-device"></a>Het vervangende apparaat installeren en activeren

Voer de volgende stappen uit om het vervangende apparaat te installeren en te activeren:

1. [Installeer uw apparaat](azure-stack-edge-deploy-install.md).
2. [Activeer uw apparaat](azure-stack-edge-deploy-connect-setup-activate.md) met de nieuwe resource die u eerder hebt gemaakt.

## <a name="return-your-existing-device"></a>Uw bestaande apparaat retour neren

Volg alle stappen om het oorspronkelijke apparaat te retour neren:

1. [De gegevens op het apparaat wissen](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. Het [retour neren van het apparaat starten](azure-stack-edge-return-device.md#initiate-device-return) voor het oorspronkelijke apparaat.
3. [Een ophaling plannen](azure-stack-edge-return-device.md#schedule-a-pickup).
4. Zodra het apparaat is ontvangen bij micro soft, kunt u [de resource verwijderen](azure-stack-edge-return-device.md#delete-the-resource) die aan het geretourneerde apparaat is gekoppeld.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [retour neren van een Azure stack Edge Pro-apparaat](azure-stack-edge-return-device.md).

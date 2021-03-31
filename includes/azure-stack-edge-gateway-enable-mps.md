---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102517553"
---
1. Zorg voordat u begint voor het volgende:

    1. U hebt [uw Azure stack Edge Pro-apparaat](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) geconfigureerd en geactiveerd met een Azure stack Edge-resource in Azure.
    1. U hebt [Compute op dit apparaat geconfigureerd in de Azure Portal](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Verbinding maken met de Power shell-interface](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Gebruik de volgende opdracht om MPS op uw apparaat in te scha kelen.

    ```powershell
    Start-HcsGpuMPS
    ```
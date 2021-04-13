---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/09/2021
ms.author: alkohli
ms.openlocfilehash: 1298673c475a0308f1db27641aae93837e6b5df3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305550"
---
Zorg ervoor dat de volgende stappen kunnen worden gebruikt om toegang te krijgen tot het apparaat vanaf uw client.

Controleer of de client verbinding kan maken met de lokale Azure Resource Manager. 

1. Api's van het lokale apparaat aanroepen om te verifiëren:

    ```powershell
    login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d  
    ```

1. Geef de gebruikers naam `EdgeArmUser` en het wacht woord op om verbinding te maken via Azure Resource Manager. Als u het wacht woord niet intrekt, [stelt u het wacht woord voor Azure Resource Manager opnieuw](../articles/databox-online/azure-stack-edge-gpu-set-azure-resource-manager-password.md) in en gebruikt u dit wacht woord om u aan te melden.
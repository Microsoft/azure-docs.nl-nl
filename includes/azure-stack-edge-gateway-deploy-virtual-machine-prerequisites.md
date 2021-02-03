---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500149"
---
Voordat u Vm's op uw Azure Stack edge-apparaat kunt implementeren, moet u uw client configureren om verbinding te maken met het apparaat via Azure Resource Manager over Azure PowerShell. Ga voor gedetailleerde stappen naar [verbinding maken met Azure Resource Manager op uw Azure stack edge-apparaat](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Zorg ervoor dat de volgende stappen kunnen worden gebruikt om toegang te krijgen tot het apparaat vanaf uw client. (U hebt deze configuratie uitgevoerd tijdens het verbinden met Azure Resource Manager. U gaat nu gewoon controleren of de configuratie is geslaagd.) 

1. Controleer of Azure Resource Manager communicatie werkt. Voer het volgende in:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. De Api's van het lokale apparaat aanroepen om te verifiëren. Voer het volgende in: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Geef de gebruikers naam- *EdgeARMuser* en het wacht woord op om verbinding te maken via Azure Resource Manager.

1. Als u **Compute** voor Kubernetes hebt geconfigureerd, kunt u deze stap overs Laan. Ga door om te controleren of u een netwerk interface voor Compute hebt ingeschakeld. Ga in de lokale gebruikers interface naar **computer** instellingen. Selecteer de netwerk interface die u wilt gebruiken voor het maken van een virtuele switch. De Vm's die u maakt, worden gekoppeld aan een virtuele switch die is gekoppeld aan deze poort en het bijbehorende netwerk. Zorg ervoor dat u een netwerk kiest dat overeenkomt met het IP-adres dat u voor de virtuele machine gaat gebruiken.  

    ![Scherm afbeelding die laat zien hoe reken instellingen worden ingeschakeld.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Schakel Compute in op de netwerkinterface. Azure Stack Edge maakt en beheert een virtuele switch die overeenkomt met die netwerk interface. Voer op dit moment geen specifieke IP-adressen in voor Kubernetes. Het kan enkele minuten duren om de berekening in te scha kelen.

    > [!NOTE]
    > Als u GPU-Vm's maakt, selecteert u een netwerk interface die is verbonden met internet. Hiermee kunt u een GPU-extensie op uw apparaat installeren.



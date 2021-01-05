---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763422"
---
Voordat u Vm's op uw Azure Stack edge-apparaat kunt implementeren, moet u uw client configureren om verbinding te maken met het apparaat via Azure Resource Manager over Azure PowerShell. Ga voor gedetailleerde stappen naar [verbinding maken met Azure Resource Manager op uw Azure stack edge-apparaat](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).


Zorg ervoor dat de volgende stappen kunnen worden gebruikt om toegang te krijgen tot het apparaat vanaf uw client (u hebt deze configuratie uitgevoerd wanneer u verbinding maakt met Azure Resource Manager, controleert u gewoon of de configuratie is geslaagd.): 

1. Controleer of Azure Resource Manager communicatie werkt. Type:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. De Api's van het lokale apparaat aanroepen om te verifiëren. Type: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Geef de gebruikers naam- *EdgeARMuser* en het wacht woord op om verbinding te maken via Azure Resource Manager.

1. Als u **Compute** voor Kubernetes hebt geconfigureerd, kunt u deze stap overs Laan. Ga door om te controleren of u een netwerk interface voor Compute hebt ingeschakeld. Ga in lokale gebruikers interface naar **computer** instellingen. Selecteer de netwerkinterface die u wilt gebruiken om een virtuele switch te maken. De Vm's die u maakt, worden gekoppeld aan een virtuele switch die is gekoppeld aan deze poort en het bijbehorende netwerk. Zorg ervoor dat u een netwerk kiest dat overeenkomt met het IP-adres dat u voor de virtuele machine gaat gebruiken.  

    ![Reken instellingen inschakelen 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Schakel Compute in op de netwerkinterface. Azure Stack Edge maakt en beheert een virtuele switch die overeenkomt met die netwerk interface. Voer op dit moment geen specifieke IP-adressen in voor Kubernetes. Het kan enkele minuten duren om de berekening in te scha kelen.

    > [!NOTE]
    > Als u GPU-Vm's maakt, selecteert u een netwerk interface die is verbonden met internet. Hiermee kunt u de GPU-extensie op uw apparaat installeren.


1. Schakel de VM-rol in van de Azure Portal. Met deze stap maakt u een uniek abonnement voor uw apparaat dat wordt gebruikt voor het maken van Vm's via de lokale Api's van het apparaat. 

    1. Als u de VM-functie wilt inschakelen, gaat u in het Azure Portal naar de Azure Stack Edge-resource voor uw Azure Stack edge-apparaat. Ga naar **Edge compute > virtual machines**.

        ![VM-installatie kopie 1 toevoegen](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Selecteer **virtual machines** om naar de pagina **overzicht** te gaan. Cloud beheer van virtuele machines **inschakelen** .
        ![VM-installatie kopie 2 toevoegen](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)
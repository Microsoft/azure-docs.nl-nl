---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 71d5a910e36762d096763c4f45a13cbdad47414d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730618"
---
Voordat u Vm's op uw Azure Stack edge-apparaat kunt implementeren, moet u uw client configureren om verbinding te maken met het apparaat via Azure Resource Manager over Azure PowerShell. Zie [verbinding maken met Azure Resource Manager op uw Azure stack edge-apparaat](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md)voor gedetailleerde instructies.

Zorg ervoor dat u de volgende stappen kunt gebruiken om toegang te krijgen tot het apparaat vanaf uw client. U hebt deze configuratie al uitgevoerd toen u verbinding hebt gemaakt met Azure Resource Manager en u controleert nu of de configuratie is geslaagd. 

1. Controleer of Azure Resource Manager communicatie werkt door de volgende opdracht uit te voeren:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Als u de Api's van het lokale apparaat wilt aanroepen voor verificatie, voert u het volgende in: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Als u verbinding wilt maken via Azure Resource Manager, geeft u de gebruikers naam *EdgeARMuser* en uw wacht woord op.

1. Als u Compute voor Kubernetes hebt geconfigureerd, kunt u deze stap overs Laan. Anders moet u ervoor zorgen dat u een netwerk interface voor Compute hebt ingeschakeld door de volgende handelingen uit te voeren: 

   a. Ga in uw lokale gebruikers interface naar **computer** instellingen.  
   b. Selecteer de netwerk interface die u wilt gebruiken voor het maken van een virtuele switch. De Vm's die u maakt, worden gekoppeld aan een virtuele switch die is gekoppeld aan deze poort en het bijbehorende netwerk. Zorg ervoor dat u een netwerk kiest dat overeenkomt met het IP-adres dat u voor de virtuele machine gaat gebruiken.  

    ![Scherm opname van het deel venster instellingen van het configuratie netwerk.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. Selecteer onder **inschakelen voor Compute** op de netwerk interface de optie **Ja**. Met Azure Stack Edge wordt een virtuele switch gemaakt en beheerd die overeenkomt met die netwerk interface. Voer op dit moment geen specifieke IP-adressen in voor Kubernetes. Het kan enkele minuten duren om de berekening in te scha kelen.

    > [!NOTE]
    > Als u GPU-Vm's maakt, selecteert u een netwerk interface die is verbonden met internet. Hierdoor kunt u een GPU-extensie op uw apparaat installeren.



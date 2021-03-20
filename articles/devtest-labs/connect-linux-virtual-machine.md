---
title: Verbinding maken met uw virtuele Linux-machines in Azure DevTest Labs
description: Meer informatie over het maken van verbinding met uw virtuele Linux-machine in een Lab (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86531651"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Verbinding maken met een virtuele Linux-machine in uw Lab (Azure DevTest Labs)
In dit artikel wordt beschreven hoe u verbinding maakt met een virtuele Linux-machine in uw Lab. 

## <a name="connect-to-a-linux-vm"></a>Verbinding maken met een Linux-VM
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek en selecteer **DevTest Labs** in de zoek balk. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="DevTest Labs zoeken en selecteren":::    
1. Selecteer in de lijst met Labs uw **Lab**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Uw Lab selecteren":::            
1. Selecteer op de start pagina van uw Lab uw virtuele Linux-machine in de lijst **mijn virtual machines** . 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Selecteer uw virtuele Linux-machine":::        
5. Op de pagina **overzicht** kunt u de Fully QUALIFIED domain name (FQDN) of het IP-adres van de virtuele machine bekijken. U kunt ook de poort zien, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Fully Qualified Domain name voor de virtuele machine":::    

    U ziet dat de knop **verbinding maken** grijs wordt weer gegeven, zelfs als de virtuele machine is gestart. Dat is een ontwerp.
6.  Gebruik SSH om verbinding te maken met uw virtuele Linux-machine. In het volgende voor beeld wordt verbinding gemaakt met de virtuele machine met FQDN `mydtl07172452621450000.eastus.cloudapp.azure.com` , met de gebruikers naam `vmuser` en de poort `51637` . Voer het wacht woord in voor de gebruiker om verbinding te maken met de virtuele machine. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    U kunt hulpprogram ma's zoals [putty](https://www.putty.org/) of een andere SSH-client gebruiken om verbinding te maken met de virtuele machine. 

    Nadat u verbinding hebt gemaakt via SSH, kunt u een bureaublad omgeving ([xfce](https://www.xfce.org)) en extern bureau blad ([xrdp](http://xrdp.org)) installeren en configureren.  Zie [extern bureaublad installeren en configureren om verbinding te maken met een virtuele Linux-machine in azure](../virtual-machines/linux/use-remote-desktop.md)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen
[Procedure: verbinding maken met een virtuele Windows-machine](connect-windows-virtual-machine.md)

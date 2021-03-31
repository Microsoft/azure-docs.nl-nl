---
title: Gedeelde IP-adressen in Azure DevTest Labs begrijpen | Microsoft Docs
description: Meer informatie over hoe Azure DevTest Labs gedeelde IP-adressen gebruikt om de open bare IP-adressen te minimaliseren die nodig zijn voor toegang tot uw Lab-Vm's.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 36a5da1b2b6252d0adb480a622c461b33425e675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85484091"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Gedeelde IP-adressen in Azure DevTest Labs begrijpen

Azure DevTest Labs kunnen Lab-Vm's hetzelfde open bare IP-adres delen om het aantal open bare IP-adressen te beperken dat nodig is voor toegang tot uw afzonderlijke Lab-Vm's.  In dit artikel wordt beschreven hoe gedeelde IP-adressen en de bijbehorende configuratie opties worden gebruikt.

## <a name="shared-ip-setting"></a>Gedeelde IP-instelling

Wanneer u een Lab maakt, wordt dit gemaakt in een subnet van een virtueel netwerk.  Dit subnet wordt standaard gemaakt met de **optie Gedeelde open bare IP inschakelen** ingesteld op *Ja*.  Deze configuratie maakt één openbaar IP-adres voor het hele subnet.  Zie [Configure a Virtual Network in azure DevTest Labs](devtest-lab-configure-vnet.md)voor meer informatie over het configureren van virtuele netwerken en subnetten.

![Nieuw Lab-subnet](media/devtest-lab-shared-ip/lab-subnet.png)

Voor bestaande Labs kunt u deze optie inschakelen door **configuratie en beleid > virtuele netwerken** te selecteren. Selecteer vervolgens een virtueel netwerk in de lijst en kies **gedeeld openbaar IP-adres inschakelen** voor een geselecteerd subnet. U kunt deze optie ook uitschakelen in een Lab als u geen openbaar IP-adres wilt delen via Lab-Vm's.

Alle Vm's die zijn gemaakt in deze Lab, worden standaard gebruikt voor het gebruik van een gedeeld IP-adres.  Bij het maken van de virtuele machine kan deze instelling worden waargenomen op de pagina **Geavanceerde instellingen** onder **IP-adres configuratie**.

![Nieuwe VM](media/devtest-lab-shared-ip/new-vm.png)

- **Gedeeld:** Alle Vm's die zijn gemaakt als **gedeeld** , worden in één resource groep (RG) geplaatst. Er wordt één IP-adres toegewezen voor die RG en alle virtuele machines in RG zullen dat IP-adres gebruiken.
- **Openbaar:** Elke VM die u maakt, heeft een eigen IP-adres en wordt gemaakt in een eigen resource groep.
- **Privé:** Elke virtuele machine die u maakt, maakt gebruik van een privé-IP-adres. U kunt niet rechtstreeks via Internet verbinding maken met deze virtuele machine met Extern bureaublad.

Wanneer een virtuele machine waarop gedeeld IP-adres is ingeschakeld, wordt toegevoegd aan het subnet, voegt DevTest Labs de virtuele machine automatisch toe aan een load balancer en wordt een TCP-poort nummer op het open bare IP-adressen toegewezen en doorgestuurd naar de RDP-poort op de virtuele machine.  

## <a name="using-the-shared-ip"></a>Het gedeelde IP-adres gebruiken

- **Linux-gebruikers:** SSH naar de virtuele machine met behulp van het IP-adres of Fully Qualified Domain Name, gevolgd door een dubbele punt, gevolgd door de poort. In de onderstaande afbeelding ziet u bijvoorbeeld het RDP-adres om verbinding te maken met de virtuele machine `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661` .

  ![VM-voor beeld](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-gebruikers:** Selecteer de knop **verbinding maken** op het Azure Portal om een vooraf geconfigureerd RDP-bestand te downloaden en toegang te krijgen tot de virtuele machine.

## <a name="next-steps"></a>Volgende stappen

* [Lab-beleid definiëren in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Een virtueel netwerk configureren in Azure DevTest Labs](devtest-lab-configure-vnet.md)






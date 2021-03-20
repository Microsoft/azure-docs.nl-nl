---
title: Verbinding maken met een Windows-schaalset voor virtuele machines met behulp van Azure Bastion | Microsoft Docs
description: In dit artikel leert u hoe u verbinding kunt maken met een virtuele-machine schaalset van Azure met behulp van Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: charwen
ms.openlocfilehash: 001d2ff6789ec4cfcc391171f0859b67ab1ee0a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077776"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Verbinding maken met een virtuele-machineschaalset met behulp van Azure Bastion

In dit artikel wordt beschreven hoe u veilig en naadloos RDP kunt gebruiken voor uw Windows virtual machine Scale set-exemplaar in een virtueel Azure-netwerk met behulp van Azure Bastion. U kunt rechtstreeks vanuit het Azure Portal verbinding maken met een virtuele machine Scale set-exemplaar. Wanneer u Azure Bastion gebruikt, is er geen client, agent of extra software vereist voor Vm's. Zie het [overzicht](bastion-overview.md)voor meer informatie over Azure Bastion.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u een Azure bastion-host hebt ingesteld voor het virtuele netwerk waarin de schaalset van de virtuele machine zich bevindt. Zie [een Azure bastion-host maken](./tutorial-create-host-portal.md)voor meer informatie. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om verbinding te maken met een virtuele machine Scale set-exemplaar in dit virtuele netwerk. Bastion gaat ervan uit dat u RDP gebruikt om verbinding te maken met een virtuele-machineset van Windows en SSH om verbinding te maken met de schaalset voor virtuele Linux-machines. Zie [verbinding maken met een VM-Linux](bastion-connect-vm-ssh.md)voor meer informatie over verbinding met een virtuele Linux-machine.

## <a name="connect-using-rdp"></a><a name="rdp"></a>Verbinding maken via RDP

1. Open de [Azure Portal](https://portal.azure.com). Ga naar de schaalset voor virtuele machines waarmee u verbinding wilt maken.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Navigeer naar het exemplaar van de virtuele-machine Scale set waarmee u verbinding wilt maken en selecteer vervolgens **verbinding maken**. Wanneer u een RDP-verbinding gebruikt, moet de schaalset van de virtuele machine een Windows-schaalset voor virtuele machines zijn.

   ![schaalset voor virtuele machines](./media/bastion-connect-vm-scale-set/2.png)
3. Nadat u **verbinding maken** hebt geselecteerd, wordt er een kantlijn balk met drie TABBLADEN: RDP, SSH en Bastion. Selecteer het tabblad **Bastion** in de balk aan de zijkant. Als u Bastion niet hebt ingericht voor het virtuele netwerk, kunt u de koppeling selecteren om Bastion te configureren. Zie [Bastion configureren](./tutorial-create-host-portal.md)voor configuratie-instructies.

   ![Tabblad Bastion](./media/bastion-connect-vm-scale-set/3.png)
4. Voer op het tabblad Bastion de gebruikers naam en het wacht woord in voor de schaalset van de virtuele machine en selecteer vervolgens **verbinding maken**.

   ![verbinding maken](./media/bastion-connect-vm-scale-set/4.png)
5. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).
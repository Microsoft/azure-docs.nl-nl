---
title: Verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion
description: In dit artikel leert u hoe u verbinding kunt maken met een virtuele Linux-machine met behulp van Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: a5cda5d4d447bc04f853ea4a9abd15be75e7e177
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588657"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Via SSH verbinding maken met een virtuele Linux-machine met behulp van Azure Bastion

Dit artikel laat u zien hoe u veilig en naadloos SSHeert met uw virtuele Linux-machines in een virtueel Azure-netwerk. U kunt rechtstreeks vanuit het Azure Portal verbinding maken met een virtuele machine. Wanneer u Azure Bastion gebruikt, is er geen client, agent of extra software vereist voor Vm's. Zie het [overzicht](bastion-overview.md)voor meer informatie over Azure Bastion.

U kunt Azure Bastion gebruiken om verbinding te maken met een virtuele Linux-machine via SSH. U kunt de gebruikers naam en het wacht woord en de SSH-sleutels voor verificatie gebruiken. U kunt met SSH-sleutels verbinding maken met uw virtuele machine met behulp van:

* Een persoonlijke sleutel die u hand matig invoert
* Een bestand dat de persoonlijke sleutel gegevens bevat

De persoonlijke SSH-sleutel moet een indeling hebben die begint met  `"-----BEGIN RSA PRIVATE KEY-----"` en eindigt met `"-----END RSA PRIVATE KEY-----"` .

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u een Azure bastion-host hebt ingesteld voor het virtuele netwerk waarin de VM zich bevindt. Zie [een Azure bastion-host maken](./tutorial-create-host-portal.md)voor meer informatie. Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om verbinding te maken met elke virtuele machine in dit virtuele netwerk. 

Wanneer u Bastion gebruikt om verbinding te maken, wordt ervan uitgegaan dat u RDP gebruikt om verbinding te maken met een Windows-VM, en SSH om verbinding te maken met uw Linux-Vm's. Zie [verbinding maken met een VM-Windows](bastion-connect-vm-rdp.md)voor meer informatie over het maken van een verbinding met een virtuele Windows-machine.

### <a name="required-roles"></a>Vereiste rollen

De volgende rollen zijn vereist om verbinding te maken:

* De rol van Lezer op de virtuele machine
* De rol van Lezer op de NIC met het privé-IP-adres van de virtuele machine
* De rol van Lezer in de Azure Bastion-resource

### <a name="ports"></a>Poorten

Om via SSH verbinding te maken met de virtuele Linux-machine, moeten de volgende poorten zijn geopend op uw VM:

* Binnenkomende poort: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Verbinding maken: gebruikers naam en wacht woord gebruiken

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken** en selecteer **Bastion** in de vervolg keuzelijst.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Scherm afbeelding toont het overzicht van een virtuele machine in Azure Portal met geselecteerde verbinding":::
1. Nadat u Bastion hebt geselecteerd, klikt u op **Bastion gebruiken**. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](./quickstart-host-portal.md).
1. Voer op de pagina **verbinding maken met Azure Bastion** de **gebruikers naam** en het **wacht woord** in.

   :::image type="content" source="./media/bastion-connect-vm-ssh/password.png" alt-text="Wachtwoordverificatie":::
1. Selecteer **verbinding maken** om verbinding te maken met de virtuele machine.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Verbinden: Voer een persoonlijke sleutel hand matig in

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken** en selecteer **Bastion** in de vervolg keuzelijst.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Scherm afbeelding toont het overzicht van een virtuele machine in Azure Portal met geselecteerde verbinding":::
1. Nadat u Bastion hebt geselecteerd, klikt u op **Bastion gebruiken**. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](./quickstart-host-portal.md).
1. Voer op de pagina **verbinding maken met Azure Bastion** de **gebruikers naam** en de **persoonlijke SSH-sleutel** in.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-private-key.png" alt-text="Verificatie van de persoonlijke SSH-sleutel":::
1. Voer uw persoonlijke sleutel in de **persoonlijke SSH-sleutel** van het tekst gebied in (of plak deze direct).
1. Selecteer **verbinding maken** om verbinding te maken met de virtuele machine.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Verbinding maken: een persoonlijke-sleutel bestand gebruiken

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken** en selecteer **Bastion** in de vervolg keuzelijst.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Scherm afbeelding toont het overzicht van een virtuele machine in Azure Portal met geselecteerde verbinding":::
1. Nadat u Bastion hebt geselecteerd, klikt u op **Bastion gebruiken**. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](./quickstart-host-portal.md).
1. Voer op de pagina **verbinding maken met Azure Bastion** de **gebruikers naam** en de **persoonlijke SSH-sleutel van het lokale bestand** in.

   :::image type="content" source="./media/bastion-connect-vm-ssh/private-key-file.png" alt-text="Bestand met persoonlijke SSH-sleutel":::

1. Blader naar het bestand en selecteer **openen**.
1. Selecteer **verbinding maken** om verbinding te maken met de virtuele machine. Nadat u op verbinding maken hebt geklikt, wordt SSH naar deze virtuele machine rechtstreeks in de Azure Portal geopend. Deze verbinding is meer dan HTML5 via poort 443 van de Bastion-service via het privé-IP-adres van uw virtuele machine.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Verbinding maken: een persoonlijke sleutel die is opgeslagen in Azure Key Vault

>[!NOTE]
>De portal-update voor deze functie wordt momenteel geïmplementeerd naar regio's.
>

1. Open de [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en klik vervolgens op **verbinding maken** en selecteer **Bastion** in de vervolg keuzelijst.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Scherm afbeelding toont het overzicht van een virtuele machine in Azure Portal met geselecteerde verbinding":::
1. Nadat u Bastion hebt geselecteerd, klikt u op **Bastion gebruiken**. Als u Bastion niet hebt ingericht voor het virtuele netwerk, raadpleegt u [Bastion configureren](./quickstart-host-portal.md).
1. Op de pagina **verbinding maken met Azure Bastion voert u** de **gebruikers naam** in en selecteert u de **persoonlijke SSH-sleutel van Azure Key Vault**.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-key-vault.png" alt-text="Persoonlijke SSH-sleutel van Azure Key Vault":::
1. Selecteer de vervolg keuzelijst **Azure Key Vault** en selecteer de resource waarin u uw persoonlijke SSH-sleutel hebt opgeslagen. Als u geen Azure Key Vault resource hebt ingesteld, raadpleegt u [een sleutel kluis maken](../key-vault/general/quick-create-portal.md) en uw persoonlijke SSH-sleutel opslaan als waarde voor een nieuw Key Vault geheim.

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

   Zorg ervoor dat u beschikt over een **lijst** en toegang **krijgt** tot de geheimen die zijn opgeslagen in de Key Vault resource. Zie [een Key Vault toegangs beleid toewijzen](../key-vault/general/assign-access-policy-portal.md)om toegangs beleid voor uw Key Vault-bron toe te wijzen en te wijzigen.
1. Selecteer de vervolg keuzelijst **Azure Key Vault geheim** en selecteer het Key Vault geheim met de waarde van uw persoonlijke SSH-sleutel.
1. Selecteer **verbinding maken** om verbinding te maken met de virtuele machine. Nadat u op **verbinding maken** hebt geklikt, wordt SSH naar deze virtuele machine rechtstreeks in de Azure portal geopend. Deze verbinding is meer dan HTML5 via poort 443 van de Bastion-service via het privé-IP-adres van uw virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen over Bastion](bastion-faq.md)voor meer informatie over Azure Bastion. 

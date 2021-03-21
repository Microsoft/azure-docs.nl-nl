---
title: bestand opnemen
description: bestand opnemen
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92521527"
---
1. Open [Azure Portal](https://portal.azure.com). Navigeer naar de virtuele machine waarmee u verbinding wilt maken en selecteer vervolgens **Verbinding maken**. Selecteer **Bastion** in de vervolgkeuzelijst.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Bastion selecteren":::

1. Nadat u Bastion in de vervolgkeuzelijst hebt geselecteerd, wordt er een zijbalk met drie tabbladen weergegeven: RDP, SSH en Bastion. Omdat Bastion is ingericht voor het virtuele netwerk, is het tabblad Bastion standaard actief. Selecteer **Azure Bastion gebruiken**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Azure Bastion gebruiken selecteren":::

1. Voer op de pagina **Verbinding maken met behulp van Azure Bastion** de gebruikersnaam en het wachtwoord in voor uw virtuele machine en selecteer vervolgens **Verbinding maken**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Verbinding maken":::

1. De RDP-verbinding met deze virtuele machine via Bastion wordt rechtstreeks geopend in de Azure Portal (via HTML5) met behulp van poort 443 en de Bastion-service.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Verbinding maken met behulp van poort 443":::

---
title: 'Kopiëren en plakken van en naar een virtuele machine: Azure Bastion'
description: In dit artikel leest u hoe kopiëren en plakken naar en van een Azure-VM met behulp van Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cherylmc
ms.openlocfilehash: 50f7906992aa19daa205a30f71ce21456bafe558
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92079153"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Kopiëren en plakken naar een virtuele machine: Azure Bastion

Dit artikel helpt u bij het kopiëren en plakken van tekst naar en van virtuele machines wanneer u Azure Bastion gebruikt. Voordat u met een virtuele machine gaat werken, moet u ervoor zorgen dat u de stappen hebt gevolgd om [een bastion-host te maken](./tutorial-create-host-portal.md). Maak vervolgens verbinding met de virtuele machine die u wilt gebruiken met [RDP](bastion-connect-vm-rdp.md) of [SSH](bastion-connect-vm-ssh.md).

Voor browsers die ondersteuning bieden voor de geavanceerde klembord-API-toegang, kunt u tekst kopiëren en plakken tussen uw lokale apparaat en de externe sessie op dezelfde manier als u kopieert en plakt tussen toepassingen op het lokale apparaat. Voor andere browsers kunt u het palet toegang tot het Bastion klem bord gebruiken.

>[!NOTE]
>Alleen tekst kopiëren/plakken wordt momenteel ondersteund.
>

   ![Klem bord toestaan](./media/bastion-vm-manage/allow.png)

Alleen tekst kopiëren/plakken wordt ondersteund. Voor direct kopiëren en plakken kan uw browser vragen om toegang tot het klem bord wanneer de Bastion-sessie wordt geïnitialiseerd. Hiermee **staat u toe dat** de webpagina toegang heeft tot het klem bord.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Kopiëren naar een externe sessie

Nadat u verbinding hebt gemaakt met de virtuele machine met behulp van de [Azure Portal ](https://portal.azure.com), voert u de volgende stappen uit:

1. Kopieer de tekst/inhoud van het lokale apparaat naar het lokale klem bord.
1. Start tijdens de externe sessie het palet toegang tot het Bastion klem bord door de twee pijlen te selecteren. De pijlen bevinden zich aan de linkerkant van de sessie.

   ![Scherm opname van de start pijlen voor het palet met hulp middelen dat aan de linkerkant van het venster is gemarkeerd.](./media/bastion-vm-manage/left.png)

   ![Scherm afbeelding toont een klem bord voor tekst die is gekopieerd in Bastion.](./media/bastion-vm-manage/clipboard.png)
1. Normaal gesp roken wordt de gekopieerde tekst automatisch weer gegeven in het Bastion kopie plakken. Als uw tekst niet aanwezig is, plakt u de tekst in het tekst gebied in het palet.
1. Als de tekst zich in het tekst gebied bevindt, kunt u deze in de externe sessie plakken.

   ![Scherm opname van de knop kopiëren/plakken gemarkeerd en een voorbeeld teken reeks wordt gekopieerd naar de externe sessie.](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Kopiëren uit een externe sessie

Nadat u verbinding hebt gemaakt met de virtuele machine met behulp van de [Azure Portal ](https://portal.azure.com), voert u de volgende stappen uit:

1. Kopieer de tekst/inhoud van de externe sessie naar het klem bord op afstand (met behulp van CTRL-C).

   ![palet van het hulp programma](./media/bastion-vm-manage/remote.png)
1. Start tijdens de externe sessie het palet toegang tot het Bastion klem bord door de twee pijlen te selecteren. De pijlen bevinden zich aan de linkerkant van de sessie.

   ![toetsenbord](./media/bastion-vm-manage/clipboard2.png)
1. Normaal gesp roken wordt de gekopieerde tekst automatisch weer gegeven in het Bastion kopie plakken. Als uw tekst niet aanwezig is, plakt u de tekst in het tekst gebied in het palet.
1. Als de tekst zich in het tekst gebied bevindt, kunt u deze op het lokale apparaat plakken.

   ![plakken](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).

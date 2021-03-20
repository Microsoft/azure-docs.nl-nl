---
title: Verbinding maken met een Azure Lab Services VM vanuit Chromebook | Microsoft Docs
description: Meer informatie over hoe u verbinding maakt tussen een Chromebook en een virtuele machine in Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 74135c0b36f533ebfbba6422bc79af47825a1a3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97813221"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Verbinding maken met een virtuele machine met behulp van Remote Desktop Protocol op een Chromebook

In deze sectie wordt uitgelegd hoe een student met behulp van RDP verbinding kan maken met een leslokaal-VM op basis van een Chromebook.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Microsoft Extern bureaublad installeren op een Chromebook

1. Open de App Store op uw Chromebook en zoek naar **Microsoft extern bureaublad**.

    ![Microsoft Extern bureaublad](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. Installeer de meest recente versie van Microsoft Extern bureaublad. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Toegang tot de virtuele machine vanuit uw Chromebook met RDP

1. Open het **RDP** -bestand dat op uw computer is gedownload met **Microsoft extern bureaublad** geïnstalleerd. Het moet beginnen met het maken van verbinding met de virtuele machine. 

    ![Verbinding maken met de virtuele machine](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Voer uw wacht woord in als u hierom wordt gevraagd.

    ![Scherm opname van het aanmeldings scherm waarin u uw gebruikers naam en wacht woord opgeeft.](./media/how-to-use-classroom-lab/password-chromebook.png)

1. Selecteer **door gaan** als u de volgende waarschuwing ontvangt. 

    ![Certificaat waarschuwing](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. U ziet nu het bureau blad van de virtuele machine waarmee u verbinding maakt.

## <a name="next-steps"></a>Volgende stappen

Zie [verbinding maken met virtuele Linux-machines](how-to-use-remote-desktop-linux-student.md) voor meer informatie over het maken van verbinding met Linux-vm's.


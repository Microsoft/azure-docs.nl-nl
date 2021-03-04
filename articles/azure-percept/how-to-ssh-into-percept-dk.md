---
title: Verbinding maken met uw Azure percept DK via SSH
description: Meer informatie over SSH in uw Azure percept DK met PuTTy
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8dda18271de9b7d65246f0882ee7a68191031c05
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102096612"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Verbinding maken met uw Azure percept DK via SSH

Volg de onderstaande stappen om een SSH-verbinding met uw Azure percept DK in te stellen met behulp van [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Vereisten

- Een op Windows, Linux of OS X gebaseerde hostcomputer met Wi-Fi mogelijkheid
- Een SSH-client
    - Als op uw hostcomputer Windows wordt uitgevoerd, is [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) een efficiënte SSH-client en wordt deze in deze hand leiding gebruikt.
    - Als uw hostcomputer Linux of OS X uitvoert, worden SSH-services opgenomen in deze besturings systemen en kunnen ze zonder een afzonderlijke client toepassing worden uitgevoerd. Raadpleeg de product documentatie van uw besturings systeem voor meer informatie over het uitvoeren van SSH-Services.
- Azure percept DK
- Een SSH-aanmeldings account instellen tijdens de [on-boarding-ervaring van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="initiate-the-ssh-connection"></a>De SSH-verbinding initiëren

1. Schakel uw Azure percept DK (dev kit) in

1. Als uw dev kit al is verbonden met een netwerk via Ethernet of Wi-Fi, gaat u verder met de volgende stap. Als dat niet het geval is, kunt u uw hostcomputer rechtstreeks aansluiten op het Wi-Fi toegangs punt van de dev kit, net zoals verbinding maken met een ander Wi-Fi netwerk:
    - **netwerk naam**: SCZ-xxxx (waarbij ' xxxx ' de laatste vier cijfers van het Mac-netwerk adres van de dev kit is)
    - **wacht woord**: is te vinden op de welkomst kaart die is geleverd bij de dev kit

    > [!WARNING]
    > Tijdens de verbinding met het Azure percept DK Wi-Fi toegangs punt wordt de verbinding van de hostcomputer met Internet tijdelijk verbroken. Actieve video vergaderingen, webstreaming of andere op het netwerk gebaseerde ervaringen worden onderbroken totdat stap 3 van de on-boarding-ervaring van Azure percept DK is voltooid.

1. Open PuTTY. Voer het volgende in en klik op **Open** ssh in uw Devkit:

    1. Hostnaam: 10.1.1.1
    1. Poort: 22
    1. Verbindings type: SSH

    > [!NOTE]
    > De **hostnaam** is het IP-adres van uw apparaat. Als uw dev kit is verbonden met het Wi-Fi toegangs punt van de dev kit, is het IP-adres 10.1.1.1. Als uw dev kit is verbonden via Ethernet, gebruikt u het lokale IP-adres van het apparaat, dat u kunt verkrijgen van de Ethernet-router of-hub. Als uw apparaat is verbonden via Wi-Fi, moet u het IP-adres gebruiken dat is verzameld tijdens de [on-boarding-ervaring van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Bitmapafbeelding.":::

1. Meld u aan bij de PuTTy-Terminal met de SSH-gebruikers naam en het wacht woord die tijdens de on-boarding-ervaring zijn gemaakt.

## <a name="next-steps"></a>Volgende stappen

Nadat u via SSH verbinding hebt gemaakt met uw Azure percept DK, kunt u verschillende taken uitvoeren, waaronder het oplossen van problemen, USB-updates en het uitvoeren van het DiagTool-of SoftAP-hulp programma.
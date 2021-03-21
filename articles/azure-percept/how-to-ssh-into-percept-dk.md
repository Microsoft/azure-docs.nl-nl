---
title: Verbinding maken met uw Azure percept DK via SSH
description: Meer informatie over SSH in uw Azure percept DK met PuTTy
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721474"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Verbinding maken met uw Azure percept DK via SSH

Volg de onderstaande stappen voor het instellen van een SSH-verbinding met uw Azure percept DK via OpenSSH of [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Vereisten

- Een op Windows, Linux of OS X gebaseerde hostcomputer met Wi-Fi mogelijkheid
- Een SSH-client (Zie de volgende sectie voor installatie richtlijnen)
- Een Azure percept DK (dev kit)
- Een SSH-aanmelding, gemaakt tijdens de [installatie-ervaring van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="install-your-preferred-ssh-client"></a>Uw voorkeurs-SSH-client installeren

Als uw hostcomputer Linux of OS X uitvoert, worden SSH-services opgenomen in deze besturings systemen en kunnen ze zonder een afzonderlijke client toepassing worden uitgevoerd. Raadpleeg de product documentatie van uw besturings systeem voor meer informatie over het uitvoeren van SSH-Services.

Als op uw hostcomputer Windows wordt uitgevoerd, kunt u kiezen uit twee SSH-client opties: OpenSSH en PuTTy.

### <a name="openssh"></a>OpenSSH

Windows 10 bevat een ingebouwde SSH-client met de naam OpenSSH die kan worden uitgevoerd met een eenvoudige opdracht in een opdracht prompt. We raden u aan OpenSSH te gebruiken met Azure percept als deze voor u beschikbaar is. Voer de volgende stappen uit om te controleren of op de Windows-computer OpenSSH is geïnstalleerd:

1. Ga naar   ->  **instellingen** starten.

1. Selecteer **Apps**.

1. Selecteer onder **Apps & onderdelen** de optie **optionele functies**.

1. Typ **openssh-client** in de zoek balk **geïnstalleerde functies** . Als OpenSSH wordt weer gegeven, is de client al geïnstalleerd en kunt u door gaan naar de volgende sectie. Als u OpenSSH niet ziet, klikt u op **een functie toevoegen**.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Scherm opname van instellingen met de OpenSSH-installatie status.":::

1. Selecteer **openssh-client** en klik op **installeren**. U kunt nu door gaan naar de volgende sectie. Als OpenSSH niet beschikbaar is voor installatie op uw computer, volgt u de onderstaande stappen om PuTTy, een SSH-client van derden, te installeren.

### <a name="putty"></a>PuTTY

Als uw Windows-computer geen OpenSSH bevat, kunt u het beste [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)gebruiken. Als u PuTTy wilt downloaden en installeren, voert u de volgende stappen uit:

1. Ga naar de [pagina voor het downloaden van putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. Klik onder **pakket bestanden** op het bestand 32-bits of 64-bits. msi om het installatie programma te downloaden. Bekijk de [Veelgestelde vragen](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit)als u niet zeker weet welke versie u moet kiezen.

1. Klik op het installatie programma om het installatie proces te starten. Volg de aanwijzingen om dit te doen.

1. Gefeliciteerd De PuTTy SSH-client is geïnstalleerd.

## <a name="initiate-the-ssh-connection"></a>De SSH-verbinding initiëren

1. Schakel uw Azure percept DK uit.

1. Als uw dev kit al is verbonden met een netwerk via Ethernet of Wi-Fi, gaat u verder met de volgende stap. Als dat niet het geval is, sluit u de hostcomputer rechtstreeks aan op het Wi-Fi toegangs punt van de dev kit. Net als bij het maken van verbinding met een ander Wi-Fi netwerk opent u het netwerk en Internet instellingen op uw computer, klikt u op het volgende netwerk en voert u het netwerk wachtwoord in wanneer u hierom wordt gevraagd:

    - **Netwerk naam**: afhankelijk van de versie van het besturings systeem van de dev kit is de naam van het Wi-Fi toegangs punt **SCZ-xxxx** of **apd-xxxx** (waarbij "xxxx" de laatste vier cijfers van het MAC-adres van de dev kit is)
    - **Wacht woord**: is te vinden op de welkomst kaart die is geleverd bij de dev kit

    > [!WARNING]
    > Tijdens de verbinding met het Azure percept DK Wi-Fi toegangs punt wordt de verbinding van de hostcomputer met Internet tijdelijk verbroken. Actieve video vergaderingen, webstreaming of andere op het netwerk gebaseerde ervaringen worden onderbroken.

1. Voltooi het SSH-verbindings proces op basis van uw SSH-client.

### <a name="using-openssh"></a>OpenSSH gebruiken

1. Open een opdracht prompt (**Start**  ->  **opdracht prompt**).

1. Voer het volgende in bij de opdracht prompt:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Als uw computer is verbonden met het Wi-Fi toegangs punt van de dev kit, is het IP-adres 10.1.1.1. Als uw dev kit is verbonden via Ethernet, gebruikt u het lokale IP-adres van het apparaat, dat u kunt verkrijgen van de Ethernet-router of-hub. Als uw dev kit is verbonden via Wi-Fi, moet u het IP-adres dat is toegewezen aan uw dev kit gebruiken tijdens de [installatie van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Als uw dev kit is verbonden met een Wi-Fi netwerk, maar u niet weet wat het IP-adres is, gaat u naar Azure percept Studio en opent u de [video stroom van uw apparaat](./how-to-view-video-stream.md). In de adres balk van het tabblad Video Stream browser wordt het IP-adres van uw apparaat weer gegeven.

1. Voer uw SSH-wacht woord in wanneer u hierom wordt gevraagd.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Scherm opname van open SSH-opdracht prompt aanmelden.":::

1. Als dit de eerste keer is dat u via OpenSSH verbinding maakt met uw dev kit, wordt u mogelijk ook gevraagd om de sleutel van de host te accepteren. Voer **Ja** in om de sleutel te accepteren.

1. Gefeliciteerd U hebt verbinding gemaakt met uw dev kit via SSH.

### <a name="using-putty"></a>PuTTy gebruiken

1. Open PuTTY. Voer het volgende in het venster **putty-configuratie** in en klik op **openen** in SSH in uw dev kit:

    1. Hostnaam: [IP-adres]
    1. Poort: 22
    1. Verbindings type: SSH

    De **hostnaam** is het IP-adres van uw dev kit. Als uw computer is verbonden met het Wi-Fi toegangs punt van de dev kit, is het IP-adres 10.1.1.1. Als uw dev kit is verbonden via Ethernet, gebruikt u het lokale IP-adres van het apparaat, dat u kunt verkrijgen van de Ethernet-router of-hub. Als uw dev kit is verbonden via Wi-Fi, moet u het IP-adres dat is toegewezen aan uw dev kit gebruiken tijdens de [installatie van Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Als uw dev kit is verbonden met een Wi-Fi netwerk, maar u niet weet wat het IP-adres is, gaat u naar Azure percept Studio en opent u de [video stroom van uw apparaat](./how-to-view-video-stream.md). In de adres balk van het tabblad Video Stream browser wordt het IP-adres van uw apparaat weer gegeven.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Scherm opname van het venster PuTTy-configuratie.":::

1. Er wordt een PuTTy-terminal geopend. Wanneer u hierom wordt gevraagd, voert u uw SSH-gebruikers naam en wacht woord in de terminal in.

1. Gefeliciteerd U hebt verbinding gemaakt met uw dev kit via SSH.

## <a name="next-steps"></a>Volgende stappen

Nadat u via SSH verbinding hebt gemaakt met uw Azure percept DK, kunt u verschillende taken uitvoeren, waaronder problemen met het [oplossen van apparaten](./troubleshoot-dev-kit.md) en [USB-updates](./how-to-update-via-usb.md).
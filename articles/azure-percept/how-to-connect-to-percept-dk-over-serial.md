---
title: Verbinding maken met uw Azure percept DK via serieel
description: Meer informatie over het instellen van een seriële verbinding met uw Azure percept DK met PuTTy en een USB-naar-TTL seriële kabel
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662325"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Verbinding maken met uw Azure percept DK via serieel

Volg de onderstaande stappen om een seriële verbinding met uw Azure percept DK in te stellen met behulp van [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

> [!WARNING]
> Probeer uw Devkit **niet** via serieel te verbinden, behalve bij uitzonderlijke fout situaties (bijvoorbeeld dat u uw apparaat hebt losgekoppeld). Het nemen van de draag bare kaart behuizing om verbinding te maken met de seriële kabel is zeer lastig en verbreekt uw Wi-Fi antenne kabels.

## <a name="prerequisites"></a>Vereisten

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Host-PC
- Azure percept DK
- [USB naar TTL seriële kabel](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB-naar-TTL seriële kabel.":::

## <a name="initiate-the-serial-connection"></a>De seriële verbinding initiëren

1. Als uw draaggolf kaart is verbonden met een 80/20-Rail, verwijdert u deze uit de rail met behulp van de hex-toets (opgenomen in de welkomst kaart van Devkit).

1. Verwijder de schroeven aan de onderkant van de draag bare kaart behuizing en pak het moeder bord uit.

    > [!WARNING]
    > Als u het moeder bord verwijdert, worden uw Wi-Fi antenne kabels verbroken. Ga **niet** verder met de seriële verbinding tenzij het de laatste redmiddel is om uw apparaat te herstellen.

1. Verwijder de heatsink.

1. Verwijder het jumper bord van de GPIO-pincodes.

    > [!TIP]
    > Let op de richting van de jumper kaart voordat u deze verwijdert. Teken bijvoorbeeld een pijl op of koppel een sticker aan het jumper bord om naar het circuit voor referentie te verwijzen. Het jumper bord wordt niet versleuteld en kan per ongeluk worden aangesloten tijdens het opnieuw samen stellen van uw vervoerders kaart.

1. Verbind de [USB-seriële kabel](https://www.adafruit.com/product/954) met de aan de GPIO-pincodes op het moeder bord, zoals hieronder wordt weer gegeven. Houd er rekening mee dat de rode kabel niet is verbonden.

    - Verbind de zwarte kabel (GND) met pin 6.
    - Verbind de witte kabel (RX) met de pincode 8.
    - Verbind de groene kabel (TX) met pin 10.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Seriële PIN-verbindingen voor draag signaal kaarten.":::

1. Schakel uw Devkit uit en sluit de USB-zijde van de seriële kabel aan op uw PC.

1. Ga in Windows naar **Start**  ->  **Windows Update instellingen**  ->  **optionele updates**  ->  **stuur programma-updates** weer geven. Zoek naar een serieel naar USB-update in de lijst, schakel het selectie vakje ernaast in en klik op **downloaden en installeren**.  

1. Open vervolgens de Windows-Apparaatbeheer (**Start**  ->  **Apparaatbeheer**). Ga naar **poorten** en klik op **USB naar UART** om **Eigenschappen** te openen. Houd er rekening mee dat de COM-poort waarmee uw apparaat is verbonden.

1. Klik op het tabblad **poort instellingen** . Zorg ervoor dat **bits per seconde** is ingesteld op 115200.

1. Open PuTTY. Voer het volgende in en klik op **openen** om verbinding te maken met uw Devkit via serie nummer:

    1. Seriële lijn: COM [poort #]
    1. Snelheid: 115200
    1. Verbindings type: serieel

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="PuTTy-sessie venster waarvoor seriële para meters zijn geselecteerd.":::

## <a name="next-steps"></a>Volgende stappen

Als u een apparaat dat niet kan worden opgestart, wilt bijwerken met de [seriële kabel van USB naar TTL](https://www.adafruit.com/product/954), raadpleegt u de USB-Update gids voor niet-standaard situaties.

[comment]: # (Voeg indien beschikbaar een koppeling naar de USB-Update gids toe.)
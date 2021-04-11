---
title: Verbinding maken met een virtueel bureau blad van Windows met de webclient-Azure
description: Verbinding maken met het virtuele bureau blad van Windows via de webclient.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: b17ec374f2e8b6e268d993fc9a7b14ebeebd40df
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447892"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Verbinding maken met een virtueel bureau blad van Windows met de webclient

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/connect-web-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Met de webclient kunt u vanuit een webbrowser toegang krijgen tot uw virtuele bureau blad-resources zonder het langdurige installatie proces.

>[!NOTE]
>De webclient heeft momenteel geen ondersteuning voor mobiele besturings systemen.

## <a name="supported-operating-systems-and-browsers"></a>Ondersteunde besturingssystemen en browsers

Hoewel een voor HTML5 geschikte browser zou moeten werken, ondersteunen we de volgende besturings systemen en browsers officieel.

| Browser           | Ondersteund besturingssysteem                     | Notities               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Versie 11 of hoger |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versie 55 of hoger |
| Google Chrome     | Windows-, macOS-, Linux-en Chrome-besturings systeem |                     |

## <a name="access-remote-resources-feed"></a>Toegang tot externe resources-feed

Ga in een browser naar de Azure Resource Manager-geïntegreerde versie van de Windows Virtual Desktop-WebClient op <https://rdweb.wvd.microsoft.com/arm/webclient> en meld u aan met uw gebruikers account.

>[!NOTE]
>Als u Windows virtueel bureau blad (klassiek) gebruikt zonder Azure Resource Manager integratie, kunt u in plaats daarvan verbinding maken met uw resources <https://rdweb.wvd.microsoft.com/webclient> .
>
> Als u de US Gov Portal gebruikt, gebruikt u <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Als u zich al hebt aangemeld met een ander Azure Active Directory account dan dat u wilt gebruiken voor virtueel bureau blad van Windows, moet u zich afmelden of een persoonlijk browser venster gebruiken.

Nadat u zich hebt aangemeld, ziet u nu een lijst met resources. U kunt resources starten door ze te selecteren zoals u een normale app op het tabblad **alle resources** zou doen.

## <a name="using-an-input-method-editor"></a>Een Input Method Editor gebruiken

De webclient ondersteunt het gebruik van een Input Method Editor (IME) in de externe sessie in versie **1.0.21.16 of hoger**. Het taal pakket voor het toetsen bord dat u wilt gebruiken in de externe sessie moet zijn geïnstalleerd op de virtuele machine van de host. Voor meer informatie over het instellen van taal pakketten in de externe sessie raadpleegt u [taal pakketten toevoegen aan een Windows 10-installatie kopie voor meerdere sessies](language-packs.md).

IME-invoer inschakelen met de webclient:

1. Voordat u verbinding maakt met de externe sessie, gaat u naar het deel venster **instellingen** voor webclient.

2. Schakel de instelling **invoer methode-editor inschakelen** in **op aan**.

3. Selecteer in de vervolg keuzelijst het toetsen bord dat u in de externe sessie wilt gebruiken.

4. Maak verbinding met de externe sessie.

De webclient onderdrukt het lokale IME-venster als u zich richt op de externe sessie. Als u IME-instellingen wijzigt nadat u al verbinding hebt gemaakt met de externe sessie, heeft dit geen effect.

>[!NOTE]
>Als het taal pakket niet is geïnstalleerd op de virtuele machine van de host, wordt de externe sessie standaard ingesteld op het Engels (Verenigde Staten)-toetsen bord.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het gebruik van de webclient naar aan [de slag met de webclient](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).

---
title: Verbinding maken Windows Virtual Desktop met de webclient - Azure
description: Verbinding maken met Windows Virtual Desktop met behulp van de webclient.
author: Heidilohr
ms.topic: how-to
ms.date: 09/24/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 80a090abee45f9cb3ec6ee5406aad6abf1d73a59
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725004"
---
# <a name="connect-to-windows-virtual-desktop-with-the-web-client"></a>Verbinding maken Windows Virtual Desktop met de webclient

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/connect-web-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Met de webclient hebt u toegang tot Windows Virtual Desktop resources vanuit een webbrowser zonder het langdurige installatieproces.

>[!NOTE]
>De webclient biedt momenteel geen ondersteuning voor mobiele besturingssystemen.

## <a name="supported-operating-systems-and-browsers"></a>Ondersteunde besturingssystemen en browsers

Hoewel elke voor HTML5 geschikte browser zou moeten werken, ondersteunen we officieel de volgende besturingssystemen en browsers.

| Browser           | Ondersteund besturingssysteem                     | Notities               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | Versie 11 of hoger |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versie 55 of hoger |
| Google Chrome     | Windows, macOS, Linux, Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>Toegang tot externe resourcesfeed

Navigeer in een browser naar de Azure Resource Manager geïntegreerde versie van de Windows Virtual Desktop-webclient op en meld u <https://rdweb.wvd.microsoft.com/arm/webclient> aan met uw gebruikersaccount.

>[!NOTE]
>Als u een Windows Virtual Desktop (klassiek) zonder integratie Azure Resource Manager, maakt u in plaats daarvan verbinding met uw resources <https://rdweb.wvd.microsoft.com/webclient> op.
>
> Als u de US Gov portal gebruikt, gebruikt u <https://rdweb.wvd.azure.us/arm/webclient/index.html> .

>[!NOTE]
>Als u zich al hebt aangemeld met een ander Azure Active Directory-account dan het account dat u wilt gebruiken voor Windows Virtual Desktop, moet u zich aanmelden of een privébrowservenster gebruiken.

Nadat u zich hebt aanmeldt, ziet u nu een lijst met resources. U kunt resources starten door ze te selecteren zoals een normale app op **het tabblad Alle resources.**

## <a name="next-steps"></a>Volgende stappen

Zie Aan de slag met de webclient voor meer informatie over het gebruik [van de webclient.](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)

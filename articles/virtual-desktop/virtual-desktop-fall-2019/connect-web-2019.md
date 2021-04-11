---
title: Verbinding maken met het virtuele bureau blad van Windows (klassieke) webclient-Azure
description: Verbinding maken met het virtuele bureau blad van Windows (klassiek) met behulp van de webclient.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 0ea095a8ed902b9636b0cb8026f86eb3a0882460
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445190"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-web-client"></a>Verbinding maken met het virtuele bureau blad van Windows (klassiek) met de webclient

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../connect-web.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Met de webclient kunt u vanuit een webbrowser toegang krijgen tot uw virtuele bureau blad-resources zonder het langdurige installatie proces.

>[!NOTE]
>De webclient heeft momenteel geen ondersteuning voor mobiele besturings systemen.

## <a name="supported-operating-systems-and-browsers"></a>Ondersteunde besturingssystemen en browsers

Hoewel een voor HTML5 geschikte browser zou moeten werken, ondersteunen we de volgende besturings systemen en browsers officieel.

| Browser           | Ondersteund besturingssysteem                     | Notities               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows, macOS, Linux            | Versie 55 of hoger |
| Google Chrome     | Windows-, macOS-, Linux-en Chrome-besturings systeem |                     |

## <a name="access-remote-resources-feed"></a>Toegang tot externe resources-feed

Navigeer in een browser naar de webclient met virtueel bureau blad van Windows op <https://rdweb.wvd.microsoft.com/webclient> en meld u aan met uw gebruikers account.

>[!NOTE]
>Als u een virtueel bureau blad van Windows gebruikt met Azure Resource Manager integratie, kunt u in plaats daarvan verbinding maken met uw resources <https://rdweb.wvd.microsoft.com/arm/webclient> .

>[!NOTE]
>Als u zich al hebt aangemeld met een ander Azure Active Directory account dan dat u wilt gebruiken voor virtueel bureau blad van Windows, moet u zich afmelden of een persoonlijk browser venster gebruiken.

Nadat u zich hebt aangemeld, ziet u nu een lijst met resources. U kunt resources starten door ze te selecteren zoals u een normale app op het tabblad **alle resources** zou doen.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het gebruik van de webclient naar aan [de slag met de webclient](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client).

---
title: Verbinding maken met het virtuele bureau blad van Windows vanuit iOS-Azure
description: Verbinding maken met het virtuele bureau blad van Windows met behulp van de iOS-client.
author: Heidilohr
ms.topic: how-to
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 87bb2cc53ce056552e8f44aac4ade96e603a8787
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230038"
---
# <a name="connect-to-windows-virtual-desktop-with-the-ios-client"></a>Verbinding maken met het virtuele bureau blad van Windows met de iOS-client

> Van toepassing op: iOS 13,0 of hoger. Compatibel met iPhone, iPad en iPod Touch.

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/connect-ios-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

U kunt toegang krijgen tot virtuele bureau blad-resources van Windows vanaf uw iOS-apparaat met onze Download bare client. In deze hand leiding wordt uitgelegd hoe u de iOS-client kunt instellen.

## <a name="install-the-ios-client"></a>De iOS-client installeren

[Down load](https://aka.ms/rdios) en installeer de-client op uw IOS-apparaat om aan de slag te gaan.

## <a name="subscribe-to-a-feed"></a>Abonneren op een feed

Abonneer u op de feed van uw beheerder om de lijst met beheerde resources te verkrijgen die u op uw iOS-apparaat kunt gebruiken.

Abonneren op een feed:

1. Tik in het verbindings centrum **+** en tik vervolgens op **werk ruimte toevoegen**.
2. Voer de URL van de feed in het veld URL van de **feed** in. De feed-URL kan een URL of een e-mail adres zijn.
   - Als u een URL gebruikt, gebruikt u de beheerder die u hebt ontvangen. Normaal gesp roken is de URL <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - Als u e-mail wilt gebruiken, voert u uw e-mail adres in. Dit geeft de client de opdracht om te zoeken naar een URL die is gekoppeld aan uw e-mail adres als uw beheerder de server op die manier heeft geconfigureerd.
   - Als u verbinding wilt maken via de portal van US Gov, gebruikt u <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Tik op **Volgende**.
4. Voer uw referenties in wanneer hierom wordt gevraagd.
   - Geef voor de **gebruikers naam** de gebruikers naam op met machtigingen voor toegang tot resources.
   - Geef bij **wacht woord** het wacht woord op dat is gekoppeld aan de gebruikers naam.
   - U wordt mogelijk ook gevraagd extra factoren op te geven als uw beheerder de verificatie op die manier heeft geconfigureerd.
5. Tik op **Opslaan**.

Daarna moeten de externe bronnen worden weer gegeven in het verbindings centrum.

Zodra u bent geabonneerd op een feed, wordt de inhoud van de feed regel matig automatisch bijgewerkt. Resources kunnen worden toegevoegd, gewijzigd of verwijderd op basis van wijzigingen die zijn aangebracht door de beheerder.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de documentatie aan de [slag met de IOS-client](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) voor meer informatie over het gebruik van de IOS-client.

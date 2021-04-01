---
title: Verbinding maken met het virtuele bureau blad van Windows (klassiek) van iOS-Azure
description: Verbinding maken met het virtuele bureau blad van Windows (klassiek) met behulp van de iOS-client.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7efedadbba196223db2ad5700035e27d37fb414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008454"
---
# <a name="connect-to-windows-virtual-desktop-classic-with-the-ios-client"></a>Verbinding maken met het virtuele bureau blad van Windows (klassiek) met de iOS-client

> Van toepassing op: iOS 13,0 of hoger. Compatibel met iPhone, iPad en iPod Touch.

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../connect-ios.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

U kunt toegang krijgen tot virtuele bureau blad-resources van Windows vanaf uw iOS-apparaat met onze Download bare client. In deze hand leiding wordt uitgelegd hoe u de iOS-client kunt instellen.

## <a name="install-the-ios-client"></a>De iOS-client installeren

[Down load](https://aka.ms/rdios) en installeer de-client op uw IOS-apparaat om aan de slag te gaan.

## <a name="subscribe-to-a-feed"></a>Abonneren op een feed

Abonneer u op de feed van uw beheerder om de lijst met beheerde resources te verkrijgen die u op uw iOS-apparaat kunt gebruiken.

Abonneren op een feed:

1. Tik in het verbindings centrum **+** en tik vervolgens op **werk ruimte toevoegen**.
2. Voer de URL van de feed in het veld URL van de **feed** in. De feed-URL kan een URL of een e-mail adres zijn.
   - Als u een URL gebruikt, gebruikt u de beheerder die u hebt ontvangen. Normaal gesp roken is de URL <https://rdweb.wvd.microsoft.com> .
   - Als u e-mail wilt gebruiken, voert u uw e-mail adres in. Dit geeft de client de opdracht om te zoeken naar een URL die is gekoppeld aan uw e-mail adres als uw beheerder de server op die manier heeft geconfigureerd.
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

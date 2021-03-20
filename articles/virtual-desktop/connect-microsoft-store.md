---
title: Verbinding maken met Microsoft Store-client-Azure
description: Verbinding maken met het virtuele bureau blad van Windows met behulp van de Microsoft Store-client.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bab2a11ff9e7907621087e5027929e3e42eaf29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91744795"
---
# <a name="connect-with-the-microsoft-store-client"></a>Verbinding maken met de Microsoft Store-client

>Van toepassing op: Windows 10.

U kunt toegang krijgen tot virtuele bureau blad-resources op apparaten met Windows 10.

## <a name="install-the-microsoft-store-client"></a>De Microsoft Store-client installeren

U kunt de client installeren voor de huidige gebruiker, waarvoor geen beheerders rechten zijn vereist. De beheerder kan de client ook installeren en configureren, zodat alle gebruikers op het apparaat er toegang toe hebben.

Nadat de client is geïnstalleerd, kan deze vanuit het menu Start worden gestart door te zoeken naar Extern bureaublad.

Om aan de slag te gaan, moet u [de-client downloaden en installeren via de Microsoft Store](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Abonneren op een werk ruimte

Abonneer u op de werk ruimte van uw beheerder om de lijst met beheerde resources te verkrijgen die u op uw PC kunt openen.

Abonneren op een werk ruimte:

1. Tik in het scherm van het verbindings centrum op **+ toevoegen** en tik vervolgens op **werk ruimten**.
2. Geef de werk ruimte-URL op in het URL-veld voor de werk ruimte dat door de beheerder wordt opgegeven. De URL van de werk ruimte kan een URL of een e-mail adres zijn.
   
   - Als u een werk ruimte-URL gebruikt, gebruikt u de URL die uw beheerder heeft gekregen.
   - Als u verbinding maakt vanaf een virtueel bureau blad van Windows, gebruikt u een van de volgende Url's, afhankelijk van de versie van de service die u gebruikt:
       - Virtueel bureau blad van Windows (klassiek): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Virtueel bureau blad van Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. Tik op **Abonneren**.
4. Voer uw referenties in wanneer hierom wordt gevraagd.
5. Nadat u bent geabonneerd, moeten de werk ruimten worden weer gegeven in het verbindings centrum.

Werk ruimten kunnen worden toegevoegd, gewijzigd of verwijderd op basis van wijzigingen die zijn aangebracht door de beheerder.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg aan [de slag met de Microsoft Store-client](/windows-server/remote/remote-desktop-services/clients/windows/)voor meer informatie over het gebruik van de Microsoft Store-client.
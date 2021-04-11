---
title: Verbinding maken met Windows Virtual Desktop Windows 10 of 7-Azure
description: Verbinding maken met het virtuele bureau blad van Windows met behulp van de Windows-desktop-client.
author: Heidilohr
ms.topic: how-to
ms.date: 09/22/2020
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 625662a6b67e7d30e6320fe7831e4fa7793b9c30
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447876"
---
# <a name="connect-with-the-windows-desktop-client"></a>Verbinding maken met de Windows Desktop-client

U hebt toegang tot de virtuele bureau blad-bronnen van Windows op apparaten met Windows 10, Windows 10 IoT Enter prise en Windows 7 met behulp van de Windows-bureaubladclient. 

> [!IMPORTANT]
> Dit biedt geen ondersteuning voor Window 8 of Windows 8,1.
> 
> Dit ondersteunt alleen Azure Resource Manager objecten, om objecten zonder Azure Resource Manager te ondersteunen. Zie [verbinding maken met Windows Desktop (klassieke) client](./virtual-desktop-fall-2019/connect-windows-7-10-2019.md).
> 
> Dit biedt geen ondersteuning voor de client RemoteApp-en bureaublad verbindingen (RADC) of de Verbinding met extern bureaublad-client (MSTSC).

## <a name="install-the-windows-desktop-client"></a>De Windows-Desktop-Client installeren

Down load de client op basis van uw Windows-versie:

- [Windows 64-bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32-bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows-ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Selecteer tijdens de installatie een van de volgende opties:

- **Alleen voor u installeren**
- **Installeren voor alle gebruikers van deze computer** (vereist beheerders rechten)

Als u de client na de installatie wilt starten, gebruikt u het menu **Start** en zoekt u naar **extern bureaublad**.

## <a name="subscribe-to-a-workspace"></a>Abonneren op een werk ruimte

Als u zich wilt abonneren op een werk ruimte, kiest u een van de volgende opties:

- Een werk-of school account gebruiken en de client de beschik bare bronnen voor u laten detecteren
- De specifieke URL van de resource gebruiken

Als u de resource wilt starten nadat u bent geabonneerd, gaat u naar het **verbindings centrum** en dubbelklikt u op de resource.

> [!TIP]
> Als u een resource wilt starten vanuit het menu **Start** , kunt u de map met de naam van de werk ruimte vinden of de resource naam invoeren in de zoek balk.

### <a name="use-a-user-account"></a>Een gebruikers account gebruiken

1. Selecteer **Abonneren** op de hoofd pagina.
1. Meld u aan met uw gebruikers account wanneer u hierom wordt gevraagd.

De resources gegroepeerd op werk ruimte worden weer gegeven in het **verbindings centrum**.

   > [!NOTE]
   > De Windows-client wordt automatisch standaard ingesteld op virtueel bureau blad van Windows (klassiek). 
   > 
   > Als de client echter aanvullende Azure Resource Manager resources detecteert, worden deze automatisch toegevoegd of wordt de gebruiker gewaarschuwd dat ze beschikbaar zijn.

### <a name="use-a-specific-url"></a>Een specifieke URL gebruiken

1. Selecteer **abonneren met URL** op de hoofd pagina.
1. Geef de URL van de *werk ruimte* of een *e-mail adres* op:
   - Gebruik voor de **werk ruimte-URL** de URL die door uw beheerder is verschaft.

   |Beschik bare resources|URL|
   |-|-|
   |Windows Virtual Desktop (klassieke versie)|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Windows Virtual Desktop|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Virtueel bureau blad van Windows (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   
   - Gebruik uw e-mail adres voor **e-mail**. 
      
   De-client vindt de URL die aan uw e-mail adres is gekoppeld, op voor instelling dat uw beheerder [e-mail detectie](/windows-server/remote/remote-desktop-services/rds-email-discovery)heeft ingeschakeld.

1. Selecteer **Next**.
1. Meld u aan met uw gebruikers account wanneer u hierom wordt gevraagd.

De resources gegroepeerd op werk ruimte worden weer gegeven in het **verbindings centrum**.

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over het gebruik van de-client naar aan [de slag met de Windows desktop-client](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).

Als u een beheerder bent die meer wil weten over de functies van de client, raadpleegt u [Windows desktop client voor beheerders](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin).

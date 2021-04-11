---
title: Windows Virtual Desktop-omgeving-Azure
description: Meer informatie over de basis elementen van een virtueel-bureaublad omgeving van Windows, zoals hostgroepen en app-groepen.
author: Heidilohr
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2c598be641ee09773e4d56ebc485e6a2fbe49191
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446872"
---
# <a name="windows-virtual-desktop-environment"></a>Windows Virtual Desktop-omgeving

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/environment-setup-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Virtueel bureau blad van Windows is een service waarmee gebruikers eenvoudig en veilig toegang krijgen tot hun gevirtualiseerde Bureau bladen en RemoteApps. In dit onderwerp vindt u meer informatie over de algemene structuur van de virtueel-bureaublad omgeving van Windows.

## <a name="host-pools"></a>Hostgroepen

Een hostgroep is een verzameling virtuele Azure-machines die als sessie-hosts worden geregistreerd bij Windows virtueel bureau blad wanneer u de virtueel-bureaublad agent van Windows uitvoert. Alle virtuele machines van de host in een hostgroep moeten vanuit dezelfde installatie kopie worden gebrond voor een consistente gebruikers ervaring.

Een hostgroep kan een van de volgende twee typen zijn:

- Persoonlijk, waarbij elke sessiehost wordt toegewezen aan individuele gebruikers.
- Gepoold, waarbij sessie-hosts verbindingen kunnen accepteren van alle gebruikers die zijn gemachtigd voor een app-groep in de hostgroep.

U kunt extra eigenschappen instellen voor de hostgroep om het gedrag van de taak verdeling te wijzigen, het aantal sessies dat elke sessie-host kan uitvoeren en wat de gebruiker kan doen om in de hostgroep sessies in te stellen, terwijl u zich aanmeldt bij de virtuele Windows-bureaublad sessies. U beheert de resources die worden gepubliceerd voor gebruikers via app-groepen.

## <a name="app-groups"></a>App-groepen

Een app-groep is een logische groepering van toepassingen die zijn geïnstalleerd op sessie-hosts in de hostgroep. Een app-groep kan een van de volgende twee typen zijn:

- RemoteApp, waar gebruikers toegang hebben tot de RemoteApps die u afzonderlijk selecteert en publiceert naar de app-groep
- Bureau blad, waar gebruikers toegang tot het volledige bureau blad hebben

Standaard wordt een bureau blad-app-groep (met de naam bureau blad-toepassings groep) automatisch gemaakt wanneer u een hostgroep maakt. U kunt deze app-groep op elk gewenst moment verwijderen. U kunt echter geen andere bureau blad-app-groep maken in de hostgroep terwijl er een bureau blad-app-groep bestaat. Als u RemoteApps wilt publiceren, moet u een RemoteApp-app-groep maken. U kunt meerdere RemoteApp-app-groepen maken die geschikt zijn voor verschillende werk scenario's. Verschillende RemoteApp-app-groepen kunnen ook overlappende RemoteApps bevatten.

Als u resources naar gebruikers wilt publiceren, moet u deze toewijzen aan app-groepen. Houd rekening met de volgende zaken wanneer u gebruikers aan app-groepen toewijst:

- Een gebruiker kan worden toegewezen aan zowel een bureau blad-app-groep als een RemoteApp-app-groep in dezelfde hostgroep. Gebruikers kunnen echter slechts één type app-groep per sessie starten. Gebruikers kunnen niet beide typen app-groepen tegelijk starten tijdens één sessie.
- Een gebruiker kan worden toegewezen aan meerdere app-groepen binnen dezelfde hostgroep en hun feed wordt een accumulatie van beide app-groepen.

## <a name="workspaces"></a>Workspaces

Een werk ruimte is een logische groepering van toepassings groepen in Windows virtueel bureau blad. Elke Windows-toepassings groep voor virtueel bureau blad moet worden gekoppeld aan een werk ruimte zodat gebruikers de externe apps en Desk tops kunnen zien die ze hebben gepubliceerd.

## <a name="end-users"></a>Eindgebruikers

Nadat u gebruikers aan hun app-groepen hebt toegewezen, kunnen ze verbinding maken met een virtuele Windows-bureau blad-implementatie met een van de virtuele bureau blad-clients van Windows.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over gedelegeerde toegang en het toewijzen van rollen aan gebruikers op het [virtuele bureau blad van Windows](delegated-access-virtual-desktop.md).

Zie [een hostgroep maken met de Azure Portal](create-host-pools-azure-marketplace.md)voor meer informatie over het instellen van uw Windows-hostgroep voor virtueel bureau blad.

Zie een van de volgende artikelen voor meer informatie over het maken van verbinding met Windows Virtual Desktop:

- [Verbinding maken met Windows 10 of Windows 7](connect-windows-7-10.md)
- [Verbinding maken met een webbrowser](connect-web.md)
- [Verbinding maken met de Android-client](connect-android.md)
- [Verbinding maken met de macOS-client](connect-macos.md)
- [Verbinding maken met de iOS-client](connect-ios.md)
---
title: Problemen met Extern bureaublad client Windows virtueel bureau blad oplossen-Azure
description: Problemen oplossen bij het instellen van client verbindingen in een Windows Virtual Desktop-Tenant omgeving.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 097c97d16cf62793d03ac42662267e0553383bc1
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539616"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Problemen met de Extern bureaublad-client oplossen

In dit artikel worden veelvoorkomende problemen met de Extern bureaublad-client beschreven en hoe u deze kunt oplossen.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Extern bureaublad-client voor Windows 7 of Windows 10 reageert niet meer of kan niet worden geopend

Vanaf versie 1.2.790 kunt u de gebruikers gegevens opnieuw instellen op de pagina over of met behulp van een opdracht.

Gebruik de volgende opdracht om uw gebruikers gegevens te verwijderen, de standaard instellingen te herstellen en af te melden bij alle werk ruimten.

```cmd
msrdcw.exe /reset [/f]
```

Als u een eerdere versie van de Extern bureaublad-client gebruikt, raden wij u aan de client te verwijderen en opnieuw te installeren.

## <a name="web-client-wont-open"></a>WebClient kan niet worden geopend

Test eerst uw Internet verbinding door een andere website in uw browser te openen. bijvoorbeeld [www.Bing.com](https://www.bing.com).

**Nslookup** gebruiken om te controleren of DNS de FQDN kan omzetten:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Probeer verbinding te maken met een andere client, bijvoorbeeld Extern bureaublad-client voor Windows 7 of Windows 10, en controleer of u de webclient kunt openen.

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>Kan geen andere websites openen tijdens de verbinding met de webclient

Als u geen andere websites kunt openen terwijl u verbonden bent met de webclient, kunnen er problemen met de netwerk verbinding of een netwerk storing optreden. U wordt aangeraden contact op te nemen met netwerk ondersteuning.

### <a name="nslookup-cant-resolve-the-name"></a>Nslookup kan de naam niet omzetten

Als nslookup de naam niet kan omzetten, zijn er mogelijk problemen met de netwerk verbinding of een netwerk storing. U wordt aangeraden contact op te nemen met netwerk ondersteuning.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>De client kan geen verbinding maken, maar andere clients in uw netwerk kunnen verbinding maken

Als uw browser wordt gestart of niet meer werkt terwijl u de webclient gebruikt, volgt u deze instructies om het probleem op te lossen:

1. Start de browser opnieuw.
2. Wis browser cookies. Zie [cookie bestanden verwijderen in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Wis de browsercache. Zie [browser cache wissen voor uw browser](https://binged.it/2RKyfdU).
4. Open de browser in de persoonlijke modus.

## <a name="client-doesnt-show-my-resources"></a>De client geeft geen resources weer

Controleer eerst het Azure Active Directory account dat u gebruikt. Als u zich al hebt aangemeld met een ander Azure Active Directory account dan dat u wilt gebruiken voor virtueel bureau blad van Windows, moet u zich afmelden of een persoonlijk browser venster gebruiken.

Als u Windows virtueel bureau blad (klassiek) gebruikt, gebruikt u de koppeling webclient in [dit artikel](./virtual-desktop-fall-2019/connect-web-2019.md) om verbinding te maken met uw resources.

Als dat niet werkt, moet u ervoor zorgen dat uw app-groep is gekoppeld aan een werk ruimte.

## <a name="web-client-stops-responding-or-disconnects"></a>Webclient reageert niet meer of de verbinding wordt verbroken

Probeer verbinding te maken met behulp van een andere browser of client.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Andere browsers en clients kunnen ook niet worden geopend

Als er problemen blijven optreden nadat u de browser hebt geswitcheerd, is het probleem mogelijk niet in uw browser, maar met uw netwerk. U wordt aangeraden contact op te nemen met netwerk ondersteuning.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webclient vraagt om referenties

Als de webclient om referenties wordt gevraagd, volgt u deze instructies:

1. Controleer of de URL van de webclient juist is.
2. Controleer of de referenties die u gebruikt voor de Windows Virtual Desktop-omgeving zijn gekoppeld aan de URL.
3. Wis browser cookies. Zie [cookie bestanden verwijderen in Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)voor meer informatie.
4. Wis de browsercache. Zie [browser cache voor uw browser wissen](https://binged.it/2RKyfdU)voor meer informatie.
5. Open uw browser in de persoonlijke modus.

## <a name="windows-client-blocks-windows-virtual-desktop-classic-feed"></a>Windows-client blokkeert Windows virtueel bureau blad (klassiek) feed

Volg de volgende instructies als de Windows client-feed geen Windows virtueel bureau blad-apps (Classic) weergeeft:

1. Controleer of het beleid voor voorwaardelijke toegang de app-Id's bevat die zijn gekoppeld aan virtueel bureau blad van Windows (klassiek).
2. Controleer of het beleid voor voorwaardelijke toegang alle toegang blokkeert, met uitzonde ring van de Windows-app-Id's voor virtueel bureau blad (klassiek). Als dat het geval is, moet u de App-ID **9cdead84-a844-4324-93f2-b2e6bb768d07** toevoegen aan het beleid, zodat de client de feeds kan detecteren.

Als u de App-ID 9cdead84-a844-4324-93f2-b2e6bb768d07 niet kunt vinden in de lijst, moet u de resource provider voor het virtuele bureau blad van Windows registreren. De resource provider registreren:

1. Meld u aan bij de Azure-portal.
2. Ga naar het **abonnement** en selecteer vervolgens uw abonnement.
3. Selecteer **resource provider** in het menu aan de linkerkant van de pagina.
4. Zoek en selecteer **micro soft. DesktopVirtualization** en selecteer **opnieuw registreren**.

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [omgeving en hostgroep maken](troubleshoot-set-up-issues.md)om problemen op te lossen tijdens het maken van een virtuele Windows-desktop omgeving en-hostgroep in een Windows-omgeving voor virtueel bureau blad.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie problemen [met veelvoorkomende problemen met Windows Virtual Desktop agent oplossen](troubleshoot-agent.md)voor informatie over het oplossen van problemen met de Windows Virtual Desktop agent of sessie verbinding.
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.

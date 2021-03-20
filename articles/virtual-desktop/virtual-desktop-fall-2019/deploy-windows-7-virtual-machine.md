---
title: Windows 7 virtuele machine implementeren Windows virtueel bureau blad (klassiek)-Azure
description: Een virtuele machine met Windows 7 configureren en implementeren op het virtuele bureau blad van Windows (klassiek).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 259e49fbdd6a0eb392ddf6a3cd3c318798cfabd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005057"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop-classic"></a>Een virtuele machine met Windows 7 implementeren op het virtuele bureau blad van Windows (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](../deploy-windows-7-virtual-machine.md)als u probeert Azure Resource Manager virtuele bureau blad-objecten die zijn geïntroduceerd in de huidige versie van Windows virtueel bureau blad, te beheren.

Het proces voor het implementeren van een virtuele machine met Windows 7 op Windows virtueel bureau blad wijkt enigszins af van de virtuele machines waarop latere versies van Windows worden uitgevoerd. In deze hand leiding wordt uitgelegd hoe u Windows 7 implementeert.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, volgt u de instructies in [een hostgroep met Power shell maken](create-host-pools-powershell-2019.md) om een hostgroep te maken. Volg daarna de instructies in [host Pools maken in azure Marketplace](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) om een of meer gebruikers toe te wijzen aan de groep bureau blad-toepassingen.

## <a name="configure-a-windows-7-virtual-machine"></a>Een virtuele machine met Windows 7 configureren

Wanneer u klaar bent met de vereisten, kunt u uw Windows 7-VM configureren voor implementatie op virtueel bureau blad van Windows.

Een Windows 7-VM instellen op het virtuele bureau blad van Windows:

1. Meld u aan bij de Azure Portal en zoek naar de installatie kopie van Windows 7 Enter prise of upload uw eigen aangepaste Windows 7 Enter prise (x64)-installatie kopie.
2. Implementeer een of meer virtuele machines met Windows 7 Enter prise als hostbesturingssysteem. Zorg ervoor dat de virtuele machines Remote Desktop Protocol (RDP) toestaan (de TCP/3389-poort).
3. Maak verbinding met de Windows 7 Enter prise-host met behulp van het RDP en verificatie met de referenties die u hebt gedefinieerd tijdens het configureren van uw implementatie.
4. Voeg het account dat u hebt gebruikt bij het maken van een verbinding met de host met RDP toe aan de groep Extern bureaublad gebruiker. Als u dit niet doet, kunt u mogelijk geen verbinding maken met de virtuele machine nadat u deze hebt toegevoegd aan uw Active Directory domein.
5. Ga naar Windows Update op uw VM.
6. Installeer alle Windows-updates in de belang rijke categorie.
7. Installeer alle Windows-updates in de optionele categorie (exclusief taal pakketten). Hiermee installeert u de Remote Desktop Protocol 8,0-update ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) die u nodig hebt om deze instructies te volt ooien.
8. Open de lokale Groepsbeleidsobjecteditor en navigeer naar **computer configuratie**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **extern bureaublad-services**  >  **extern bureaublad Session-Host**  >  **externe sessie omgeving**.
9. Schakel het beleid voor Remote Desktop Protocol 8,0 in.
10. Voeg deze VM toe aan uw Active Directory domein.
11. Start de virtuele machine opnieuw op door de volgende opdracht uit te voeren:

     ```cmd
     shutdown /r /t 0
     ```

12. Volg de instructies [hier](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) om een registratie token op te halen.
13. [Down load de Windows-agent voor virtueel bureau blad voor Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Down load de Windows Virtual Desktop Agent Manager voor Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Open het installatie programma voor de virtueel-bureaublad agent van Windows en volg de instructies. Wanneer u hierom wordt gevraagd, geeft u de registratie sleutel op die u in stap 12 hebt gemaakt.
16. Open de Windows Virtual Desktop Agent Manager en volg de instructies.
17. U kunt desgewenst de TCP/3389-poort blok keren om direct Remote Desktop Protocol toegang tot de virtuele machine te verwijderen.
18. U kunt ook controleren of uw .NET Framework ten minste versie 4.7.2. Dit is vooral belang rijk als u een aangepaste installatie kopie maakt.

## <a name="next-steps"></a>Volgende stappen

Uw Windows-implementatie voor virtueel bureau blad is nu klaar voor gebruik. [Down load de nieuwste versie van de Windows Virtual Desktop-Client](https://aka.ms/wvd/clients/windows) om aan de slag te gaan.

Voor een lijst met bekende problemen en instructies voor het oplossen van problemen met Windows 7 op virtueel bureau blad van Windows, raadpleegt u ons artikel over probleem oplossing bij het [oplossen van problemen met Windows 7 virtual machines in Windows virtueel bureau blad](troubleshoot-windows-7-vm.md).

---
title: Problemen met de service verbinding oplossen Windows virtueel bureau blad-Azure
description: Problemen oplossen tijdens het instellen van service verbindingen in een Windows Virtual Desktop-Tenant omgeving.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 42502864cfed177adfe487e9c59247579628fec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539072"
---
# <a name="windows-virtual-desktop-service-connections"></a>Windows Virtual Desktop service-verbindingen

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/troubleshoot-service-connection-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Gebruik dit artikel voor het oplossen van problemen met Windows-client verbindingen met virtueel bureau blad.

## <a name="provide-feedback"></a>Feedback geven

U kunt ons feedback geven en de Windows Virtual Desktop-service bespreken met het product team en andere Active community-leden op de [technische community van het Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Gebruiker maakt verbinding, maar er wordt niets weer gegeven (geen feed)

Een gebruiker kan Extern bureaublad-clients starten en kan worden geverifieerd, maar de gebruiker ziet geen pictogrammen in de feed voor webdetectie.

1. Controleer of de gebruiker die de problemen rapporteert, is toegewezen aan toepassings groepen met behulp van deze opdracht regel:

     ```powershell
     Get-AzRoleAssignment -SignInName <userupn>
     ```

2. Controleer of de gebruiker zich aanmeldt met de juiste referenties.

3. Als de WebClient wordt gebruikt, controleert u of er geen problemen met de referenties in de cache zijn.

4. Als de gebruiker deel uitmaakt van een Azure Active Directory (AD)-gebruikers groep, moet u ervoor zorgen dat de gebruikers groep een beveiligings groep is in plaats van een distributie groep. Virtuele Windows-Bureau bladen bieden geen ondersteuning voor Azure AD-distributie groepen.

## <a name="user-loses-existing-feed-and-no-remote-resource-is-displayed-no-feed"></a>Gebruiker verliest bestaande feed en er wordt geen externe bron weer gegeven (geen feed)

Deze fout treedt meestal op nadat een gebruiker zijn of haar abonnement van een Azure AD-Tenant naar een andere heeft verplaatst. Als gevolg hiervan verliest de service het bijhouden van hun gebruikers toewijzingen, omdat deze nog steeds zijn gekoppeld aan de oude Azure AD-Tenant.

Om dit probleem op te lossen, moet u de gebruikers opnieuw toewijzen aan hun app-groepen.

Dit kan ook gebeuren als een CSP-provider het abonnement heeft gemaakt en vervolgens is overgezet naar de klant. U kunt dit probleem oplossen door de resource provider opnieuw te registreren.

1. Meld u aan bij Azure Portal.
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

---
title: Windows-virtueel bureau blad Power shell-Azure
description: Problemen met Power shell oplossen bij het instellen van een virtuele Windows-desktop omgeving.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd02ee8ab794858566aeafa96fa78919be3b85a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367619"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Gebruik dit artikel om fouten en problemen op te lossen bij het gebruik van Power shell met Windows virtueel bureau blad. Zie [Windows Virtual Desktop Power shell](/powershell/windows-virtual-desktop/overview)(Engelstalig) voor meer informatie over Extern bureaublad-services Power shell.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Power shell-opdrachten die tijdens de installatie van Windows virtueel bureau blad worden gebruikt

In deze sectie vindt u Power shell-opdrachten die doorgaans worden gebruikt tijdens het instellen van Windows virtueel bureau blad en biedt manieren om problemen op te lossen die zich kunnen voordoen tijdens het gebruik van deze.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Fout: New-AzRoleAssignment: de verstrekte informatie is niet toegewezen aan een AD-object-ID

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**Oorzaak:** De gebruiker die is opgegeven door de para meter *-SignInName,* is niet gevonden in de Azure Active Directory gekoppeld aan de virtueel-bureaublad omgeving van Windows.

**Oplossen:** Zorg ervoor dat u het volgende kunt doen.

- De gebruiker moet worden gesynchroniseerd met Azure Active Directory.
- De gebruiker mag niet zijn gekoppeld aan Business-to-consumer (B2C) of Business-to-Business (B2B) commerce.
- De Windows-omgeving voor virtueel bureau blad moet zijn gekoppeld om Azure Active Directory te kunnen corrigeren.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Fout: New-AzRoleAssignment: de client met object-id heeft geen machtiging voor het uitvoeren van een actie over het bereik (code: AuthorizationFailed)

**Oorzaak 1:** Het account dat wordt gebruikt, heeft geen eigenaars machtigingen voor het abonnement.

**Oplossing 1:** Een gebruiker met eigenaars machtigingen moet de roltoewijzing uitvoeren. U moet de gebruiker ook toewijzen aan de rol beheerder van gebruikers toegang om een gebruiker toe te wijzen aan een toepassings groep.

**Oorzaak 2:** Het account dat wordt gebruikt, heeft eigenaars machtigingen, maar maakt geen deel uit van de Azure Active Directory van de omgeving of heeft geen machtigingen voor het opvragen van de Azure Active Directory waar de gebruiker zich bevindt.

**Oplossing 2:** Een gebruiker met Active Directory machtigingen moet de roltoewijzing uitvoeren.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Fout: New-AzWvdHostPool--de locatie is niet beschikbaar voor het resource type

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Oorzaak: Windows virtueel bureau blad ondersteunt het selecteren van de locatie van hostgroepen, toepassings groepen en werk ruimten om de meta gegevens van de service op bepaalde locaties op te slaan. Uw opties zijn beperkt tot waar deze functie beschikbaar is. Deze fout betekent dat de functie niet beschikbaar is op de locatie die u hebt gekozen.

Fix: in het fout bericht wordt een lijst met ondersteunde regio's gepubliceerd. Gebruik in plaats daarvan een van de ondersteunde regio's.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Fout: New-AzWvdApplicationGroup moet zich op dezelfde locatie betreden als de hostgroep

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Oorzaak:** Er is een niet-overeenkomende locatie. Alle hostgroepen, toepassings groepen en werk ruimten hebben een locatie om de meta gegevens van de service op te slaan. Objecten die u maakt en die aan elkaar zijn gekoppeld, moeten zich op dezelfde locatie bevinden. Als een hostgroep zich bijvoorbeeld in bevindt `eastus` , moet u ook de toepassings groepen maken in `eastus` . Als u een werk ruimte maakt voor het registreren van deze toepassings groepen, moet die werk ruimte `eastus` ook in zijn.

**Oplossen:** Haal de locatie op waarin de hostgroep is gemaakt en wijs vervolgens de toepassings groep toe die u maakt op dezelfde locatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [probleemoplossings overzicht, feedback en ondersteuning](troubleshoot-set-up-overview.md)voor een overzicht van het oplossen van problemen met het virtuele bureau blad van Windows en de escalatie trajecten.
- Zie [omgeving en hostgroep maken](troubleshoot-set-up-issues.md)om problemen op te lossen tijdens het instellen van uw Windows Virtual Desktop-omgeving en-hostgroepen.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) om problemen met extern bureaublad-clients op te lossen
- Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de service.
- Zie [bewerkingen controleren met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om de fouten te bepalen tijdens de implementatie.

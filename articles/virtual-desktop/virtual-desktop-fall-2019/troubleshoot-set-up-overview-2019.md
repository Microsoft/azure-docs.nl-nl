---
title: Overzicht van het oplossen van problemen met Windows virtueel bureau blad (klassiek)-Azure
description: Een overzicht van het oplossen van problemen bij het instellen van een Windows Virtual Desktop (klassieke) Tenant omgeving.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: efc96f03d93227cfffbb533bf1ce1786a0a47d14
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444153"
---
#  <a name="windows-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Overzicht van het oplossen van problemen met Windows Virtual Desktop (klassiek), feedback en ondersteuning

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../troubleshoot-set-up-overview.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

Dit artikel bevat een overzicht van de problemen die zich kunnen voordoen bij het instellen van een Windows Virtual Desktop-Tenant omgeving en biedt manieren om de problemen op te lossen.

## <a name="provide-feedback"></a>Feedback geven

Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) voor het bespreken van de Windows Virtual Desktop-service met het product team en de actieve leden van de community.

## <a name="escalation-tracks"></a>Escalatie trajecten

Gebruik de volgende tabel voor het identificeren en oplossen van problemen die zich kunnen voordoen bij het instellen van een Tenant omgeving met Extern bureaublad-client. Zodra de Tenant is ingesteld, kunt u onze nieuwe service voor [Diagnostische gegevens](diagnostics-role-service-2019.md) gebruiken om problemen voor veelvoorkomende scenario's te identificeren.

>[!NOTE]
> We hebben een technisch Community-Forum dat u kunt bezoeken om uw problemen met het product team en de actieve communityleden te bespreken. Ga naar de [technische community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) om een discussie te starten.

| **Probleem**                                                            | **Voorgestelde oplossing**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Een Windows-Tenant voor virtueel bureau blad maken                                                    | Als er een onderbreking van Azure is, [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/). anders [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **Windows virtueel bureau blad** voor de service, selecteert u **implementatie** voor het probleem type en selecteert u vervolgens problemen met het **maken van een virtueel bureau blad-Tenant voor Windows** voor het subtype van het probleem.|
| Toegang tot Marketplace-sjablonen in Azure Portal       | Als er een onderbreking van Azure is, [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Azure Marketplace-sjablonen voor virtueel bureau blad zijn gratis beschikbaar.|
| Toegang tot Azure Resource Manager sjablonen vanuit GitHub                                  | Zie de sectie Vm's voor het [maken van virtuele Bureau bladen voor Windows-sessies](troubleshoot-set-up-issues-2019.md#creating-windows-virtual-desktop-session-host-vms) van [Tenant en hostgroepen](troubleshoot-set-up-issues-2019.md)maken. Als het probleem nog steeds niet is opgelost, neemt u contact op met het [ondersteunings team van github](https://github.com/contact). <br> <br> Als de fout optreedt nadat u de sjabloon in GitHub hebt geopend, neemt u contact op met de [ondersteuning van Azure](https://azure.microsoft.com/support/create-ticket/).|
| Instellingen voor de Azure Virtual Network (VNET) en Express-route van de Session Host-pool               | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/)en selecteer vervolgens de betreffende service (onder de categorie netwerken). |
| De hostgroep van de virtuele machine (VM) maken wanneer Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad niet worden gebruikt | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/)en selecteer vervolgens de **virtuele machine met Windows** voor de service. <br> <br> Zie voor problemen met de Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad maken de sectie Tenant voor Windows-virtueel bureau blad voor het [maken van Tenant-en hostgroepen](troubleshoot-set-up-issues-2019.md). |
| Windows Virtual Desktop Session Host-omgeving beheren vanuit de Azure Portal    | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell-2019.md) of [een Azure-ondersteunings aanvraag openen](https://azure.microsoft.com/support/create-ticket/), **Windows virtueel bureau blad** voor de service, **configuratie en beheer** selecteren voor het probleem type en selecteer vervolgens **problemen met het configureren van tenants met Power shell** voor het subtype probleem. Extern bureaublad-services |
| Virtuele Windows-bureaublad configuratie beheren die zijn gekoppeld aan host Pools en toepassings groepen (app-groepen)      | Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell-2019.md), of [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteer **Windows virtueel bureau blad** voor de service en selecteer vervolgens het juiste probleem type.|
| FSLogix-profiel containers implementeren en beheren | Raadpleeg de [gids voor probleem oplossing voor FSLogix-producten](/fslogix/fslogix-trouble-shooting-ht/) en als het probleem niet wordt opgelost, [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **virtueel bureau blad voor Windows** voor de service, selecteert u **FSLogix** voor het probleem type en selecteert u vervolgens het juiste probleem subtype. |
| Storing van extern bureau blad-clients bij starten                                                 | Zie [problemen met de Extern bureaublad-client oplossen](../troubleshoot-client.md) en als het probleem niet wordt opgelost,  [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **Windows Virtual Desktop** voor de service en selecteert u vervolgens **extern bureaublad clients** voor het probleem type.  <br> <br> Als het een netwerk probleem is, moeten uw gebruikers contact opnemen met de netwerk beheerder. |
| Verbonden, maar geen feed                                                                 | Problemen oplossen met behulp van de [gebruiker verbinding maken, maar er wordt niets weer gegeven (geen feed)](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) van de [virtuele bureau blad-service verbindingen van Windows](troubleshoot-service-connection-2019.md). <br> <br> Als uw gebruikers zijn toegewezen aan een app-groep,  [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **Windows virtueel bureau blad** voor de service en selecteert u vervolgens **extern bureaublad clients** voor het probleem type. |
| Problemen met de detectie van feeds door het netwerk                                            | Uw gebruikers moeten contact opnemen met hun netwerk beheerder. |
| Clients verbinden                                                                    | Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection-2019.md) (Engelstalig) als u het probleem niet kunt oplossen, raadpleegt u de [virtuele machine configuratie](troubleshoot-vm-configuration-2019.md)van de host. |
| Reactie tijd van externe toepassingen of bureau blad                                      | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Licentie berichten of-fouten                                                          | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Problemen met het gebruik van virtuele bureau blad-hulpprogram ma's van Windows op GitHub (Azure Resource Manager sjablonen, diagnostische hulp programma, beheer hulpprogramma) | Zie [Azure Resource Manager sjablonen voor extern bureaublad-services voor](https://github.com/Azure/RDS-Templates/blob/master/README.md) het melden van problemen. |

## <a name="next-steps"></a>Volgende stappen

- Zie [Tenant en hostgroep maken](troubleshoot-set-up-issues-2019.md)voor informatie over het oplossen van problemen bij het maken van een Tenant en een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration-2019.md)van de host.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection-2019.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](../troubleshoot-client.md) om problemen met extern bureaublad-clients op te lossen
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell-2019.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](environment-setup-2019.md)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
- Zie [bewerkingen controleren met Resource Manager](../../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om fouten te bepalen tijdens de implementatie.

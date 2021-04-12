---
title: Overzicht van het oplossen van problemen met Windows virtueel bureau blad-Azure
description: Een overzicht van het oplossen van problemen bij het instellen van een virtuele Windows-desktop omgeving.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1108b736c9e2358ee57b34d7e88d2b841da71010
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445343"
---
# <a name="troubleshooting-overview-feedback-and-support-for-windows-virtual-desktop"></a>Probleemoplossings overzicht, feedback en ondersteuning voor virtueel bureau blad van Windows

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop met Azure Resource Manager Windows Virtual Desktop-objecten. Zie [dit artikel](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md) als u Windows Virtual Desktop (klassiek) zonder Azure Resource Manager-objecten gebruikt.

Dit artikel bevat een overzicht van de problemen die zich kunnen voordoen bij het instellen van een virtuele Windows-desktop omgeving en biedt manieren om de problemen op te lossen.

## <a name="report-issues"></a>Problemen melden

Ga naar de [tech-community van Windows virtueel bureau blad](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)voor het melden van problemen of Voorst Ellen voor Windows virtueel bureau blad met Azure Resource Manager-integratie. U kunt de technische community gebruiken voor het bespreken van aanbevolen procedures of suggesties en stemmen voor nieuwe functies.

Wanneer u een bericht voor hulp vraagt of een nieuwe functie voor stelt, zorg er dan voor dat u het onderwerp in zoveel mogelijk details beschrijft. Gedetailleerde informatie kan andere gebruikers helpen bij het beantwoorden van uw vraag of het begrijpen van de functie waarvoor u een stem voor stelt.

## <a name="escalation-tracks"></a>Escalatie trajecten

Voordat u iets anders doet, controleert u de [Azure-status pagina](https://status.azure.com/status) en [Azure service Health](https://azure.microsoft.com/features/service-health/) om te controleren of uw Azure-service correct wordt uitgevoerd.

Gebruik de volgende tabel voor het identificeren en oplossen van problemen die zich kunnen voordoen bij het instellen van een omgeving met Extern bureaublad-client. Als de omgeving is ingesteld, kunt u onze nieuwe [diagnose service](diagnostics-role-service.md) gebruiken om problemen voor algemene scenario's te identificeren.

| **Probleem**                                                            | **Voorgestelde oplossing**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| Instellingen voor de Azure Virtual Network (VNET) en Express-route van de Session Host-pool               | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/)en selecteer vervolgens de betreffende service (onder de categorie netwerken). |
| De hostgroep van de virtuele machine (VM) maken wanneer Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad niet worden gebruikt | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/)en selecteer vervolgens **Windows virtueel bureau blad** voor de service. <br> <br> Voor problemen met de Azure Resource Manager sjablonen die worden meegeleverd met Windows virtueel bureau blad, raadpleegt u Azure Resource Manager sjabloon fouten sectie van het maken van een [hostgroep](troubleshoot-set-up-issues.md). |
| Windows Virtual Desktop Session Host-omgeving beheren vanuit de Azure Portal    | [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/). <br> <br> Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md) of [een Azure-ondersteunings aanvraag openen](https://azure.microsoft.com/support/create-ticket/), **Windows virtueel bureau blad** voor de service selecteren, **configuratie en beheer** selecteren voor het probleem type en selecteer vervolgens **problemen met het configureren van een omgeving met Power shell** voor het subtype probleem. Extern bureaublad-services |
| Virtuele Windows-bureaublad configuratie beheren die zijn gekoppeld aan host Pools en toepassings groepen (app-groepen)      | Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md), of [Open een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteer **Windows virtueel bureau blad** voor de service en selecteer vervolgens het juiste probleem type.|
| FSLogix-profiel containers implementeren en beheren | Raadpleeg de [gids voor probleem oplossing voor FSLogix-producten](/fslogix/fslogix-trouble-shooting-ht/) en als het probleem niet wordt opgelost, [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **virtueel bureau blad voor Windows** voor de service, selecteert u **FSLogix** voor het probleem type en selecteert u vervolgens het juiste probleem subtype. |
| Storing van extern bureau blad-clients bij starten                                                 | Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) en als het probleem niet wordt opgelost,  [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **Windows Virtual Desktop** voor de service en selecteert u vervolgens **extern bureaublad clients** voor het probleem type.  <br> <br> Als het een netwerk probleem is, moeten uw gebruikers contact opnemen met de netwerk beheerder. |
| Verbonden, maar geen feed                                                                 | Problemen oplossen met behulp van de [gebruiker verbinding maken, maar er wordt niets weer gegeven (geen feed)](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) van de [virtuele bureau blad-service verbindingen van Windows](troubleshoot-service-connection.md). <br> <br> Als uw gebruikers zijn toegewezen aan een app-groep,  [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/), selecteert u **Windows virtueel bureau blad** voor de service en selecteert u vervolgens **extern bureaublad clients** voor het probleem type. |
| Problemen met de detectie van feeds door het netwerk                                            | Uw gebruikers moeten contact opnemen met hun netwerk beheerder. |
| Clients verbinden                                                                    | Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md) (Engelstalig) als u het probleem niet kunt oplossen, raadpleegt u de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host. |
| Reactie tijd van externe toepassingen of bureau blad                                      | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Licentie berichten of-fouten                                                          | Neem contact op met het team dat verantwoordelijk is voor het desbetreffende product als er problemen zijn gekoppeld aan een specifieke toepassing of product. |
| Problemen met verificatie methoden of hulpprogram ma's van derden | Controleer of uw externe provider Windows Virtual Desktop-scenario's ondersteunt en hoe u deze aanpakt met betrekking tot bekende problemen. |
| Problemen met het gebruik van Log Analytics voor virtueel bureau blad van Windows | Voor problemen met het diagnostische schema [opent u een ondersteunings aanvraag voor Azure](https://azure.microsoft.com/support/create-ticket/).<br><br>Voor query's, visualisaties of andere problemen in Log Analytics selecteert u het juiste probleem type onder Log Analytics. |
| Problemen met het gebruik van M365-apps | Neem contact op met het M365-beheer centrum met een van de Help-opties voor het [M365-beheer centrum](/microsoft-365/admin/contact-support-for-business-products/). |

## <a name="next-steps"></a>Volgende stappen

- Zie het maken van een [hostgroep](troubleshoot-set-up-issues.md)voor het oplossen van problemen bij het maken van een hostgroep in een virtueel-bureaublad omgeving van Windows.
- Zie voor het oplossen van problemen bij het configureren van een virtuele machine (VM) in Windows virtueel bureau blad de [virtuele machine configuratie](troubleshoot-vm-configuration.md)van de host.
- Zie problemen [met veelvoorkomende problemen met Windows Virtual Desktop agent oplossen](troubleshoot-agent.md)voor informatie over het oplossen van problemen met de Windows Virtual Desktop agent of sessie verbinding.
- Zie [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md)(Engelstalig) voor het oplossen van problemen met Windows Virtual Desktop-Client verbindingen.
- Zie [problemen met de Extern bureaublad-client oplossen](troubleshoot-client.md) om problemen met extern bureaublad-clients op te lossen
- Zie [Windows Virtual Desktop Power shell](troubleshoot-powershell.md)(Engelstalig) voor informatie over het oplossen van problemen met het gebruik van Power shell met Windows virtueel bureau blad.
- Zie [Windows Virtual Desktop Environment](environment-setup.md)(Engelstalig) voor meer informatie over de service.
- Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)om de zelf studie voor problemen oplossen op te lossen.
- Zie [bewerkingen controleren met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over controle acties.
- Zie [implementatie bewerkingen weer geven](../azure-resource-manager/templates/deployment-history.md)voor meer informatie over acties om fouten te bepalen tijdens de implementatie.

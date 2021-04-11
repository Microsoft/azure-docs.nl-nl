---
title: Servicewaarschuwingen instellen voor Windows Virtual Desktop (klassiek) - Azure
description: Hoe stel ik Azure Service Health in om servicemeldingen voor Windows Virtual Desktop (klassiek) te ontvangen?
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: aac22dfb95cc099a5d1243e120834415dfa61a4b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444255"
---
# <a name="tutorial-set-up-service-alerts-for-windows-virtual-desktop-classic"></a>Zelfstudie: Servicewaarschuwingen instellen voor Windows Virtual Desktop (klassiek)

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten. Raadpleeg [dit artikel](../set-up-service-alerts.md) als u Azure Resource Manager Windows Virtual Desktop-objecten probeert te beheren.

U kunt Azure Service Health gebruiken om serviceproblemen en statusadviezen voor Windows Virtual Desktop te monitoren. Via Azure Service Health kunt u verschillende soorten meldingen ontvangen (bijvoorbeeld e-mail of sms), inzicht krijgen in de impact van een probleem en op de hoogte worden gehouden tijdens het oplossen van een probleem. Ook kan Azure Service Health u helpen bij het verminderen van downtime en bij de voorbereiding op gepland onderhoud en wijzigingen die de beschikbaarheid van uw bronnen kunnen beïnvloeden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * servicewaarschuwingen maken en configureren.

Bekijk de [documentatie over Azure Health](../../service-health/index.yml) voor meer informatie over Azure Service Health.

## <a name="prerequisites"></a>Vereisten

- [Zelfstudie: een tenant maken in Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
- [Zelfstudie: service-principals en roltoewijzingen maken met PowerShell](create-service-principal-role-powershell.md)
- [Zelfstudie: een hostgroep maken met Azure Marketplace](create-host-pools-azure-marketplace-2019.md)

## <a name="create-service-alerts"></a>Servicewaarschuwingen maken

In deze sectie wordt uitgelegd hoe u Azure Service Health configureert en hoe u meldingen instelt die u kunt openen op de Microsoft Azure-portal. U kunt verschillende soorten waarschuwingen instellen en deze zo plannen dat ze u tijdig op de hoogte brengen.

### <a name="recommended-service-alerts"></a>Aanbevolen servicewaarschuwingen

We raden u aan servicewaarschuwingen te maken voor de volgende statusgebeurtenistypes:

- **Serviceprobleem:** ontvang meldingen over belangrijke problemen die van invloed zijn op de connectiviteit van uw gebruikers met de service of met de mogelijkheid om uw Windows Virtual Desktop-tenant te beheren.
- **Statusadvies:** ontvang meldingen die uw aandacht vereisen. Hier volgen enkele voorbeelden van dit type melding:
    - Virtual Machines (Vm's) niet veilig geconfigureerd als open poort 3389
    - Afkeuring van functionaliteit

### <a name="configure-service-alerts"></a>Servicewaarschuwingen configureren

Om servicewaarschuwingen te configureren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Service Health.**
3. Gebruik de instructies in [Waarschuwingen voor activiteitenlogboeken maken met servicemeldingen](../../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json) om uw waarschuwingen en meldingen in te stellen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Azure Service Health instelt en gebruikt om serviceproblemen en statusadviezen voor Windows Virtual Desktop te monitoren. Voor meer informatie over hoe u zich aanmeldt bij Windows Virtual Desktop, gaat u verder met de instructies voor verbinding maken met Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Verbinding maken met de Remote Desktop-client op Windows 7 en Windows 10](connect-windows-7-10-2019.md)
---
title: Logboeken integreren met ArcSight met behulp van Azure Monitor | Microsoft Docs
description: Meer informatie over het integreren van Azure Active Directory-logboeken met ArcSight met behulp van Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7bcab04da005fd0d46d18e7b708dcb1c9d58e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230514"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Logboeken van Azure Active Directory integreren met ArcSight met behulp van Azure Monitor

[Micro focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) is een Siem-oplossing (Security Information and Event Management) waarmee u beveiligings dreigingen in uw platform kunt detecteren en erop kan reageren. U kunt Azure Active Directory-Logboeken (Azure AD) nu routeren naar ArcSight met behulp van Azure Monitor met behulp van de ArcSight-connector voor Azure AD. Met deze functie kunt u uw Tenant controleren op beveiligings problemen met behulp van ArcSight.  

In dit artikel leert u hoe u Azure AD-Logboeken kunt door sturen naar ArcSight met behulp van Azure Monitor. 

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:
* Een Azure-Event Hub die Azure AD-activiteiten Logboeken bevat. Meer informatie over het [streamen van uw activiteiten logboeken naar een event hub](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 
* Een geconfigureerd exemplaar van ArcSight syslog NG aardgas SmartConnector (SmartConnector) of ArcSight Load Balancer. Als de gebeurtenissen worden verzonden naar ArcSight Load Balancer, worden deze daarom naar de SmartConnector verzonden door de Load Balancer.

Down load en open de [configuratie handleiding voor ArcSight SmartConnector voor Azure monitor event hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Deze hand leiding bevat de stappen die u moet installeren en configureren van de ArcSight SmartConnector voor Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Azure AD-logboeken integreren met ArcSight

1. Voer eerst de stappen uit in de sectie **vereisten** van de configuratie handleiding. Deze sectie bevat de volgende stappen:
    * Stel de gebruikers machtigingen in Azure in om ervoor te zorgen dat er een gebruiker is met de rol van **eigenaar** voor het implementeren en configureren van de connector.
    * Open poorten op de server met syslog NG daemon SmartConnector, zodat deze toegankelijk is vanuit Azure. 
    * De implementatie voert een Windows Power shell-script uit. Daarom moet u Power shell inschakelen om scripts uit te voeren op de computer waarop u de connector wilt implementeren.

2. Volg de stappen in het gedeelte **de connector implementeren** van de configuratie handleiding voor het implementeren van de connector. In deze sectie vindt u instructies voor het downloaden en uitpakken van de connector, het configureren van toepassings eigenschappen en het uitvoeren van het implementatie script uit de uitgepakte map. 

3. Volg de stappen in de **implementatie in azure controleren** om ervoor te zorgen dat de connector correct is ingesteld en werkt. Controleer het volgende:
    * De vereiste Azure-functies worden gemaakt in uw Azure-abonnement.
    * De Azure AD-logboeken worden naar de juiste bestemming gestreamd. 
    * De toepassings instellingen van uw implementatie worden opgeslagen in de toepassings instellingen in azure function-apps. 
    * Er wordt een nieuwe resource groep voor ArcSight gemaakt in azure, met een Azure AD-toepassing voor de ArcSight-connector en opslag accounts met de toegewezen bestanden in CEF-indeling.

4. Voltooi ten slotte de stappen na de implementatie in de configuratie handleiding **na de implementatie** . In deze sectie wordt uitgelegd hoe u aanvullende configuratie kunt uitvoeren als u een App Service plan bent om te voor komen dat de functie-apps na een time-outperiode niet meer actief zijn, streaming van bron logboeken van de Event Hub te configureren en het SysLog NG-daemon SmartConnector te updaten om het te koppelen aan het zojuist gemaakte opslag account.

5. De configuratie handleiding legt ook uit hoe u de connector eigenschappen in azure kunt aanpassen en hoe u de connector kunt bijwerken en verwijderen. Er is ook een sectie over prestatie verbeteringen, waaronder het upgraden naar een [Azure-verbruiks plan](https://azure.microsoft.com/pricing/details/functions) en het configureren van een ArcSight Load Balancer als de gebeurtenis belasting groter is dan een enkele syslog-daemon-SmartConnector kunnen verwerken.

## <a name="next-steps"></a>Volgende stappen

[Configuratie handleiding voor ArcSight SmartConnector voor Azure Monitor Event hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
---
title: Geïntegreerde werk belasting beveiliging van Azure Security Center inschakelen
description: Meer informatie over hoe u Azure Defender kunt gebruiken om de beveiliging van Azure Security Center uit te breiden naar uw hybride en multicloud-resources
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108874"
---
# <a name="quickstart-enable-azure-defender"></a>Snelstartgids: Azure Defender inschakelen

Zie [Security Center Free VS Azure Defender enabled](security-center-pricing.md)(Engelstalig) voor meer informatie over de voor delen van Azure Defender.

## <a name="prerequisites"></a>Vereisten

Voor de quickstarts en zelfstudies van Security Center moet u Azure Defender inschakelen. 

U kunt een volledig Azure-abonnement beschermen met Azure Defender en de beschermingen worden overgenomen door alle resources binnen het abonnement.

Er is een gratis proefversie voor 30 dagen beschikbaar. Zie [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor prijs informatie in de valuta van uw keuze en volgens uw regio.

## <a name="enable-azure-defender-from-the-azure-portal"></a>Azure Defender inschakelen vanaf de Azure Portal

Als u de alle bedreigingsbeschermingsfuncties van Security Center wilt inschakelen, moet u Azure Defender inschakelen voor het abonnement dat de van toepassing zijnde workloads bevat. Als u dit op werkruimteniveau inschakelt, betekent dat niet dat just-in-time toegang tot VM's wordt ingeschakeld, evenmin als adaptieve toepassingscontroles en netwerkdetecties voor Azure-resources. Daarnaast zijn de enige Azure Defender-abonnementen die op werkruimteniveau beschikbaar zijn Azure Defender voor servers en Azure Defender voor SQL-servers op computers.

- U kunt **Azure Defender inschakelen voor opslag accounts** op abonnements niveau of op het niveau van de resource
- U kunt **Azure Defender voor SQL** inschakelen op abonnements niveau of op het niveau van de resource
- U kunt bedreigings beveiliging alleen inschakelen voor **Azure database for MariaDB/MySQL/PostgreSQL** op het niveau van de resource

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>Azure Defender inschakelen voor uw abonnementen en werk ruimten:

- Azure Defender inschakelen op één abonnement:

    1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.
    1. Selecteer het abonnement of de werk ruimte die u wilt beveiligen.
    1. Selecteer **Azure Defender aan** als u een upgrade wilt uitvoeren.
    1. Selecteer **Opslaan**.

    > [!TIP]
    > U ziet dat elk abonnement in Azure Defender afzonderlijk wordt geprijsd en afzonderlijk op aan of uit kan worden ingesteld. Het is bijvoorbeeld mogelijk dat u Azure Defender wilt uitschakelen voor App Service op abonnementen waaraan geen Azure App Service plan is gekoppeld. 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="De pagina met prijzen van Security Center in de portal":::

- Azure Defender inschakelen op meerdere abonnementen of werk ruimten:

    1. Selecteer **Aan de slag** in de zijbalk van Security Center.

        Op het tabblad **Upgrade** worden abonnementen en werkruimten vermeld die in aanmerking komen voor onboarding.

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="Tabblad Upgrade van de pagina Aan de slag"::: 

    1. Selecteer in de lijst **Abonnementen en werk ruimten selecteren om Azure Defender in te scha kelen** de abonnementen en werk ruimten die u wilt bijwerken en selecteer **upgrade** om Azure Defender in te scha kelen.

       - Als u abonnementen en werkruimten selecteert die niet in aanmerking komen voor een proefversie, wordt de volgende stap het uitvoeren van een upgrade en worden er kosten in rekening gebracht.
       - Als u een werkruimte selecteert die in aanmerking komt voor een gratis proefversie, wordt in de volgende stap een proefversie gestart.

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="Alle geselecteerde werk ruimten en abonnementen bijwerken vanaf de pagina aan de slag":::


## <a name="next-steps"></a>Volgende stappen

Nu u Azure Defender hebt ingeschakeld, schakelt u het automatisch verzamelen van gegevens in door de benodigde agents en extensies die zijn beschreven in [agents en uitbrei dingen voor automatische inrichting van Azure Security Center](security-center-enable-data-collection.md).
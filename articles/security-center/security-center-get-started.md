---
title: Upgraden naar Azure Defender - Azure Security Center
description: In deze quickstart wordt beschreven hoe u upgradet naar Azure Defender van Security Center voor extra beveiliging.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099349"
---
# <a name="quickstart-set-up-azure-security-center"></a>Quickstart: Azure Security Center instellen

Azure Security Center biedt geïntegreerde beveiligings beheer en bedreigings beveiliging in uw hybride en meerdere Cloud werkbelastingen. De gratis functies bieden beperkte beveiliging voor alleen uw Azure-resources, maar Azure Defender biedt deze mogelijkheden ook voor on-premises en andere clouds. Azure Defender van Security Center helpt u beveiligingsproblemen te vinden en op te lossen, toegangs- en toepassingsbesturingselementen toe te passen om schadelijke activiteiten te blokkeren, bedreigingen te detecteren met behulp van analyses en gegevens en snel te reageren bij aanvallen. U kunt Azure Defender gratis uitproberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

In deze snelstartgids vindt u alle aanbevolen stappen voor het inschakelen van Azure Security Center en Azure Defender. Wanneer u alle Quick Start-stappen hebt voltooid, hebt u het volgende gedaan:

> [!div class="checklist"]
> * Security Center ingeschakeld in uw Azure-abonnementen
> * Azure Defender is ingeschakeld voor uw Azure-abonnementen
> * Automatische gegevens verzameling instellen
> * E-mail meldingen die zijn ingesteld voor beveiligings waarschuwingen
> * Uw hybride en multi-Cloud machines die zijn verbonden met Azure

## <a name="prerequisites"></a>Vereisten
U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).

Als u Azure Defender wilt inschakelen voor een abonnement, moet de rol Abonnementhouder, Inzender of Beveiligingsbeheerder aan u zijn toegewezen.

## <a name="enable-security-center-on-your-azure-subscription"></a>Security Center inschakelen voor uw Azure-abonnement

> [!TIP]
> Zie [Enable Security Center on multiple Azure subscriptions](onboard-management-group.md) voor het inschakelen van Security Center op alle abonnementen in een beheergroep.

1. Meld u aan bij de [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. Selecteer **Security Center** in het menu van de portal. 

    De overzichtspagina van Security Center wordt geopend.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Overzichtsdashboard van Security Center" lightbox="./media/security-center-get-started/overview.png":::

    **Security Center - overzicht** biedt een duidelijk overzicht van de beveiligingsstatus van uw hybride cloudworkloads, zodat u de beveiliging van uw workloads kunt bepalen en beoordelen en risico's kunt herkennen en verminderen. Security Center activeert automatisch, en gratis, de Azure-abonnementen waarvoor u of een andere abonnementsgebruiker niet eerder onboarding hebt uitgevoerd.

U kunt de lijst met abonnementen bekijken en filteren door het menu-item **Abonnementen** te selecteren. Security Center past de weergave aan om het beveiligingspostuur van de geselecteerde abonnementen weer te geven. 

Binnen enkele minuten nadat u Security Center voor het eerst hebt gestart, ziet u mogelijk het volgende:

- **Aanbevelingen** voor manieren om de beveiliging van uw verbonden resources te verbeteren.
- Een overzicht van uw resources die nu worden beoordeeld door Security Center en het beveiligingspostuur van elke resource.

Als u optimaal gebruik wilt maken van Security Center, gaat u door met de volgende stappen in de sectie Quick Start.



## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u Azure Security Center ingeschakeld. De volgende stap is om Azure Defender in te scha kelen voor Unified Security Management en bedreigings beveiliging in uw hybride Cloud werkbelastingen.

> [!div class="nextstepaction"]
> [Snelstartgids: Azure Defender inschakelen](enable-azure-defender.md)
---
title: Upgraden naar Azure Defender - Azure Security Center
description: In deze quickstart wordt beschreven hoe u upgradet naar Azure Defender van Security Center voor extra beveiliging.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: bd8c5c31579ac22cd6c77dffd31103680e393f69
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187167"
---
# <a name="quickstart-setting-up-azure-security-center"></a>Quickstart: Azure Security Center instellen

Azure Security Center biedt geïntegreerd beveiligingsbeheer en bedreigingsbeveiliging voor uw verschillende hybride cloudworkloads. De gratis functies bieden beperkte beveiliging voor alleen uw Azure-resources, maar Azure Defender biedt deze mogelijkheden ook voor on-premises en andere clouds. Azure Defender van Security Center helpt u beveiligingsproblemen te vinden en op te lossen, toegangs- en toepassingsbesturingselementen toe te passen om schadelijke activiteiten te blokkeren, bedreigingen te detecteren met behulp van analyses en gegevens en snel te reageren bij aanvallen. U kunt Azure Defender gratis uitproberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

In dit artikel gaat u upgraden naar Azure Defender voor extra beveiliging en de Log Analytics-agent installeren op uw virtuele machines om te controleren op beveiligingsproblemen en bedreigingen.

## <a name="prerequisites"></a>Vereisten
U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).

Als u Azure Defender wilt inschakelen voor een abonnement, moet de rol Abonnementhouder, Inzender of Beveiligingsbeheerder aan u zijn toegewezen.


## <a name="enable-security-center-on-your-azure-subscription"></a>Security Center inschakelen voor uw Azure-abonnement

1. Meld u aan bij de [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. Selecteer **Security Center** in het menu van de portal. 

    De overzichtspagina van Security Center wordt geopend.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Overzichtsdashboard van Security Center" lightbox="./media/security-center-get-started/overview.png":::

**Security Center - overzicht** biedt een duidelijk overzicht van de beveiligingsstatus van uw hybride cloudworkloads, zodat u de beveiliging van uw workloads kunt bepalen en beoordelen en risico's kunt herkennen en verminderen. Security Center activeert automatisch, en gratis, de Azure-abonnementen waarvoor u of een andere abonnementsgebruiker niet eerder onboarding hebt uitgevoerd.

U kunt de lijst met abonnementen bekijken en filteren door het menu-item **Abonnementen** te selecteren. Security Center past de weergave aan om het beveiligingspostuur van de geselecteerde abonnementen weer te geven. 

Binnen enkele minuten nadat u Security Center voor het eerst hebt gestart, ziet u mogelijk het volgende:

- **Aanbevelingen** voor manieren om de beveiliging van uw verbonden resources te verbeteren.
- Een overzicht van uw resources die nu worden beoordeeld door Security Center en het beveiligingspostuur van elke resource.

Voltooi de stappen hieronder om Azure Defender in te schakelen en de Log Analytics-agent te installeren om zo volledig van Security Center te profiteren.

> [!TIP]
> Zie [Enable Security Center on multiple Azure subscriptions](onboard-management-group.md) voor het inschakelen van Security Center op alle abonnementen in een beheergroep.

## <a name="enable-azure-defender"></a>Azure Defender inschakelen

Voor de quickstarts en zelfstudies van Security Center moet u Azure Defender inschakelen. Er is een gratis proefversie voor 30 dagen beschikbaar. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie. 

1. Selecteer **Aan de slag** in de zijbalk van Security Center.

    :::image type="content" source="./media/security-center-get-started/get-started-upgrade-tab.png" alt-text="Tabblad Upgrade van de pagina Aan de slag"::: 

    Op het tabblad **Upgrade** worden abonnementen en werkruimten vermeld die in aanmerking komen voor onboarding.

1. Selecteer in de lijst **Werkruimten selecteren waarvoor Azure Defender moet worden ingeschakeld** de werkruimten waarvoor een upgrade moet worden uitgevoerd.
   - Als u abonnementen en werkruimten selecteert die niet in aanmerking komen voor een proefversie, wordt de volgende stap het uitvoeren van een upgrade en worden er kosten in rekening gebracht.
   - Als u een werkruimte selecteert die in aanmerking komt voor een gratis proefversie, wordt in de volgende stap een proefversie gestart.
1. Selecteer **Upgrade** om Azure Defender in te schakelen.

## <a name="enable-automatic-data-collection"></a>Automatische gegevensverzameling inschakelen
Security Center verzamelt gegevens van uw machines om deze te controleren op beveiligingsproblemen en bedreigingen. De gegevens worden verzameld met behulp van de Log Analytics-agent, die verschillende configuraties en gebeurtenislogboeken met betrekking tot beveiliging van de machine leest en de gegevens kopieert naar uw werkruimte voor analyse. Security Center maakt standaard een nieuwe werkruimte voor u.

Als automatisch inrichten is ingeschakeld, installeert Security Center de Log Analytics-agent op alle ondersteunde machines en op nieuwe machines. Automatische inrichting wordt sterk aanbevolen.

Automatische inrichting van de Log Analytics-agent inschakelen:

1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.
1. Selecteer het betreffende abonnement.
1. Stel op de pagina **Gegevensverzameling** **Automatische inrichting** in op **Aan**.
1. Selecteer **Opslaan**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Automatische inrichting van de Log Analytics-agent inschakelen":::

>[!TIP]
> Als een werkruimte moet worden ingericht, kan de installatie van de agent tot wel 25 minuten duren.

Als de agent is geïmplementeerd op uw machines, kan Security Center extra aanbevelingen doen met betrekking tot de updatestatus van het systeem, de beveiligingsconfiguraties van het besturingssysteem en de eindpuntbeveiliging. Ook kan Security Center aanvullende beveiligingswaarschuwingen genereren.

>[!NOTE]
> Wanneer u automatische inrichting instelt op **Uit**, wordt de Log Analytics-agent niet verwijderd van Azure-VM's waarop de agent al is ingericht. Door automatische inrichting uit te schakelen, wordt de beveiligingsbewaking voor uw resources beperkt.



## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u Azure Defender ingeschakeld en de Log Analytics-agent ingericht voor geïntegreerd beveiligingsbeheer en bescherming tegen bedreigingen voor uw hybride cloudworkloads. Ga naar de snelstartgids voor het onboarden van Windows-computers die zich on-premises en in andere clouds bevinden voor meer informatie over hoe u Security Center kunt gebruiken.

> [!div class="nextstepaction"]
> [Snelstart: Niet-Azure-computers onboarden](quickstart-onboard-machines.md)

Wilt u optimaliseren en op uw cloudverbruik besparen?

> [!div class="nextstepaction"]
> [Analyseer kosten met Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
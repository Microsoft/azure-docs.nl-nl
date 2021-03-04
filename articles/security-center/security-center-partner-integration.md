---
title: Beveiligingsoplossingen integreren in Azure Security Center | Microsoft Docs
description: Leer hoe Azure Security Center kan worden geïntegreerd met partners om de algehele beveiliging van uw Azure-resources te verbeteren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: ff23a1fa4b631fc10163f22d94ccdbd8cbe657c2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099247"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Beveiligingsoplossingen integreren in Azure Security Center
Dit document helpt u bij het beheren van beveiligingsoplossingen die al zijn gekoppeld aan Azure Security Center en bij het toevoegen van nieuwe oplossingen.

## <a name="integrated-azure-security-solutions"></a>Geïntegreerde Azure-beveiligingsoplossingen
Met Security Center kunt u gemakkelijk geïntegreerde beveiligingsoplossingen in Azure inschakelen. Voordelen zijn:

- **Vereenvoudigde implementatie**: Security Center biedt gestroomlijnde inrichting van geïntegreerde partneroplossingen. Security Center kan de agent op uw virtuele machines inrichten voor oplossingen zoals antimalware en evaluatie van beveiligings problemen. Voor firewall apparaten kan Security Center een groot deel van de vereiste netwerk configuratie zijn.
- **Geïntegreerde detecties**: beveiligings gebeurtenissen van partner oplossingen worden automatisch verzameld, geaggregeerd en weer gegeven als onderdeel van Security Center waarschuwingen en incidenten. Deze gebeurtenissen worden ook gekoppeld aan detecties van andere bronnen voor geavanceerde detectie van bedreigingen.
- **Geïntegreerde statuscontrole en beheer**: klanten kunnen geïntegreerde statusgebeurtenissen gebruiken om alle partneroplossingen in één oogopslag te controleren. Er zijn basisbeheermogelijkheden beschikbaar, met eenvoudige toegang tot geavanceerde installatie met behulp van de partneroplossing.

Geïntegreerde beveiligings oplossingen omvatten momenteel de evaluatie van beveiligings problemen met [Qualys](https://www.qualys.com/public-cloud/#azure) en [Rapid7](https://www.rapid7.com/products/insightvm/) en [Microsoft Azure Web Application firewall op Azure-toepassing gateway](../web-application-firewall/ag/ag-overview.md).

> [!NOTE]
> Security Center installeert de Log Analytics agent niet op virtualisatie apparaten van de partner, omdat de meeste beveiligings leveranciers externe agents die op hun apparaten worden uitgevoerd, niet verbieden.

Zie voor meer informatie over de integratie van hulpprogram ma's voor het scannen van beveiligings problemen van Qualys, met inbegrip van een ingebouwde scanner die beschikbaar is voor Azure Defender-klanten, [de evaluatie van beveiligings problemen voor uw Azure-virtual machines](deploy-vulnerability-assessment-vm.md).

Security Center biedt ook een beveiligingslek met betrekking tot de analyse van uw:

* SQL-data bases-Zie [evaluatie van beveiligings problemen analyseren in het dash board evaluatie van beveiligings problemen](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)
* Azure Container Registry-installatie kopieën: Zie [Azure Defender gebruiken voor container registers voor het scannen van uw installatie kopieën op beveiligings problemen](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Beveiligingsoplossingen integreren
Azure-beveiligingsoplossingen die zijn geïmplementeerd vanuit Security Center, zijn automatisch verbonden. U kunt ook verbinding maken met andere beveiligings gegevens bronnen, waaronder computers die on-premises of in andere Clouds worden uitgevoerd.

[![Integratie van partneroplossingen](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Geïntegreerde Azure-beveiligingsoplossingen en andere gegevensbronnen beheren

1. Ga naar [Azure Portal](https://azure.microsoft.com/features/azure-portal/) en open **Security Center**.

1. Selecteer in het menu van Security Center **beveiligings oplossingen**.

Op de pagina **beveiligings oplossingen** kunt u de status van geïntegreerde Azure-beveiligings oplossingen bekijken en basis beheer taken uitvoeren.

### <a name="connected-solutions"></a>Verbonden oplossingen

De sectie **Connected Solutions** bevat beveiligings oplossingen die momenteel zijn verbonden met Security Center. Ook wordt de status van elke oplossing weer gegeven.  

![Verbonden oplossingen](./media/security-center-partner-integration/connected-solutions.png)

De status van een partner oplossing kan zijn:

* **In orde** (groen): er zijn geen problemen met de status.
* **Slecht** (rood): er is een probleem met de status dat onmiddellijke aandacht vereist.
* **Rapportage gestopt** (oranje): de oplossing is gestopt met het rapporteren van de status.
* **Niet gerapporteerd** (grijs): de oplossing heeft nog niets gerapporteerd en er zijn geen status gegevens beschikbaar. De status van een oplossing kan niet worden gerapporteerd als deze onlangs is verbonden en nog steeds wordt geïmplementeerd.

> [!NOTE]
> Als de status gegevens niet beschikbaar zijn, worden Security Center de datum en tijd weer gegeven van de laatste ontvangen gebeurtenis om aan te geven of de oplossing al dan niet rapporteert. Als er geen status gegevens beschikbaar zijn en er in de afgelopen 14 dagen geen waarschuwingen zijn ontvangen, geeft Security Center aan dat de oplossing slecht is of niet wordt gerapporteerd.
>
>

Selecteer **weer geven** voor meer informatie en opties, zoals:

   - **Solution console** : Hiermee opent u de beheer ervaring voor deze oplossing.
   - **Koppelings-VM** : Hiermee opent u de pagina koppelings toepassingen. Hier kunt u resources verbinden met de partneroplossing.
   - **Oplossing verwijderen**
   - **Configureerer**

   ![Details van partneroplossingen](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Gedetecteerde oplossingen

Security Center detecteert automatisch beveiligings oplossingen die worden uitgevoerd in azure, maar zijn niet verbonden met Security Center en de oplossingen worden weer gegeven in de sectie **gedetecteerde oplossingen** . Deze oplossingen omvatten Azure-oplossingen, zoals [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)en partner oplossingen.

> [!NOTE]
> Schakel **Azure Defender** in op het abonnements niveau voor de functie gedetecteerde oplossingen. Meer informatie vindt u in [Quick Start: Azure Defender inschakelen](enable-azure-defender.md).

Selecteer **verbinding maken** onder een oplossing om te integreren met Security Center en op de hoogte te worden gesteld van beveiligings waarschuwingen.

### <a name="add-data-sources"></a>Gegevensbronnen toevoegen

Het gedeelte **Gegevensbronnen toevoegen** bevat andere beschikbare gegevensbronnen die kunnen worden verbonden. Klik op **TOEVOEGEN** voor instructies over het toevoegen van gegevens uit een van deze bronnen.

![Gegevensbronnen](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u kunnen lezen hoe u partneroplossingen integreert in Security Center. Zie [continu Security Center gegevens exporteren](continuous-export.md)voor meer informatie over het instellen van een integratie met Azure Sentinel of andere Siem.
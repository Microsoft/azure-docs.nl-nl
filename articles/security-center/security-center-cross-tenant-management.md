---
title: Beheer van meerdere tenants in Azure Security Center | Microsoft Docs
description: Meer informatie over het instellen van cross-Tenant beheer voor het beheren van de beveiligings postuur van meerdere tenants in Security Center met behulp van het beheer van gedelegeerde resources van Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: d76ecc55c2e156199b2db789721946fd1f36de27
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922915"
---
# <a name="cross-tenant-management-in-security-center"></a>Beheer van meerdere tenants in Security Center

Met beheer op meerdere tenants kunt u de beveiligings postuur van meerdere tenants in Security Center weer geven en beheren door gebruik te maken van [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md) . Beheer meerdere tenants efficiënt, vanuit één weer gave, zonder dat u zich hoeft aan te melden bij de directory van elke Tenant.

- Service providers kunnen de beveiligings postuur van resources, voor meerdere klanten, beheren vanuit hun eigen Tenant.

- Beveiligings teams van organisaties met meerdere tenants kunnen hun beveiligings postuur op één locatie bekijken en beheren.

## <a name="set-up-cross-tenant-management"></a>Beheer van meerdere tenants instellen

Azure delegated resource management is een van de belangrijkste onderdelen van Azure Lighthouse. Stel beheer van meerdere tenants in door de toegang tot resources van beheerde tenants over te dragen aan uw eigen Tenant met behulp van de instructies in de documentatie van Azure Lighthouse: [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md).


## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Hoe werkt het beheer van cross-tenants in Security Center

U kunt abonnementen op meerdere tenants bekijken en beheren op dezelfde manier als u meerdere abonnementen beheert in één Tenant.

Klik op het filter pictogram in de bovenste menu balk en selecteer de abonnementen, in de map van elke Tenant, die u wilt weer geven.

  ![Tenants filteren](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

De weer gaven en acties zijn in principe hetzelfde. Enkele voorbeelden:

- **Beveiligings beleid beheren**: vanuit de ene weer gave kunt u de beveiligings postuur van veel resources met [beleids regels](tutorial-security-policy.md)beheren, acties uitvoeren met aanbevelingen voor beveiliging en beveiligings gegevens verzamelen en beheren.
- **Verbeter de beveiligde Score-en nalevings postuur**: door de zicht baarheid van meerdere tenants kunt u de algemene beveiligings-postuur van al uw tenants bekijken en waar en hoe u de [beveiligde Score](secure-score-security-controls.md) en de [nalevings postuur](security-center-compliance-dashboard.md) voor elk van hen het beste kunt verbeteren.
- **Aanbevelingen herstellen**: bewaak en herstel een [aanbeveling](security-center-recommendations.md) voor veel resources van verschillende tenants tegelijk. Vervolgens kunt u de beveiligings problemen die het hoogst mogelijke risico op alle tenants Voorst Ellen, direct aanpakken.
- **Waarschuwingen beheren**: [waarschuwingen](security-center-alerts-overview.md) detecteren in de verschillende tenants. Onderneem actie op resources die niet compatibel zijn met herstels [tappen](security-center-managing-and-responding-alerts.md)waarvoor actie kan worden ondernomen.

- **Geavanceerde functies voor Cloud beveiliging en meer beheren**: beheer de verschillende beveiligings Services voor dreigingen, zoals [just-in-time-VM-toegang](security-center-just-in-time.md), [adaptieve netwerk beveiliging](security-center-adaptive-network-hardening.md), [adaptieve toepassings besturings elementen](security-center-adaptive-application.md), en meer.
 
## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt uitgelegd hoe cross-Tenant beheer werkt in Security Center. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Breid uw beveiligings postuur uit met Azure Security Center](security-center-monitoring.md) -Leer hoe u de status van uw Azure-resources kunt bewaken.
* [Veelgestelde](faq-general.md) vragen over Azure Security Center: vind een veelgestelde vraag over het gebruik van de service.
* [Meer informatie over Azure Lighthouse in Enter prise-scenario's](../lighthouse/concepts/enterprise.md) : Ontdek hoe Azure Lighthouse het beheer van meerdere tenants in een onderneming kan vereenvoudigen. Dit maakt gebruik van verschillende Azure AD-tenants.
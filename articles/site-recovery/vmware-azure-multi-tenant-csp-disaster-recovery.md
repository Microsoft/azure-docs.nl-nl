---
title: VMware-nood herstel instellen voor Azure in een multitenancy-omgeving met Site Recovery en het programma Cloud Solution Provider (CSP) | Microsoft Docs
description: Hierin wordt beschreven hoe u met behulp van Azure Site Recovery VMware-herstel na nood gevallen instelt in een omgeving met meerdere tenants.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: fb2a8a7bb14758ab21eb2183a119f456b53c8562
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97654946"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>VMware-nood herstel instellen in een omgeving met meerdere pacht met het provider programma voor cloud oplossingen

Het [CSP-programma](https://partner.microsoft.com/cloud-solution-provider) bevordert betere verhalen voor micro soft-Cloud Services, waaronder Microsoft 365, Enter prise Mobility Suite en Microsoft Azure. Met CSP hebben partners eigenaar van de end-to-end-relatie met klanten en worden ze het primaire relatie punt van relaties. Partners kunnen Azure-abonnementen voor klanten implementeren en de abonnementen combi neren met hun eigen toegevoegde waarde, aangepaste aanbiedingen.

Met [Azure site Recovery](site-recovery-overview.md)kunt u, als partners, herstel na nood gevallen rechtstreeks via CSP beheren. U kunt ook CSP gebruiken om Site Recovery omgevingen in te stellen, en klanten de mogelijkheid bieden om hun eigen herstel na nood gevallen op een selfservice manier te beheren. In beide scenario's zijn partners de samen werking tussen Site Recovery en hun klanten. Partners service de klant relatie en factureren klanten voor Site Recovery gebruik.

In dit artikel wordt beschreven hoe u als een partner Tenant abonnementen kan maken en beheren via CSP, voor een scenario met meerdere tenants voor VMware-replicatie.

## <a name="prerequisites"></a>Vereisten

Als u VMware-replicatie wilt instellen, moet u het volgende doen:

- [Voorbereiden](tutorial-prepare-azure.md) Azure-resources, waaronder een Azure-abonnement, een virtueel Azure-netwerk en een opslag account.
- On-premises VMware-servers en virtuele machines [voorbereiden](vmware-azure-tutorial-prepare-on-premises.md) .
- Maak voor elke Tenant een afzonderlijke beheer server die kan communiceren met de Tenant-Vm's en uw vCenter-servers. Alleen u als een partner moet toegangs rechten hebben voor deze beheer server. Meer informatie over [omgevingen met meerdere tenants](vmware-azure-multi-tenant-overview.md).

## <a name="create-a-tenant-account"></a>Een Tenant account maken

1. Meld u via het [micro soft Partner Center](https://partnercenter.microsoft.com/)aan bij uw CSP-account.
2. Selecteer **klanten** in het menu **dash board** .
3. Op de pagina die wordt geopend, klikt u op de knop **klant toevoegen** .
4. Vul op de pagina **nieuwe klant** de details van de account gegevens voor de Tenant in.

    ![De pagina account gegevens](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. Klik vervolgens op **volgende: abonnementen**.
6. Schakel op de pagina selectie abonnementen het selectie vakje **Microsoft Azure** in. U kunt nu of op elk gewenst moment andere abonnementen toevoegen.
7. Bevestig de details van de Tenant op de pagina **controleren** en klik vervolgens op **verzenden**.
8. Nadat u het Tenant account hebt gemaakt, wordt er een bevestigings pagina weer gegeven met daarin de details van het standaard account en het wacht woord voor dat abonnement. Sla de informatie op en wijzig zo nodig het wacht woord via de aanmeldings pagina van Azure Portal.

U kunt deze informatie met de Tenant delen als is, of u kunt zo nodig een afzonderlijke account maken en delen.

## <a name="access-the-tenant-account"></a>Toegang tot het Tenant account

U kunt het abonnement van de Tenant openen via het micro soft Partner Center-dash board.

1. Klik op de pagina **klanten** op de naam van het Tenant account.
2. Op de pagina **abonnementen** van het Tenant account kunt u de bestaande account abonnementen bewaken en zo nodig meer abonnementen toevoegen.
3. Als u de herstel bewerkingen voor nood gevallen van de Tenant wilt beheren, selecteert u **alle resources (Azure Portal)**. Hiermee krijgt u toegang tot de Azure-abonnementen van de Tenant.

    ![De koppeling alle resources](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. U kunt de toegang controleren door te klikken op de koppeling Azure Active Directory rechtsboven in de Azure Portal.

    ![Koppeling Azure Active Directory](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

U kunt nu alle Site Recovery bewerkingen voor de Tenant uitvoeren en beheren in de Azure Portal. Als u het Tenant abonnement wilt openen via CSP voor beheerde nood herstel, volgt u het eerder beschreven proces.

## <a name="assign-tenant-access-to-the-subscription"></a>Tenant toegang toewijzen aan het abonnement

1. Zorg ervoor dat de infra structuur voor herstel na nood gevallen is ingesteld. Partners hebben toegang tot Tenant abonnementen via de CSP-Portal, ongeacht of herstel na nood gevallen of self-service wordt beheerd. Stel de kluis in en registreer de infra structuur voor de Tenant abonnementen.
1. Geef de Tenant op met het [account dat u hebt gemaakt](#create-a-tenant-account).
1. U kunt als volgt een nieuwe gebruiker aan het Tenant abonnement toevoegen via de CSP-portal:

    1. Ga naar de pagina CSP-abonnement van de Tenant en selecteer vervolgens de optie **gebruikers en licenties** .

       ![De pagina CSP-abonnement van de Tenant](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    1. Maak nu een nieuwe gebruiker door de relevante gegevens in te voeren en machtigingen te selecteren, of door de lijst met gebruikers in een CSV-bestand te uploaden.

    1. Nadat u een nieuwe gebruiker hebt gemaakt, gaat u terug naar de Azure Portal. Selecteer op de pagina **abonnement** het relevante abonnement.

    1. Selecteer **toegangs beheer (IAM)** en klik vervolgens op **roltoewijzingen**.

    1. Klik op **roltoewijzing toevoegen** om een gebruiker toe te voegen met het relevante toegangs niveau. De gebruikers die zijn gemaakt via de CSP-Portal, worden weer gegeven op het tabblad roltoewijzingen.

        ![Een gebruiker toevoegen](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- Voor de meeste beheer bewerkingen is de rol *Inzender* voldoende. Gebruikers met dit toegangs niveau kunnen alles doen wat een abonnement heeft, met uitzonde ring van toegangs niveaus wijzigen (waarvoor toegang op *eigendoms* niveau is vereist).
- Site Recovery heeft ook drie [vooraf gedefinieerde gebruikers rollen](site-recovery-role-based-linked-access-control.md), die kunnen worden gebruikt om de toegangs niveaus verder te beperken als dat nodig is.

## <a name="multi-tenant-environments"></a>Omgevingen met meerdere tenants

Er zijn drie belang rijke multi tenant-modellen:

* **Shared Hosting Services provider (HSP)**: de partner is eigenaar van de fysieke infra structuur en maakt gebruik van gedeelde bronnen (vCenter, data centers, fysieke opslag, enzovoort) om meerdere Tenant-vm's te hosten op dezelfde infra structuur. De partner kan herstel na nood gevallen bieden als een beheerde service, of de Tenant kan eigenaar zijn van herstel na nood gevallen als self-service oplossing.

* **Toegewezen hosting services-provider**: de partner is eigenaar van de fysieke infra structuur, maar gebruikt toegewezen resources (meerdere vCenter, fysieke gegevens opslag, enzovoort) om de vm's van elke Tenant op een afzonderlijke infra structuur te hosten. De partner kan beheer voor herstel na nood geval bieden als een beheerde service, of de Tenant kan eigenaar zijn van het bedrijf als self-service oplossing.

* **MSP (Managed Services provider)**: de klant is eigenaar van de fysieke infra structuur die als host fungeert voor de vm's en de partner biedt ondersteuning voor herstel na nood gevallen en beheer.

Door Tenant abonnementen in te stellen, zoals beschreven in dit artikel, kunt u snel aan de slag gaan met het inschakelen van klanten in een van de relevante multi tenant-modellen. U vindt [hier](vmware-azure-multi-tenant-overview.md)meer informatie over de verschillende multi tenant-modellen en het inschakelen van on-premises toegangs beheer.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [toegangs beheer op basis van rollen (Azure RBAC) voor Azure](site-recovery-role-based-linked-access-control.md) om Azure site Recovery-implementaties te beheren.
- Meer informatie over de [replicatie architectuur](vmware-azure-architecture.md)van VMware naar Azure.
- [Bekijk de zelf studie voor het](vmware-azure-tutorial.md) repliceren van virtuele VMware-machines naar Azure.
Meer informatie over [omgevingen met meerdere tenants](vmware-azure-multi-tenant-overview.md) voor het repliceren van virtuele VMware-machines naar Azure.

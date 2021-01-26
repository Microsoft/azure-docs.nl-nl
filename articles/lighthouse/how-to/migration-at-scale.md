---
title: Migratie projecten op schaal beheren met Azure Migrate
description: Meer informatie over het effectief gebruiken van Azure Migrate op gedelegeerde klanten resources.
ms.date: 12/4/2020
ms.topic: how-to
ms.openlocfilehash: 53f7c390d9f16dcbccbb1d09f46e63fec13eee2d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788940"
---
# <a name="manage-migration-projects-at-scale-with-azure-migrate"></a>Migratie projecten op schaal beheren met Azure Migrate

Als service provider hebt u mogelijk meerdere tenants voor klanten in [Azure Lighthouse](../overview.md). Met Azure Lighthouse kunnen service providers bewerkingen tegelijk uitvoeren in meerdere Azure Active Directory (Azure AD)-tenants, waardoor beheer taken efficiënter worden.

[Azure migrate](../../migrate/migrate-services-overview.md) biedt een gecentraliseerde hub voor het beoordelen en migreren van on-premises servers, infra structuur, toepassingen en gegevens in Azure. Over het algemeen moeten partners die op schaal en migratie voor meerdere klanten worden uitgevoerd, toegang krijgen tot elk abonnement van een klant door gebruik te maken van het [abonnement model van de CSP (Cloud Solution Provider)](/partner-center/customers-revoke-admin-privileges) of door [een gast gebruiker in de Tenant van de klant in](../../active-directory/external-identities/what-is-b2b.md)te stellen.

Met Azure Lighthouse-integratie met Azure Migrate kunnen service providers werk belastingen voor verschillende klanten op schaal detecteren, beoordelen en migreren, terwijl klanten volledige zicht baarheid en controle kunnen hebben over hun omgevingen. Service providers hebben via Azure gedelegeerd resource beheer één weer gave van alle Azure Migrate projecten die ze beheren op meerdere tenants van klanten.

> [!NOTE]
> Via Azure Lighthouse kunnen partners detectie, evaluatie en migratie uitvoeren voor on-premises virtuele VMware-machines, Hyper-V-Vm's, fysieke servers en AWS/GCP-instanties. Er zijn twee opties voor de [migratie van VMware-vm's](../../migrate/server-migrate-overview.md). Op dit moment kan alleen de op agents gebaseerde migratie methode worden gebruikt bij het werken aan een migratie project in een gedelegeerd klant abonnement. migratie met replicatie zonder agent wordt momenteel niet ondersteund via gedelegeerde toegang tot het bereik van de klant.

In dit onderwerp vindt u een overzicht van hoe u [Azure migrate](../../migrate/migrate-services-overview.md) op een schaal bare manier kunt gebruiken.

> [!TIP]
> Hoewel we in dit onderwerp naar service providers en klanten verwijzen, is deze richt lijn ook van toepassing op [ondernemingen die Azure Lighthouse gebruiken om meerdere tenants te beheren](../concepts/enterprise.md).

Afhankelijk van uw scenario wilt u mogelijk de Azure Migrate maken in de Tenant van de klant of in uw Tenant beheren. Bekijk de onderstaande overwegingen en bepaal welk model het beste past bij de migratie behoeften van uw klant.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Een Azure Migrate-project maken in de Tenant van de klant

Een optie wanneer u Azure Lighthouse gebruikt, is het Azure Migrate-project maken in de Tenant van de klant. Gebruikers in de Tenant beheren kunnen vervolgens het abonnement van de klant selecteren bij het maken van een migratie project. Vanuit de beheer-Tenant kan de service provider de benodigde migratie bewerkingen uitvoeren. Dit kan omvatten het implementeren van het Azure Migrate apparaat om de werk belastingen te ontdekken, werk belastingen te beoordelen door Vm's te groeperen en Cloud kosten te berekenen, de gereedheid van de VM te evalueren en de migratie uit te voeren.

In dit scenario worden er geen resources gemaakt en opgeslagen in de beheer Tenant, zelfs als de stappen voor detectie en evaluatie kunnen worden gestart en uitgevoerd vanuit die Tenant. Alle resources, zoals migratie projecten, evaluatie rapporten voor on-premises workloads en gemigreerde resources op de doel bestemming, worden geïmplementeerd in het abonnement van de klant. De service provider heeft echter toegang tot alle klanten projecten vanuit hun eigen Tenant-en Portal-ervaring.

Deze aanpak minimaliseert de context overschakelen voor service providers die over meerdere klanten werken, terwijl klanten al hun resources in hun eigen tenants houden.

De werk stroom voor dit model ziet er ongeveer als volgt uit:

1. De klant is [onboarding van Azure Lighthouse](onboard-customer.md). De ingebouwde rol Inzender is vereist voor de identiteit die wordt gebruikt met Azure Migrate. Zie de voorbeeld sjabloon [gedelegeerde-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) voor een voor beeld met behulp van deze rol.
1. De aangewezen gebruiker meldt zich aan bij de beheer Tenant in de Azure Portal en gaat vervolgens naar Azure Migrate. Met deze gebruiker [maakt u een Azure migrate project](../../migrate/create-manage-projects.md)en selecteert u het juiste gedelegeerde klant abonnement.
1. De gebruiker [voert vervolgens stappen voor detectie en evaluatie uit](../../migrate/tutorial-discover-vmware.md).

   Voor virtuele VMware-machines kunt u, voordat u het apparaat configureert, de detectie beperken tot vCenter Server Data Centers, clusters, een map met clusters, hosts, een map van hosts of afzonderlijke Vm's. Als u het bereik wilt instellen, wijst u machtigingen toe voor het account dat het apparaat gebruikt voor toegang tot de vCenter Server. Dit is handig als de virtuele machines van meerdere klanten worden gehost op de Hyper Visor. U kunt het detectie bereik van Hyper-V niet beperken.

    > [!NOTE]
    > U kunt virtuele VMware-machines detecteren en beoordelen voor migratie met behulp van Azure Migrate via de gedelegeerde toegang van Azure Lighthouse. Voor de migratie van virtuele VMware-machines wordt op dit moment alleen de op agents gebaseerde methode ondersteund bij het werken aan een migratie project in een gedelegeerd klant abonnement.

1. Wanneer het abonnement van de doel klant gereed is, gaat u verder met de migratie via de toegang die wordt verleend door Azure Lighthouse. Het migratie project met evaluatie resultaten en gemigreerde resources wordt gemaakt in de Tenant van de klant onder het doel abonnement.

> [!TIP]
> Voorafgaand aan de migratie moet een landings zone worden geïmplementeerd voor het inrichten van de Foundation-infrastructuur resources en het voorbereiden van het abonnement op de virtuele machines die worden gemigreerd. Voor toegang tot of het maken van resources in deze landings zone is de ingebouwde rol van de eigenaar mogelijk vereist, die momenteel niet wordt ondersteund in azure Lighthouse. Voor dergelijke scenario's moet de klant mogelijk de rol gast toegang bieden of de toegang tot de beheerder overdragen via het CSP-abonnements model. Zie voor een benadering voor het maken van multi tenant-aanvoer zones de [demo oplossing multi tenant-aanvoer zones](https://github.com/Azure/Multi-tenant-Landing-Zones) op github.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Een Azure Migrate-project maken in de Tenant beheren

In dit scenario worden migratie-gerelateerde bewerkingen, zoals detectie en evaluatie, nog steeds uitgevoerd door gebruikers in de Tenant beheren. Het migratie project en alle relevante resources bevinden zich echter in de partner-Tenant en de klant heeft geen rechtstreekse zicht baarheid in het project (hoewel evaluaties kunnen worden gedeeld met klanten indien gewenst). Het migratie doel voor elke klant is het abonnement van de klant.

Met deze aanpak kunnen service providers snel migratie-en evaluatie projecten starten, die de eerste stappen van klanten abonnementen en tenants samen vattingen.

De werk stroom voor dit model ziet er ongeveer als volgt uit:

1. De klant is [onboarding van Azure Lighthouse](onboard-customer.md). De ingebouwde rol Inzender is vereist voor de identiteit die wordt gebruikt met Azure Migrate. Zie de voorbeeld sjabloon [gedelegeerde-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) voor een voor beeld met behulp van deze rol.
1. De aangewezen gebruiker meldt zich aan bij de beheer Tenant in de Azure Portal en gaat vervolgens naar Azure Migrate. Deze gebruiker [maakt een Azure migrate project](../../migrate/create-manage-projects.md) in een abonnement dat behoort tot de beheer-Tenant.
1. De gebruiker [voert vervolgens stappen voor detectie en evaluatie uit](../../migrate/tutorial-discover-vmware.md). De on-premises Vm's worden gedetecteerd en beoordeeld binnen het migratie project dat in de beheer Tenant is gemaakt en vervolgens van daaruit gemigreerd.

   Als u meerdere klanten beheert op dezelfde Hyper-V-host, kunt u alle werk belastingen tegelijk ontdekken. Klantspecifieke Vm's kunnen in dezelfde groep worden geselecteerd, een evaluatie kan worden gemaakt en de migratie kan worden uitgevoerd door het juiste abonnement van de klant te selecteren als doel bestemming. U hoeft het detectie bereik niet te beperken en u kunt een volledig overzicht van alle werk belastingen van de klant in één migratie project onderhouden.

1. Als u klaar bent, gaat u verder met de migratie door het gedelegeerte klant abonnement te selecteren als doel doel voor het repliceren en migreren van de werk belastingen. De nieuwe resources bestaan in het abonnement van de klant, terwijl de evaluatie gegevens en resources die deel uitmaken van het migratie project, in de Tenant beheren blijven.

Opmerking: u moet het parameter bestand wijzigen zodat het overeenkomt met uw omgeving voordat u implementeert. https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>Partner herkenning voor klant migraties

Als lid van de [Microsoft Partner Network](https://partner.microsoft.com)kunt u uw partner-id koppelen aan de referenties die worden gebruikt om overgedragen klanten resources te beheren. Met de partner beheerder-koppeling (PAL) kan micro soft invloed hebben op de gevolgen en Azure-omzet die is verbruikt voor uw organisatie, op basis van de taken die u uitvoert voor klanten, inclusief migratie projecten.

Zie [Uw partner-id koppelen om de impact op gedelegeerde resources te volgen](partner-earned-credit.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure migrate](../../migrate/migrate-services-overview.md).
- Meer informatie over [beheerervaring in meerdere tenants](../concepts/cross-tenant-management-experience.md).
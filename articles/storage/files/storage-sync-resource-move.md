---
title: Azure File Sync resource verplaatsen en topologie wijzigingen
description: Meer informatie over het verplaatsen van synchronisatie resources over resource groepen, abonnementen en Azure Active Directory AAD-tenants.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555355"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Azure File Sync resources verplaatsen naar een andere resource groep, een abonnement of een AAD-Tenant

In dit artikel wordt beschreven hoe u wijzigingen aanbrengt in de Tenant van de resource groep, het abonnement of de Azure Active Directory (AAD) voor uw Azure File Sync cloud resources en Azure Storage-accounts.

Wanneer u van plan bent om wijzigingen aan te brengen in de Azure File Sync cloud resources, is het belang rijk dat u de opslag resources op hetzelfde moment overweegt. De volgende resources bestaan:

**Azure File Sync resources (in hiërarchische volg orde)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Opslag synchronisatie service
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Geregistreerde server
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Synchronisatie groep
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Cloud-eind punt
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Server eindpunt

In Azure File Sync is de resource van de opslag synchronisatie service de enige resource die kan worden verplaatst. Alle subbronnen zijn gebonden aan het bovenliggende knoop punt en kunnen niet worden verplaatst naar een andere opslag synchronisatie service.

**Azure storage-resources (in hiërarchische volg orde)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Opslag account
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Bestands share

In Azure Storage is de enige resource die kan worden verplaatst het opslag account. Een Azure-bestands share, als een subresource, kan niet worden verplaatst naar een ander opslag account.

## <a name="supported-combinations"></a>Ondersteunde combi Naties

Bij het plannen van een resource verplaatsing, opslag account en de Azure File Sync resource op het hoogste niveau, de *opslag synchronisatie service*, moet samen worden beschouwd.

Als best practice zijn de opslag synchronisatie service en de opslag accounts die bestands shares synchroniseren, altijd opgeslagen in hetzelfde abonnement. Deze combi Naties worden ondersteund:

* Opslag synchronisatie service en opslag accounts bevinden zich in **verschillende resource groepen** (dezelfde Azure-Tenant)
* Opslag synchronisatie service en opslag accounts bevinden zich in **verschillende abonnementen** (dezelfde Azure-Tenant)

> [!IMPORTANT]
> Via verschillende combi Naties van verplaatsen kunnen een opslag synchronisatie service en opslag accounts worden beëindigd in verschillende abonnementen, afhankelijk van verschillende AAD-tenants. De synchronisatie lijkt zelfs te werken, maar dit is geen ondersteunde configuratie. De synchronisatie kan in de toekomst worden gestopt zonder dat er een werk voorwaarde meer kan worden weer gegeven.

Bij het plannen van het verplaatsen van resources, zijn er verschillende aandachtspunten voor [verplaatsen binnen dezelfde Aad-Tenant](#move-within-the-same-azure-active-directory-tenant) en verplaatsen [naar een andere Aad-Tenant](#move-to-a-new-azure-active-directory-tenant). Wanneer u AAD-tenants verplaatst, moet u de synchronisatie en opslag resources altijd samen verplaatsen.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Verplaatsen binnen dezelfde Azure Active Directory Tenant

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Een afbeelding met de Azure Portal voor een opslag synchronisatie service resource, met de opdracht verplaatsen uitgevouwen. Hier worden de opties voor verplaatsen en abonnements verplaatsen van de resource groep weer gegeven." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Een handige manier om een resource voor opslag synchronisatie service te verplaatsen, is door de Azure Portal te gebruiken. Navigeer naar de opslag synchronisatie service die u wilt verplaatsen en selecteer **verplaatsen** vanaf de opdracht balk. Dezelfde stappen zijn ook van toepassing op het verplaatsen van een opslag account. U kunt ook alle resources in een resource groep op deze manier verplaatsen. Het is raadzaam om een volledige resource groep te verplaatsen wanneer u de opslag synchronisatie service en alle gebruikte opslag accounts in deze resource groep hebt gebruikt.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Wanneer u een opslag account resource verplaatst, wordt de synchronisatie onmiddellijk gestopt. U moet de synchronisatie hand matig autoriseren om toegang te krijgen tot de relevante opslag accounts in het nieuwe abonnement. De sectie [Azure file sync autorisatie voor opslag toegang](#azure-file-sync-storage-access-authorization) bevat de benodigde stappen.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Verplaatsen naar een nieuwe Azure Active Directory Tenant

Individuele resources zoals een opslag synchronisatie service of opslag accounts kunnen niet door zichzelf worden verplaatst naar een andere AAD-Tenant. Alleen Azure-abonnementen kunnen AAD-tenants verplaatsen. Denk na over de structuur van uw abonnement in de nieuwe AAD-Tenant. U kunt een speciaal abonnement gebruiken voor Azure File Sync. 

1. Maak een Azure-abonnement (of bepaal een bestaande naam in de oude Tenant die moet worden verplaatst.
1. [Voer een abonnements verplaatsing uit binnen dezelfde Aad-Tenant](#move-within-the-same-azure-active-directory-tenant) van uw opslag synchronisatie service en alle gekoppelde opslag accounts.
1. De synchronisatie wordt gestopt. Voltooi de Tenant [om de opslag accounts die zijn verplaatst onmiddellijk te openen of de synchronisatie te herstellen](#azure-file-sync-storage-access-authorization). U kunt later naar de nieuwe AAD-Tenant gaan.

Zodra alle gerelateerde Azure File Sync resources zijn sequestered in hun eigen abonnement, bent u klaar om het hele abonnement te verplaatsen naar de AAD-doel-Tenant. In de [hand leiding voor overdrachts abonnementen](../../role-based-access-control/transfer-subscription.md) kunt u een dergelijke overdracht plannen en uitvoeren.

> [!WARNING]
> Wanneer u een abonnement van de ene Tenant naar de andere overbrengt, wordt de synchronisatie onmiddellijk gestopt. U moet de synchronisatie hand matig autoriseren om toegang te krijgen tot de relevante opslag accounts in het nieuwe abonnement. De sectie [Azure file sync autorisatie voor opslag toegang](#azure-file-sync-storage-access-authorization) bevat de benodigde stappen.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Een afbeelding met de Azure Portal, Blade overzicht van abonnementen, waarbij u de werkbalk opdracht map wijzigen in het midden boven aan de pagina markeert." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        U bent klaar om de migratie te starten wanneer u een plan en de vereiste machtigingen hebt:
        1. Ga in het Azure Portal naar uw abonnement op de Blade **overzicht** .
        1. Selecteer **Map wijzigen**
        1. Volg de stappen in de wizard om de nieuwe AAD-Tenant toe te wijzen.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Toegangs autorisatie voor opslag Azure File Sync

Wanneer opslag accounts worden verplaatst naar een nieuw abonnement of worden verplaatst binnen een abonnement op een nieuwe Azure Active Directory (AAD)-Tenant, wordt de synchronisatie gestopt. Op rollen gebaseerde toegang (RBAC) wordt gebruikt voor het autoriseren van Azure File Sync voor toegang tot een opslag account en deze roltoewijzingen worden niet gemigreerd met de resources.

### <a name="azure-file-sync-service-principal"></a>Service-Principal Azure File Sync

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Een afbeelding met de Azure Portal, abonnements beheer, geregistreerde resource providers." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        De Azure File Sync Service-Principal moet in uw AAD-Tenant bestaan voordat u synchronisatie toegang tot een opslag account kunt autoriseren. </br></br> Wanneer u vandaag een nieuw Azure-abonnement maakt, wordt de Azure File Sync resource provider *micro soft. StorageSync* automatisch geregistreerd bij uw abonnement. Bij de registratie van de resource provider wordt een *Service-Principal* voor synchronisatie beschikbaar gemaakt in de Azure Active Directory Tenant die het abonnement bepaalt. Een Service-Principal is vergelijkbaar met een gebruikers account in uw AAD. U kunt de Azure File Sync Service-Principal gebruiken om toegang te verlenen tot bronnen via op rollen gebaseerd toegangs beheer (RBAC). De enige resource synchronisatie moet toegang hebben tot de opslag accounts die de bestands shares bevatten die moeten worden gesynchroniseerd. *Micro soft. StorageSync* moet zijn toegewezen aan de ingebouwde functie **lezer en gegevens toegang** voor het opslag account. </br></br> Deze toewijzing wordt automatisch uitgevoerd via de gebruikers context van de aangemelde gebruiker wanneer u een bestands share aan een synchronisatie groep toevoegt, of met andere woorden, u maakt een eind punt in de Cloud. Wanneer een opslag account wordt verplaatst naar een nieuw abonnement of een AAD-Tenant, gaat deze roltoewijzing verloren en [moet deze hand matig opnieuw worden ingesteld](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Als het Azure-doel abonnement niet recent is gemaakt, controleert u of de resource provider *micro soft. StorageSync* is geregistreerd bij het abonnement. Als dat niet het geval is, voegt u dit hand matig toe op dezelfde Blade van de portal.

### <a name="establish-sync-access-to-a-storage-account"></a>Synchronisatie toegang tot een opslag account tot stand brengen

De [Azure File Sync Service-Principal](#azure-file-sync-service-principal) moet worden gebruikt om toegang te verlenen tot een opslag account via op rollen gebaseerd toegangs beheer (RBAC). *Micro soft. StorageSync* moet zijn toegewezen aan de ingebouwde functie **lezer en gegevens toegang** voor het opslag account. 

Deze toewijzing wordt doorgaans automatisch uitgevoerd via de gebruikers context van de aangemelde gebruiker wanneer u een bestands share aan een synchronisatie groep toevoegt, of met andere woorden, u maakt een eind punt in de Cloud. Als een opslag account echter wordt verplaatst naar een nieuw abonnement of een AAD-Tenant, gaat deze roltoewijzing verloren en moet deze hand matig opnieuw worden ingesteld.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Een afbeelding van de Service-Principal micro soft. StorageSync die is toegewezen aan de rol lezer en gegevens toegang voor een opslag account":::
    :::column-end:::
    :::column:::
        Ga in het Azure Portal naar het opslag account dat u nodig hebt om synchronisatie toegang tot te verlenen. <ol><li>Selecteer **toegangs beheer (IAM)** in de linkerkolom.</li><li>Selecteer het tabblad roltoewijzingen voor de lijst met gebruikers en toepassingen (Service-principals) die toegang hebben tot uw opslag account.</li><li>Selecteer **Toevoegen**</li><li>Selecteer in het **veld rol** de optie **lezer en gegevens toegang**.</li><li>Typ *micro soft. StorageSync* in het **veld selecteren**, selecteer de rol en klik op **Opslaan**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Verplaatsen naar een andere Azure-regio

De Azure File Sync resource *Storage-synchronisatie service* en de opslag accounts die bestands shares bevatten die worden gesynchroniseerd, hebben een Azure-regio waarin ze worden geïmplementeerd. U bepaalt dat gebied wanneer u een resource maakt. De regio van de opslag synchronisatie service en de resources van het opslag account moeten overeenkomen. Deze regio's kunnen niet worden gewijzigd in een resource type nadat ze zijn gemaakt.

Het toewijzen van een andere regio aan een resource verschilt van een failover van een [regio](#region-fail-over), die kan worden ondersteund, afhankelijk van de instelling van de redundantie van uw opslag account.

## <a name="region-fail-over"></a>Failover van regio

[Azure Storage biedt opties voor geo-redundantie](../common/storage-redundancy.md#geo-redundant-storage) voor een opslag account. Deze redundantie opties kunnen problemen opleveren voor opslag accounts die worden gebruikt met Azure File Sync. De belangrijkste reden is dat replicatie tussen geografisch onbereikbare regio's niet wordt uitgevoerd door Azure File Sync, maar door een opslag replicatie technologie die is ingebouwd in het opslag subsysteem in Azure. Het is niet mogelijk om te weten wat de status van een toepassing is en Azure File Sync is een toepassing met bestanden die worden gesynchroniseerd van en naar Azure-bestands shares op een bepaald moment. Als u kiest voor een van deze geografische opslag redundantie opties, verliest u niet al uw gegevens in een grootschalige nood geval. U moet echter wel [gegevens verlies verwachten](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> Failover is nooit een geschikte vervanging voor het inrichten van uw resources in de juiste Azure-regio. Als uw resources zich in de regio ' mis ' bevinden, moet u overwegen om synchronisatie te stoppen en opnieuw te synchroniseren naar nieuwe Azure-bestands shares die in uw gewenste regio worden geïmplementeerd.

Een regionale failover kan door micro soft worden gestart in een onherstelbare gebeurtenis waardoor data centers gedurende lange tijd in een Azure-regio incapacitated worden weer gegeven. De definitie van uitval tijd die uw bedrijf kan oplopen, kan korter zijn dan de tijd die micro soft heeft voor bereid om door te gaan voordat een regionale failover wordt gestart. Voor een situatie zoals dat [kan, kunnen failovers ook worden geïnitieerd door klanten](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> In het geval van een failover moet u een ondersteunings ticket indienen voor uw betrokken opslag synchronisatie Services zodat synchronisatie opnieuw kan worden uitgevoerd.

## <a name="see-also"></a>Zie ook

- [Overzicht van de Azure-bestands share en synchronisatie migratie handleidingen](storage-files-migration-overview.md)
- [Problemen oplossen met Azure File Sync](storage-sync-files-troubleshoot.md)
- [Een Azure File Sync-implementatie plannen](storage-sync-files-planning.md)

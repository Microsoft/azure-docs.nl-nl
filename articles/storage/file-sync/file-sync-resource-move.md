---
title: Azure File Sync resource verplaatst en topologiewijzigingen
description: Meer informatie over het verplaatsen van synchronisatieresources tussen resourcegroepen, abonnementen en Azure Active Directory (AAD)-tenants.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796780"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Resources Azure File Sync verplaatsen naar een andere resourcegroep, abonnement of AAD-tenant

In dit artikel wordt beschreven hoe u wijzigingen aan de resourcegroep, het abonnement of de AAD-tenant (Azure Active Directory) kunt aanbrengen voor uw Azure File Sync cloudresources en Azure-opslagaccounts.

Wanneer u van plan bent om wijzigingen aan Azure File Sync cloudbronnen aan te brengen, is het belangrijk om tegelijkertijd rekening te houden met de opslagbronnen. De volgende resources bestaan:

**Azure File Sync resources (in hiërarchische volgorde)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Opslagsynchronisatieservice
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Geregistreerde server
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Synchronisatiegroep
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Cloud-eindpunt
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Server-eindpunt

In Azure File Sync is de opslagsynchronisatieserviceresource de enige resource die kan worden verplaatst. Alle subresources zijn gebonden aan het bovenliggende subresources en kunnen niet worden verplaatst naar een andere opslagsynchronisatieservice.

**Azure-opslagbronnen (in hiërarchische volgorde)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Opslagaccount
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Bestands share

In Azure Storage is het opslagaccount de enige resource die kan worden verplaatst. Een Azure-bestands share kan, als subresource, niet worden verplaatst naar een ander opslagaccount.

## <a name="supported-combinations"></a>Ondersteunde combinaties

Bij het plannen van een resourceverhuis, moeten het opslagaccount en de Azure File Sync resource, de opslagsynchronisatieservice *genoemd,* samen worden overwogen.

Als best practice moeten de opslagsynchronisatieservice en de opslagaccounts met synchronisatie van bestands shares zich altijd in hetzelfde abonnement bevinden. Deze combinaties worden ondersteund:

* Opslagsynchronisatieservice en opslagaccounts bevinden zich in **verschillende resourcegroepen** (dezelfde Azure-tenant)
* Opslagsynchronisatieservice en opslagaccounts bevinden zich in **verschillende abonnementen** (dezelfde Azure-tenant)

> [!IMPORTANT]
> Door verschillende combinaties van stappen kunnen een opslagsynchronisatieservice en opslagaccounts in verschillende abonnementen eindigen, die worden beheerd door verschillende AAD-tenants. Synchronisatie lijkt zelfs te werken, maar dit is geen ondersteunde configuratie. Synchronisatie kan in de toekomst worden gestopt zonder dat er een werkvoorwaarde kan worden aan gewerkt.

Bij het plannen van de overstap naar een resource zijn er verschillende overwegingen om binnen dezelfde [AAD-tenant](#move-within-the-same-azure-active-directory-tenant) over te gaan naar een [andere AAD-tenant.](#move-to-a-new-azure-active-directory-tenant) Wanneer u AAD-tenants verplaatst, moet u synchronisatie- en opslagbronnen altijd bij elkaar verplaatsen.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Verplaatsen binnen dezelfde Azure Active Directory tenant

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Een afbeelding met de Azure Portal voor een opslagsynchronisatieserviceresource, met de opdracht Verplaatsen uitver vouwt. U ziet de opties voor het verplaatsen van de resourcegroep en het verplaatsen van abonnementen." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Een handige manier om een opslagsynchronisatieserviceresource te verplaatsen, is met de Azure Portal. Navigeer naar de opslagsynchronisatieservice die u wilt verplaatsen en selecteer **Verplaatsen** in de opdrachtbalk. Dezelfde stappen zijn van toepassing op het verplaatsen van een opslagaccount. U kunt ook alle resources in een resourcegroep op deze manier verplaatsen. Het verplaatsen van een volledige resourcegroep wordt aanbevolen wanneer u de opslagsynchronisatieservice en alle gebruikte opslagaccounts in deze resourcegroep hebt.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Wanneer u een opslagaccountresource verplaatst, wordt de synchronisatie onmiddellijk gestopt. U moet synchronisatie handmatig autorisatie verlenen voor toegang tot de relevante opslagaccounts in het nieuwe abonnement. De [Azure File Sync toegangsautorisatie](#azure-file-sync-storage-access-authorization) voor opslag biedt de benodigde stappen.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Verplaatsen naar een nieuwe Azure Active Directory-tenant

Afzonderlijke resources, zoals een opslagsynchronisatieservice of opslagaccounts, kunnen niet op zichzelf worden verplaatst naar een andere AAD-tenant. Alleen Azure-abonnementen kunnen AAD-tenants verplaatsen. Denk na over uw abonnementsstructuur in de nieuwe AAD-tenant. U kunt een toegewezen abonnement gebruiken voor Azure File Sync. 

1. Maak een Azure-abonnement (of bepaal een bestaand abonnement in de oude tenant dat moet worden verplaatst).
1. [Een abonnement verplaatsen binnen dezelfde AAD-tenant](#move-within-the-same-azure-active-directory-tenant) van uw opslagsynchronisatieservice en alle bijbehorende opslagaccounts.
1. Synchronisatie wordt gestopt. Voltooi de tenant verplaatsen onmiddellijk of [herstel de synchronisatie-mogelijkheid om toegang te krijgen tot de opslagaccounts die zijn verplaatst.](#azure-file-sync-storage-access-authorization) U kunt vervolgens later naar de nieuwe AAD-tenant gaan.

Zodra alle gerelateerde Azure File Sync resources in hun eigen abonnement zijn opgevraagd, bent u klaar om het hele abonnement naar de AAD-doelten tenant te verplaatsen. Met [de handleiding voor het overdragen](../../role-based-access-control/transfer-subscription.md) van abonnementen kunt u een dergelijke overdracht plannen en uitvoeren.

> [!WARNING]
> Wanneer u een abonnement overdraagt van de ene tenant naar de andere, wordt de synchronisatie onmiddellijk gestopt. U moet synchronisatie handmatig autorisatie geven voor toegang tot de relevante opslagaccounts in het nieuwe abonnement. De [Azure File Sync toegangsautorisatie](#azure-file-sync-storage-access-authorization) voor opslag biedt de benodigde stappen.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Een afbeelding van de Azure Portal blade Abonnementsoverzicht, met de opdracht Mapwerkbalk wijzigen in het midden, boven aan de pagina." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        U bent klaar om de migratie te starten zodra u een plan en de vereiste machtigingen hebt:
        1. Navigeer Azure Portal de blade Overzicht naar uw **abonnement.**
        1. Selecteer **Map wijzigen**
        1. Volg de wizardstappen om de nieuwe AAD-tenant toe te wijzen.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure File Sync toegangsautorisatie voor opslag

Wanneer opslagaccounts worden verplaatst naar een nieuw abonnement of binnen een abonnement worden verplaatst naar een nieuwe Azure Active Directory(AAD)-tenant, wordt de synchronisatie gestopt. Op rollen gebaseerde toegang (RBAC) wordt gebruikt om Azure File Sync toegang te verlenen tot een opslagaccount en deze roltoewijzingen worden niet gemigreerd met de resources.

### <a name="azure-file-sync-service-principal"></a>Azure File Sync service-principal

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Een afbeelding met de Azure Portal, abonnementsbeheer, geregistreerde resourceproviders." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        De Azure File Sync service-principal moet bestaan in uw AAD-tenant voordat u synchronisatietoegang tot een opslagaccount kunt autorisatie verlenen. </br></br> Wanneer u vandaag nog een nieuw Azure-abonnement maakt, wordt Azure File Sync resourceprovider *Microsoft.StorageSync* automatisch geregistreerd bij uw abonnement. Registratie van de resourceprovider maakt een *service-principal* voor synchronisatie beschikbaar in de Azure Active Directory tenant die het abonnement bepaalt. Een service-principal is vergelijkbaar met een gebruikersaccount in uw AAD. U kunt de service Azure File Sync-principal gebruiken om toegang tot resources via op rollen gebaseerd toegangsbeheer (RBAC) te verlenen. De enige resourcesynchronisatie heeft toegang nodig tot uw opslagaccounts met de bestands shares die moeten worden gesynchroniseerd. *Microsoft.StorageSync moet* worden toegewezen aan de ingebouwde rol Lezer en **Gegevenstoegang** in het opslagaccount. </br></br> Deze toewijzing wordt automatisch uitgevoerd via de gebruikerscontext van de aangemelde gebruiker wanneer u een bestands share toevoegt aan een synchronisatiegroep, of met andere woorden, u maakt een cloud-eindpunt. Wanneer een opslagaccount wordt verplaatst naar een nieuw abonnement of een AAD-tenant, gaat deze roltoewijzing verloren en moet deze handmatig [opnieuw worden gepubliceerd.](#establish-sync-access-to-a-storage-account)
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Als het Azure-doelabonnement niet onlangs is gemaakt, controleert u of de resourceprovider *Microsoft.StorageSync* is geregistreerd bij het abonnement. Als dat niet het probleem is, voegt u het handmatig toe op dezelfde portalblade.

### <a name="establish-sync-access-to-a-storage-account"></a>Synchronisatietoegang tot een opslagaccount tot stand maken

De [Azure File Sync service-principal](#azure-file-sync-service-principal) moet worden gebruikt om toegang tot een opslagaccount via op rollen gebaseerd toegangsbeheer (RBAC) toe te staan. *Microsoft.StorageSync* moet worden toegewezen aan de ingebouwde rol Lezer en **Gegevenstoegang** voor het opslagaccount. 

Deze toewijzing wordt doorgaans automatisch uitgevoerd via de gebruikerscontext van de aangemelde gebruiker wanneer u een bestands share toevoegt aan een synchronisatiegroep, of met andere woorden, u maakt een cloud-eindpunt. Wanneer een opslagaccount echter wordt verplaatst naar een nieuw abonnement of een nieuwe AAD-tenant, gaat deze roltoewijzing verloren en moet deze handmatig opnieuw worden gepubliceerd.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Een afbeelding met de service-principal Microsoft.StorageSync die is toegewezen aan de rol Lezer en Gegevenstoegang in een opslagaccount":::
    :::column-end:::
    :::column:::
        Navigeer Azure Portal naar het opslagaccount waar u de synchronisatietoegang opnieuw moet autoriseren. <ol><li>Selecteer **Toegangsbeheer (IAM)** in de inhoudsopgave aan de linkerkant.</li><li>Selecteer het tabblad Roltoewijzingen om de gebruikers en toepassingen (service-principals) weer te geven die toegang hebben tot uw opslagaccount.</li><li>Selecteer **Toevoegen**</li><li>Selecteer in **het veld Rol** de optie Lezer en **Gegevenstoegang.**</li><li>Typ *microsoft.StorageSync* **in** het veld Selecteren, selecteer de rol en klik op **Opslaan.**</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Verplaatsen naar een andere Azure-regio

De Azure File Sync  opslagsynchronisatieservice en de opslagaccounts die bestands shares bevatten die worden gesynchroniseerd, hebben een Azure-regio waarin ze zijn geïmplementeerd. U bepaalt die regio wanneer u een resource maakt. De regio van de opslagsynchronisatieservice en opslagaccountbronnen moeten overeenkomen. Deze regio's kunnen na het maken van een resourcetype niet meer worden gewijzigd.

Het toewijzen van een andere regio aan een resource verschilt van een [regio-fail-over](#region-fail-over)die kan worden ondersteund, afhankelijk van de redundantie-instelling van uw opslagaccount.

## <a name="region-fail-over"></a>Regio-fail-over

[Azure Storage biedt opties voor geo-redundantie](../common/storage-redundancy.md#geo-redundant-storage) voor een opslagaccount. Deze redundantieopties kunnen problemen opleveren voor opslagaccounts die worden gebruikt met Azure File Sync. De belangrijkste reden is dat replicatie tussen geografisch verafgelegen regio's niet wordt uitgevoerd door Azure File Sync, maar door een opslagreplicatietechnologie die is ingebouwd in het opslagsubsysteem in Azure. Het kan geen kennis hebben van de toepassingstoestand en Azure File Sync is een toepassing met bestanden die op een bepaald moment worden gesynchroniseerd naar en van Azure-bestands shares. Als u kiest voor een van deze geografisch verspreide opties voor opslag redundantie, verliest u niet al uw gegevens bij een grootschalige ramp. U moet echter anticiperen [op gegevensverlies.](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)

> [!CAUTION]
> Fail-over is nooit een geschikte vervanging voor het inrichten van uw resources in de juiste Azure-regio. Als uw resources zich in de 'verkeerde' regio hebben, moet u overwegen om de synchronisatie te stoppen en de synchronisatie opnieuw in te stellen op nieuwe Azure-bestands shares die zijn geïmplementeerd in de gewenste regio.

Een regionale failover kan door Microsoft worden gestart in een onherstelbare gebeurtenis die datacenters in een Azure-regio gedurende langere tijd inkapsgeschikt zal maken. De definitie van downtime die uw bedrijf kan doorlaten, is mogelijk minder dan de tijd die Microsoft bereid is om door te geven voordat een regionale fail-over wordt start. In een situatie als deze kunnen [fail-overs ook worden gestart door klanten](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> In het geval van een fail-over moet u een ondersteuningsticket indienen voor uw beïnvloede opslagsynchronisatieservices om de synchronisatie weer te laten werken.

## <a name="see-also"></a>Zie ook

- [Overzicht van migratiehandleidingen voor Azure-bestands share en synchronisatie](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Problemen oplossen met Azure File Sync](file-sync-troubleshoot.md)
- [Planning voor een Azure Files Sync-implementatie](file-sync-planning.md)
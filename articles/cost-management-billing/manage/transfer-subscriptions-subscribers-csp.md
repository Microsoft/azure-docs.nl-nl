---
title: Overdracht van Azure-abonnementen tussen abonnees en CSP's
description: Lees hoe u Azure-abonnementen kunt overdragen tussen abonnees en CSP's.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/11/2021
ms.author: banders
ms.openlocfilehash: 63fbf76b2211e530707f3598d176b646c317cc53
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363046"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Overdracht van Azure-abonnementen tussen abonnees en CSP's

Dit artikel bevat de algemene stappen voor het overdragen van Azure-abonnementen van en naar CSP-partners (Cloud Solution Provider) en hun klanten. Deze informatie is bedoeld om de Azure-abonnee te helpen bij het coördineren met een partner. Informatie die Microsoft-partners gebruiken voor het overdrachtsproces wordt beschreven in [Learn how to transfer a customer's Azure subscriptions to another partner](/partner-center/switch-azure-subscriptions-to-a-different-partner) (Azure-abonnementen van een klant overdragen aan een andere partner).

Voordat u een aanvraag voor een overdracht begint, moet u eventuele kosten- en factureringsgegevens downloaden of exporteren die u wilt blijven gebruiken. Facturerings- en gebruiksgegevens worden niet overgedragen met het abonnement. Zie [Geëxporteerde gegevens maken en beheren](../costs/tutorial-export-acm-data.md) voor meer informatie over het exporteren van gegevens over kostenbeheer. Zie [Uw Azure-factuurgegevens en dagelijkse gebruiksgegevens downloaden of weergeven](download-azure-invoice-daily-usage-date.md) voor meer informatie over het downloaden van uw factuur- en gebruiksgegevens.

Als u bestaande reserveringen hebt, worden ze niet meer toegepast nadat u een abonnement hebt overgedragen. Zorg ervoor dat u [alle reserveringen annuleert en terugbetaalt](../reservations/exchange-and-refund-azure-reservations.md) voordat u een abonnement overdraagt.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>EA-abonnementen overdragen naar een CSP-partner

CSP-partners voor directe facturering die zijn gecertificeerd als een [Azure Expert MSP (Managed Services provider)](https://partner.microsoft.com/membership/azure-expert-msp) kunnen een aanvraag indienen voor de overdracht van Azure-abonnementen voor hun klanten die een directe EA (Enterprise Agreement) hebben. Abonnementsoverdrachten zijn alleen toegestaan voor klanten die een MCA (Microsoft-klantovereenkomst) hebben geaccepteerd en een Azure-abonnement met het CSP-programma hebben aangeschaft.

Wanneer de aanvraag is goedgekeurd, kan de CSP een gecombineerde factuur aanbieden aan klanten. Zie [Eigendom van facturering van Azure-abonnementen krijgen voor uw MPA-account](mpa-request-ownership.md) voor meer informatie over CSP's die abonnementen willen overdragen.

>[!IMPORTANT]
> Nadat een EA-abonnement is overgedragen naar een CSP-partner, worden alle quota die eerder zijn toegepast op het EA-abonnement, opnieuw ingesteld op de standaardwaarde. Als er na de overdracht van het abonnement een hoger quotum vereist is, laat u uw CSP-provider een aanvraag voor een [quotumverhoging](../../azure-portal/supportability/regional-quota-requests.md) indienen. 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Andere abonnementsoverdrachten naar een CSP-partner

Als u andere Azure-abonnementen wilt overdragen naar een CSP-partner, moet de abonnee resources van bronabonnementen verplaatsen naar CSP-abonnementen. Gebruik de volgende richtlijnen voor het verplaatsen van resources tussen abonnementen.

1. Stel een [reseller-relatie](/partner-center/request-a-relationship-with-a-customer) in met de klant. Bekijk het [regionale autorisatie overzicht van CSP](/partner-center/regional-authorization-overview) om ervoor te zorgen dat zowel de Tenant van de klant als de partner zich binnen dezelfde gemachtigde regio's bevindt.
1. Werk samen met uw CSP-partner om Azure CSP-abonnementen te maken op de doellocatie.
1. Zorg ervoor dat de bron- en doelabonnementen van de CSP zich in dezelfde Azure Active Directory-tenant (Azure AD) bevinden.  
    U kunt de Azure AD-tenant voor een Azure CSP-abonnement niet wijzigen. In plaats daarvan moet u het bronabonnement toevoegen of koppelen aan de CSP Azure AD-tenant. Zie [Een Azure-abonnement aan uw Azure Active Directory-tenant toevoegen of koppelen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) voor meer informatie.
    > [!IMPORTANT]
    > - Wanneer u een abonnement aan een andere Azure Active Directory koppelt, gaat de toegang verloren voor gebruikers aan wie rollen zijn toegewezen met [Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC](../../role-based-access-control/role-assignments-portal.md)). Klassieke abonnementsbeheerders, waaronder de servicebeheerder en co-beheerders, verliezen ook hun toegang.
    > - Beleidstoewijzingen worden verwijderd uit een abonnement wanneer het abonnement wordt gekoppeld aan een andere Active Directory.
1. Het gebruikersaccount dat u gebruikt om de overdracht uit te voeren, moet in beide abonnementen beschikken over [Azure RBAC](add-change-subscription-administrator.md)-eigenaarstoegang.
1. Voordat u begint, moet u [controleren](/rest/api/resources/resources/validatemoveresources) of alle Azure-resources kunnen worden verplaatst van het bronabonnement naar het doelabonnement.  
    Sommige Azure-resources kunnen namelijk niet worden verplaatst tussen abonnementen. Als u de volledige lijst met Azure-resources wilt weergeven die wel kunnen worden verplaatst, raadpleegt u [Ondersteuning voor het verplaatsen van resources](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP ondersteunt alleen resources van Azure Resource Manager. Als er Azure-resources in het bronabonnement zijn gemaakt met behulp van het klassieke Azure-implementatiemodel, moet u deze vóór de migratie naar [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) migreren. U moet een partner zijn om de webpagina te kunnen weergeven.

1. Controleer of alle services van het bronabonnement gebruikmaken van het Azure Resource Manager-model. Vervolgens brengt u resources van het bronabonnement over naar het doelabonnement met behulp van de instructies in [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Het verplaatsen van Azure-resources tussen abonnementen kan leiden tot downtime van services, op basis van resources in de abonnementen.

## <a name="transfer-csp-subscription-to-other-offer"></a>CSP-abonnement overdragen naar een andere aanbieding

Als u andere Azure-abonnementen wilt overdragen van een CSP-partner naar een andere Azure-aanbieding, moet de abonnee resources verplaatsen tussen bron- en doelabonnementen van CSP.

1. Maak doelabonnementen voor Azure.
1. Zorg ervoor dat de bron- en doelabonnementen zich in dezelfde Azure Active Directory-tenant (Azure AD) bevinden. Zie [Een Azure-abonnement aan uw Azure Active Directory-tenant toevoegen of koppelen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) voor meer informatie over het wijzigen van een Azure AD-tenant.
    Houd er rekening mee dat de optie directory voor de wijziging niet wordt ondersteund voor het CSP-abonnement. U kunt bijvoorbeeld overgaan van een CSP-abonnement naar een abonnement met betalen per gebruik. U moet de directory van het abonnement met betalen per gebruik wijzigen, zodat deze overeenkomt met de directory.

    > [!IMPORTANT]
    >  - Wanneer u een abonnement aan een andere directory koppelt, verliezen gebruikers aan wie rollen zijn toegewezen met [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) hun toegang. Klassieke abonnementsbeheerders, waaronder de servicebeheerder en co-beheerders, verliezen ook hun toegang.
    >  - Beleidstoewijzingen worden verwijderd uit een abonnement wanneer het abonnement wordt gekoppeld aan een andere Active Directory.

1. Het gebruikersaccount dat u gebruikt om de overdracht uit te voeren, moet in beide abonnementen beschikken over [Azure RBAC](add-change-subscription-administrator.md)-eigenaarstoegang.
1. Voordat u begint, moet u [controleren](/rest/api/resources/resources/validatemoveresources) of alle Azure-resources kunnen worden verplaatst van het bronabonnement naar het doelabonnement.
    > [!IMPORTANT]
    >  - Sommige Azure-resources kunnen namelijk niet worden verplaatst tussen abonnementen. Als u de volledige lijst met Azure-resources wilt weergeven die wel kunnen worden verplaatst, raadpleegt u [Ondersteuning voor het verplaatsen van resources](../../azure-resource-manager/management/move-support-resources.md).

1. Gebruik de instructies in [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md) om resources van het bronabonnement over te brengen naar het doelabonnement.
    > [!IMPORTANT]
    >  - Het verplaatsen van Azure-resources tussen abonnementen kan leiden tot downtime van services, op basis van resources in de abonnementen.

## <a name="next-steps"></a>Volgende stappen
- [Vraag het eigendom van facturering van Azure-abonnementen aan voor uw MPA-account](mpa-request-ownership.md).
- Lees meer over het [beheren van accounts en abonnementen met Azure -facturering](../index.yml).

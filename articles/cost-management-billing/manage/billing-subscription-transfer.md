---
title: Eigendom van facturering voor een Azure-abonnement overdragen
description: Hierin word beschreven hoe het eigendom van de facturering van een Azure-abonnement kan worden overgedragen aan een ander account.
keywords: transfer azure subscription, azure transfer subscription, move azure subscription to another account,azure change subscription owner, transfer azure subscription to another account, azure transfer billing
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/06/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: ae588708a41c1259628b726a3a471034dba7d131
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601536"
---
# <a name="transfer-billing-ownership-of-an-azure-subscription-to-another-account"></a>Eigendom van de facturering van een Azure-abonnement overdragen aan een ander account

Dit artikel beschrijft de stappen die nodig zijn om het eigendom van de facturering van een Azure-abonnement over te dragen aan een ander account. Lees voordat u het eigendom van facturering voor een abonnement overdraagt eerst [About transferring billing ownership for an Azure subscription](../understand/subscription-transfer.md).

Zie [Uw Azure-abonnement overzetten naar een andere aanbieding](switch-azure-offer.md) als het abonnement uw eigendom moet blijven maar u het type abonnement wilt wijzigen. Zie [Ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md) als u wilt bepalen wie toegang hebben tot resources in het abonnement.

Als u een EA-klant (Enterprise Agreement) bent, kan uw ondernemingsbeheerder het eigendom van de facturering voor uw abonnementen overdragen naar andere accounts. Raadpleeg [Het Azure-abonnement of de accounteigenaar wijzigen](ea-portal-administration.md#change-azure-subscription-or-account-ownership) voor meer informatie.

Alleen de factureringsbeheerder van een account kan het eigendom van een abonnement overdragen.

## <a name="transfer-billing-ownership-of-an-azure-subscription"></a>Eigendom van facturering voor een Azure-abonnement overdragen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) als beheerder van het factureringsaccount met het abonnement dat u wilt overdragen. Als u niet zeker weet of u een beheerder bent, of als u wilt bepalen wie dat is, raadpleegt u [Factureringsbeheerder van account bepalen](../understand/subscription-transfer.md#whoisaa).
1. Zoek naar **Kostenbeheer en facturering**.  
   ![Schermopname met zoekfunctie in de Azure-portal](./media/billing-subscription-transfer/billing-search-cost-management-billing.png)
1. Selecteer in het linkerdeelvenster **Abonnementen**. Afhankelijk van de toegangsmogelijkheden moet u mogelijk een factureringsbereik en vervolgens **Abonnementen** of **Azure-abonnementen** selecteren.
1. Selecteer **Eigendom van facturering overdragen** voor het abonnement dat u wilt overdragen.  
   ![Het abonnement selecteren dat u wilt overdragen](./media/billing-subscription-transfer/billing-select-subscription-to-transfer.png)
1. Voer het e-mailadres in van een gebruiker die een factureringsbeheerder is voor het account dat de nieuwe eigenaar van het abonnement wordt.
1. Als u uw abonnement overdraagt naar een account in een andere Azure AD-tenant, selecteert u of u het abonnement wilt verplaatsen naar de tenant van het nieuwe account. Zie [Abonnementen overdragen naar een account in een andere Azure AD-tenant](#transfer-a-subscription-to-another-azure-ad-tenant-account) voor meer informatie.
    > [!IMPORTANT]
    > Als u ervoor kiest om het abonnement te verplaatsen naar de Azure AD-tenant van het nieuwe account, worden alle [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) voor toegang tot de resources in het abonnement definitief verwijderd. Alleen de gebruiker in het nieuwe account die uw overdrachtsaanvraag accepteert, heeft toegang om de resources in het abonnement te beheren. U kunt ook het vinkje voor de optie **Azure AD-tenant van abonnement** verwijderen om het eigendom van de facturering over te dragen zonder dat het abonnement naar de tenant van het nieuwe account wordt verplaatst. Als u dat doet, blijven bestaande Azure-roltoewijzingen voor toegang tot Azure-resources intact.  
    ![Overdrachtspagina verzenden](./media/billing-subscription-transfer/billing-send-transfer-request.png)
1. Selecteer **Overdrachtsaanvraag verzenden**.
1. De gebruiker ontvangt een e-mail met instructies voor het beoordelen van uw overdrachtsaanvraag.  
   ![E-mail voor de abonnementsoverdracht verzonden naar de ontvanger](./media/billing-subscription-transfer/billing-receiver-email.png)
1. De gebruiker kan de overdrachtsaanvraag goedkeuren door de koppeling in de e-mail te selecteren en de instructies te volgen. De gebruiker selecteert vervolgens een betalingswijze die wordt gebruikt om voor het abonnement te betalen. Als de gebruiker niet beschikt over een Azure-account, moet deze zich registreren voor een nieuw account.  
   ![Eerste webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step1.png)
   ![Tweede webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step2.png)
   ![Derde webpagina voor abonnementsoverdracht](./media/billing-subscription-transfer/billing-accept-ownership-step3.png)
1. Gelukt! Het abonnement wordt nu overgedragen.

## <a name="transfer-a-subscription-to-another-azure-ad-tenant-account"></a>Een abonnement overdragen aan een ander account van uw Azure AD-tenant

Er wordt een Azure Active Directory-tenant (AD) voor u gemaakt wanneer u zich aanmeldt voor Azure. De tenant vertegenwoordigt uw account. U gebruikt de tenant om de toegang tot uw abonnementen en resources te beheren.

Wanneer u een nieuw abonnement maakt, wordt dit gehost in de Azure AD-tenant van uw account. Als u anderen toegang wilt geven tot uw abonnement of de bijbehorende resources, moet u ze uitnodigen om lid te worden van uw tenant. Zo kunt u de toegang tot uw abonnementen en resources beheren.

Wanneer u het eigendom van de facturering van uw abonnement overdraagt naar een account in een andere Azure AD-tenant, kunt u het abonnement verplaatsen naar de tenant van het nieuwe account. Als u dit doet, raken alle gebruikers, groepen of service-principals die [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) hadden om abonnementen en de bijbehorende resources te beheren, hun toegang kwijt. Alleen de gebruiker in het nieuwe account die uw overdrachtsaanvraag accepteert, heeft toegang om de resources te beheren. De nieuwe eigenaar moet deze gebruikers handmatig toevoegen aan het abonnement om toegang te bieden voor gebruikers die de toegang zijn kwijtgeraakt. Raadpleeg [Een Azure-abonnement overzetten naar een andere Azure AD-map](../../role-based-access-control/transfer-subscription.md) voor meer informatie.

## <a name="transfer-visual-studio-and-partner-network-subscriptions"></a>Visual Studio- en Microsoft Partner Network-abonnementen overdragen

Voor Visual Studio- en Microsoft Partner Network-abonnementen geldt een maandelijks terugkerend Azure-tegoed. Wanneer u deze abonnementen overdraagt, is uw tegoed niet beschikbaar in het doelfactureringsaccount. Het tegoed in het doelfactureringsaccount wordt gebruikt voor het abonnement. Als Bob bijvoorbeeld op 9 september een Visual Studio Enterprise-abonnement overdraagt naar het account van Jeanette en Jeanette de overdracht accepteert. Zodra de overdracht is voltooid, wordt het tegoed in het account van Jeanette gebruikt voor het abonnement. Het tegoed wordt elke negende dag van de maand opnieuw ingesteld.

## <a name="next-steps-after-accepting-billing-ownership"></a>Volgende stappen na het accepteren van het eigendom van facturering

Als u het eigendom van de facturering van een Azure-abonnement hebt geaccepteerd, is het raadzaam de volgende stappen te volgen:

1. Controleer de servicebeheerder, co-beheerders en andere Azure-roltoewijzingen en werk ze bij. Zie [Toevoegen of wijzigen van de beheerders van Azure-abonnementen](add-change-subscription-administrator.md) en [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.
1. Referenties bijwerken die zijn gekoppeld aan de services van dit abonnement, waaronder:
   1. Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../../cloud-services/cloud-services-certs-create.md) voor meer informatie
   1. Toegangssleutels voor services zoals Storage. Zie [Over Azure-opslagaccounts](../../storage/common/storage-account-create.md) voor meer informatie
   1. Referenties voor externe toegang voor services zoals Azure Virtual Machines.
1. Als u met een partner werkt, kunt u overwegen om de partner-id voor het abonnement bij te werken. U kunt de partner-id bijwerken in [Azure Portal](https://portal.azure.com). Zie [Een partner-id aan uw Azure-accounts koppelen](link-partner-id.md) voor meer informatie

## <a name="cancel-a-transfer-request"></a>Een overdrachtsaanvraag annuleren

Er is slechts één overdrachtsaanvraag tegelijk actief. Een overdrachtsaanvraag is 15 dagen geldig. Na 15 dagen verloopt de overdrachtsaanvraag.

Een overdrachtsaanvraag annuleren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar **Abonnementen** > selecteer het abonnement waarvoor u een overdrachtsaanvraag hebt verzonden > selecteer **Eigendom van facturering overdragen**.
1. Selecteer onder aan de pagina **De overdrachtsaanvraag annuleren**.

:::image type="content" source="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" alt-text="Voorbeeld van het venster Eigendom van facturering overdragen met de optie De overdrachtsaanvraag annuleren" lightbox="./media/billing-subscription-transfer/transfer-billing-owership-cancel-request.png" :::

## <a name="troubleshooting"></a>Problemen oplossen

Gebruik de volgende gegevens om u te helpen bij het oplossen van eventuele problemen met het overdragen van abonnementen.

### <a name="the-transfer-subscription-option-is-unavailable"></a>De optie 'Abonnement overdragen' is niet beschikbaar

<a name="no-button"></a> 

De overdracht van selfserviceabonnementen is niet beschikbaar voor uw factureringsaccount. Het is momenteel niet mogelijk om het eigendom van de facturering van abonnementen in EA-accounts (Enterprise Agreement) in Azure Portal te verplaatsen. Bovendien bieden Microsoft Customer Agreement-accounts die zijn gemaakt in samenwerking met een vertegenwoordiger van Microsoft, geen ondersteuning voor de overdracht van het eigendom van de facturering.

###  <a name="not-all-subscription-types-can-transfer"></a>Niet alle typen abonnementen kunnen worden overgedragen

Niet alle typen abonnementen bieden ondersteuning voor de overdracht van het eigendom van de facturering. Zie [Ondersteunde typen abonnementen](../understand/subscription-transfer.md#supported-subscription-types) voor een overzicht van typen abonnementen die overdrachten ondersteunen

###  <a name="access-denied-error-shown-when-trying-to-transfer-subscription-billing-ownership"></a>Fout Toegang geweigerd wordt weergegeven bij het overdragen van het eigendom van de facturering van het abonnement

Deze fout wordt weergegeven wanneer u een Microsoft Azure Plan-abonnement probeert over te dragen en u niet over de benodigde machtigingen beschikt. Als u een Microsoft Azure Plan-abonnement wilt overdragen, moet u een eigenaar of bijdrager zijn in de factuursectie waarnaar het abonnement wordt gefactureerd. Zie [Abonnementen beheren voor de factuursectie](../manage/understand-mca-roles.md#manage-subscriptions-for-invoice-section) voor meer informatie.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- Controleer de servicebeheerder, co-beheerders en andere Azure-roltoewijzingen en werk ze bij. Zie [Toevoegen of wijzigen van de beheerders van Azure-abonnementen](add-change-subscription-administrator.md) en [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.
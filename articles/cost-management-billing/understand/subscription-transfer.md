---
title: De eigendom van facturering voor een Azure-abonnement overdragen
description: In dit artikel wordt uitgelegd wat u moet weten voordat u het eigendom van een Azure-abonnement overdraagt aan een ander account.
keywords: transfer azure subscription, azure transfer subscription, move azure subscription to another account,azure change subscription owner, transfer azure subscription to another account, azure transfer billing
author: bandersmsft
ms.reviewer: amberb
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 163bd7dbdac4de7b7d9c1657ac3debf45382a27a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027268"
---
# <a name="about-transferring-billing-ownership-for-an-azure-subscription"></a>De eigendom van facturering voor een Azure-abonnement overdragen

Dit artikel helpt u beter inzicht te krijgen in wat u moet weten voordat u het eigendom van facturering van een Azure-abonnement overdraagt naar een ander account. 

Mogelijk wilt u het eigendom van de facturering van uw Azure-abonnement overdragen als u uw organisatie verlaat of wilt u dat het abonnement wordt gefactureerd aan een ander account. Door het eigendom van de facturering aan een ander account over te dragen, zijn de beheerders in het nieuwe account gemachtigd factureringstaken uit te voeren. Ze kunnen de betalingswijze veranderen, kosten bekijken en het abonnement annuleren.

Zie [Uw Azure-abonnement overzetten naar een andere aanbieding](../manage/switch-azure-offer.md) als het abonnement uw eigendom moet blijven maar u het type abonnement wilt wijzigen. Zie [Ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md) als u wilt bepalen wie toegang hebben tot resources in het abonnement.

Als u een EA-klant (Enterprise Agreement) bent, kan uw ondernemingsbeheerder het eigendom van de facturering voor uw abonnementen overdragen naar andere accounts.

Alleen de factureringsbeheerder van een account kan het eigendom van een abonnement overdragen.

## <a name="determine-account-billing-administrator"></a>Factureringsbeheerder van account bepalen

<a name="whoisaa"></a>

De factureringsbeheerder is de persoon die gemachtigd is om de facturering voor een account te beheren. Ze hebben toegang tot de facturering in de [Azure-portal](https://portal.azure.com) en kunnen diverse factureringstaken uitvoeren. Ze kunnen bijvoorbeeld abonnementen maken, facturen weergeven en betalen van facturen of betalingswijzen bijwerken.

Ga naar de pagina [Kostenbeheer en facturering in Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/Overview) om accounts te identificeren waarvoor u een factureringsbeheerder bent. Selecteer in het linkerdeelvenster **Alle factureringsbereiken**. Op de pagina met abonnementen worden alle abonnementen weergegeven waarvoor u factureringsbeheerder bent.

Als u niet zeker weet wie de accountbeheerder is voor een abonnement, gaat u naar de [Abonnementenpagina in Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Selecteer het abonnement dat u wilt controleren en kijk vervolgens bij **Instellingen**. Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.


## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

De onderstaande typen abonnementen kunnen in Azure Portal worden overgedragen. Momenteel kunnen er geen [gratis proefabonnementen](https://azure.microsoft.com/offers/ms-azr-0044p/) of [AIO-abonnementen (Azure in Open)](https://azure.microsoft.com/offers/ms-azr-0111p/) worden overgedragen. Zie [Resources naar een nieuwe groep of nieuw abonnement verplaatsen](../../azure-resource-manager/management/move-resource-group-and-subscription.md) voor een tijdelijke oplossing. Als u andere abonnementen, zoals ondersteuningsplannen wilt overdragen, [neemt u contact op met de ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)<sup>1</sup>
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)  
- [Visual Studio Enterprise (MPN)-abonnees](https://azure.microsoft.com/offers/ms-azr-0029p/)
- [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/)  
- [Betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)
- [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/)
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)
- [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)
- [Microsoft Azure-plan](https://azure.microsoft.com/offers/ms-azr-0017g/)<sup>2</sup>

<sup>1</sup> Met behulp van de EA-portal.

<sup>2</sup> Alleen ondersteund voor accounts die worden gemaakt tijdens het aanmelden op de Azure-website.

## <a name="resources-transferred-with-subscriptions"></a>Resources overgedragen met abonnementen

Alle resources zoals VM's, schijven en websites worden overgedragen naar het nieuwe account. Als u echter een abonnement overdraagt naar een account in een andere Azure AD-tenant, worden de [beheerdersrollen](../manage/add-change-subscription-administrator.md) en [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) voor het abonnement niet overgedragen. Bovendien worden [app-registraties](../../active-directory/develop/quickstart-register-app.md) en andere tenantspecifieke services niet overgedragen met het abonnement.

## <a name="transfer-account-ownership-to-another-countryregion"></a>Accounteigendom overdragen naar een ander land of andere regio

U kunt helaas geen abonnementen overdragen tussen landen of regio's met behulp van Azure Portal. Ze kunnen echter wel worden overgedragen als u een Azure-ondersteuningsaanvraag opent. [Neem contact op met de ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u een ondersteuningsaanvraag wilt maken.

## <a name="transfer-a-subscription-from-one-account-to-another"></a>Een abonnement overdragen van het ene account naar het andere

Als u beheerder bent van twee accounts, kunt u een abonnement overdragen tussen uw accounts. Uw accounts worden conceptueel gezien als accounts van twee verschillende gebruikers, zodat abonnementen tussen uw accounts kunt overdragen.
Zie [Eigendom van facturering voor een Azure-abonnement overdragen](../manage/billing-subscription-transfer.md) om te zien welke stappen nodig zijn om uw abonnement over te dragen.

## <a name="transferring-a-subscription-shouldnt-create-downtime"></a>Het overdragen van een abonnement zou niet tot downtime moeten leiden

Als u een abonnement overdraagt naar een account in dezelfde Azure AD-tenant, is dit niet van invloed op de actieve resources in het abonnement. Contextgegevens die zijn opgeslagen in PowerShell, worden echter niet bijgewerkt, dus moet u deze mogelijk wissen of instellingen wijzigen. Als u het abonnement overdraagt naar een account in een andere tenant en besluit het abonnement naar de tenant te verplaatsen, verliezen alle gebruikers, groepen en service-principals die [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) hadden voor toegang tot resources in het abonnement, hun toegang. Uitvaltijd van de service kan het gevolg zijn.

## <a name="new-account-usage-and-billing-history"></a>Gebruiks- en factureringsgeschiedenis voor nieuw account

De enige informatie die beschikbaar is voor de gebruikers van het nieuwe account, zijn de abonnementskosten voor de afgelopen maand. De rest van de gebruiks- en factureringsgeschiedenis wordt niet overgedragen met het abonnement.

## <a name="manually-migrate-data-and-services"></a>Gegevens en services handmatig migreren

Wanneer u een abonnement overdraagt, blijven de resources bij het abonnement. Als u het eigendom van het abonnement niet kunt overdragen, kunt u de resources ervan handmatig migreren. Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="remaining-subscription-credits"></a>Resterend tegoed op abonnement 

Als u een abonnement op Visual Studio of Microsoft Partner Network hebt, ontvangt u maandelijks tegoed. Uw tegoed wordt niet met het abonnement overgedragen naar het nieuwe account. De gebruiker die de overdrachtsaanvraag accepteert, moet een Visual Studio-licentie hebben om de overdrachtsaanvraag te accepteren. Het abonnement maakt gebruik van het Visual Studio-tegoed dat beschikbaar is in het account van de gebruiker. Zie [Abonnementen voor Visual Studio en Partner Network overdragen](../manage/billing-subscription-transfer.md#transfer-visual-studio-and-partner-network-subscriptions) voor meer informatie.

## <a name="users-keep-access-to-transferred-resources"></a>Gebruikers behouden toegang tot overgedragen resources

Denk eraan dat gebruikers met toegang tot resources in een abonnement hun toegang behouden wanneer het eigendom wordt overgedragen. [Beheerdersrollen](../manage/add-change-subscription-administrator.md) en [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) kunnen echter worden verwijderd. De toegang wordt verbroken als uw account zich in een andere Azure AD-tenant bevindt dan de tenant van het abonnement en de gebruiker die de overdrachtaanvraag heeft verzonden, het abonnement verplaatst naar de tenant van uw account. 

U kunt de gebruikers die Azure-roltoewijzingen hebben om toegang te krijgen tot de resources in het abonnement weergeven in Azure Portal. Ga naar de [pagina Abonnement in Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Selecteer het abonnement dat u wilt controleren en selecteer in het linkerdeelvenster **Toegangsbeheer (IAM)** . Selecteer boven aan de pagina **Roltoewijzingen**. De pagina met roltoewijzingen bevat een overzicht van alle gebruikers met toegang voor het abonnement.

Zelfs als de [Azure-roltoewijzingen](../../role-based-access-control/role-assignments-portal.md) tijdens de overdracht worden verwijderd, hebben gebruikers in het account van de oorspronkelijke eigenaar mogelijk nog toegang tot het abonnement via andere beveiligingsmechanismen, waaronder:

* Beheercertificaten die de gebruiker beheerdersrechten verlenen voor de abonnementsresources. Zie [Een beheercertificaat voor Azure maken en uploaden](../../cloud-services/cloud-services-certs-create.md) voor meer informatie.
* Toegangssleutels voor services zoals Storage. Zie [Over Azure-opslagaccounts](../../storage/common/storage-account-create.md) voor meer informatie.
* Referenties voor externe toegang voor services zoals Azure Virtual Machines.

Als ontvangers de toegang tot hun resources willen beperken, moeten ze overwegen om de geheimen bij te werken die zijn gekoppeld aan de service. De meeste resources kunnen worden bijgewerkt. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer in het Hub-menu **Alle resources**. Selecteer vervolgens de resource. Selecteer **Instellingen** op de resourcepagina. Daar kunt u bestaande geheimen weergeven en bijwerken.

## <a name="you-pay-for-usage-when-you-receive-ownership"></a>U betaalt voor gebruik wanneer u het eigendom ontvangt

Uw account is verantwoordelijk voor de betaling van het gebruik dat wordt gerapporteerd vanaf het tijdstip van de overdracht. Het is mogelijk dat bepaald gebruik dat plaatsvond vóór de overdracht, pas daarna wordt gerapporteerd. Het gebruik is opgenomen in de factuur van uw account.

## <a name="use-a-different-payment-method"></a>Een andere betalingswijze gebruiken

Wanneer u de overdrachtsaanvraag accepteert, kunt u een bestaande betalingswijze selecteren die aan uw account is gekoppeld of een nieuwe betalingswijze toevoegen.

## <a name="transfer-enterprise-agreement-subscription-ownership"></a>Eigendom van Enterprise-overeenkomstabonnement overdragen

De Enterprise-beheerder kan het eigendom voor elk account bijwerken, zelfs nadat een eigenaar van het oorspronkelijke account niet langer deel uitmaakt van de organisatie. Zie [Azure Enterprise-overdrachten](../manage/ea-transfers.md) voor meer informatie over het overdragen van Azure Enterprise Agreement-accounts.

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Eigendom van facturering voor een Azure-abonnement overdragen](../manage/billing-subscription-transfer.md)

---
title: Eigendom aanvragen van facturering van Azure-abonnementen voor Microsoft Partner-overeenkomst (MPA)
description: Meer informatie over het aanvragen van eigendom van facturering van Azure-abonnementen van andere gebruikers voor Microsoft Partner-overeenkomst (MPA).
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/19/2020
ms.author: banders
ms.openlocfilehash: 0c5ecb61b5b34864aa89c1f0e760e96c383a269f
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091491"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>Eigendom aanvragen van facturering van Azure-abonnementen voor uw MPA-account

Als u één gecombineerde factuur wilt bieden voor beheerde services en Azure-verbruik, kan een Cloud Solution Provider (CSP) het eigendom van de facturering van Azure-abonnementen overnemen van hun klanten met Direct Enterprise Agreements (EA).

Deze functie is alleen beschikbaar voor CSP-partners voor directe facturering die zijn gecertificeerd als [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp). Het valt onder governance en beleid van Microsoft en voor bepaalde klanten is mogelijk een beoordeling en goedkeuring vereist.

Als u het eigendom van de facturering wilt aanvragen, moet u beschikken over de rol **Globale beheerder** of **Beheerderagent**. Zie [Partner Center - Assign users roles and permissions](/partner-center/permissions-overview) (Engelstalig) voor meer informatie.

Dit artikel is van toepassing op factureringsrekeningen voor Microsoft Partner-overeenkomsten. Deze rekeningen worden gemaakt voor Cloud Solution Providers (CSP's) voor het beheren van de facturering voor hun klanten in de nieuwe commerce-ervaring. De nieuwe ervaring is alleen beschikbaar voor partners die ten minste één klant hebben die een Microsoft-klantovereenkomst (MCA) heeft geaccepteerd en een Azure-plan heeft. [Controleer of u toegang hebt tot een Microsoft Partner-overeenkomst](#check-access-to-a-microsoft-partner-agreement).

## <a name="prerequisites"></a>Vereisten

1. Een [resellerrelatie ](/partner-center/request-a-relationship-with-a-customer) met de klant tot stand brengen. Controleer in het [overzicht van geautoriseerde CSP's per regio](/partner-center/regional-authorization-overview) of de tenant van de klant en de tenant van de partner zich in dezelfde regio bevinden.
1. [Controleer of de klant de Microsoft-klantovereenkomst heeft geaccepteerd](/partner-center/confirm-customer-agreement).
1. Stel een [Azure-plan](/partner-center/purchase-azure-plan) in voor de klant. Als de klant aankopen doet via meerdere resellers, moet u een Azure-plan instellen voor elke combinatie van een klant en een wederverkoper.

## <a name="request-billing-ownership"></a>Eigendom van facturering aanvragen

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met de referenties van de CSP-beheeragent in de CSP-tenant.
1. Zoek naar **Kostenbeheer en facturering**.  
    ![Schermopname van zoekopdracht in Azure Portal naar kostenbeheer en facturering om eigendom van facturering aan te vragen.](./media/mpa-request-ownership/search-cmb.png)
1. Selecteer **Klanten** aan de linkerkant en selecteer vervolgens een klant in de lijst.  
    [![Schermopname van de selectie van klanten](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Selecteer **Overdrachtsaanvragen** aan de linkerkant en selecteer vervolgens **Een nieuwe aanvraag toevoegen**.  
    [![Schermopname van de selectie van overdrachtsaanvragen](./media/mpa-request-ownership/mpa-select-transfer-requests.png)](./media/mpa-request-ownership/mpa-select-transfer-requests.png#lightbox)
1. Voer het e-mailadres in van de gebruiker in de klantorganisatie die de overdrachtsaanvraag moet accepteren. De gebruiker moet een accounteigenaar voor een Enterprise Agreement zijn. Selecteer **Overdrachtsaanvraag verzenden**.  
    [![Schermopname van de verzending van een overdrachtsaanvraag](./media/mpa-request-ownership/mpa-send-transfer-requests.png)](./media/mpa-request-ownership/mpa-send-transfer-requests.png#lightbox)
1. De gebruiker ontvangt een e-mail met instructies voor het beoordelen van uw overdrachtsaanvraag.  
    ![Schermopname van de e-mail over de beoordeling van een overdrachtsaanvraag](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)
1. De gebruiker kan de overdrachtsaanvraag goedkeuren door de koppeling in de e-mail te selecteren en de instructies te volgen.  
    [![Schermopname waarin u de aanvraag voor het controleren van de overdracht ziet](./media/mpa-request-ownership/review-transfer-requests.png)](./media/mpa-request-ownership/review-transfer-requests.png#lightbox) De gebruiker kan het factureringsaccount selecteren van waaruit ze Azure-producten willen overdragen. Zodra producten die voor overdracht in aanmerking komen, zijn geselecteerd, worden ze weergegeven. **Opmerking:** Uitgeschakelde abonnementen kunnen niet worden overgedragen en worden weergegeven in de lijst Niet-overdraagbare Azure-producten, indien van toepassing. Zodra de Azure-producten die moeten worden overgedragen, zijn geselecteerd, selecteert u **Valideren**.
1. In het gebied **Resultaat van validatie van overdracht** wordt de impact weergegeven van de Azure-producten die worden overgedragen. De mogelijke statussen zijn:
    * **Geslaagd**: de validatie voor dit Azure-product is geslaagd en dit product kan worden overgedragen.
    * **Waarschuwing**: voor het geselecteerde Azure-product geldt een waarschuwing. Het product kan nog wel worden overgedragen, maar dit heeft wel gevolgen waarvan de gebruiker zich bewust moet zijn in het geval deze stappen wil uitvoeren om dit probleem op te lossen. Het Azure-abonnement dat wordt overgedragen, profiteert bijvoorbeeld van een RI. Na de overdracht profiteert dat abonnement niet meer van dat voordeel. Voor maximale besparingen moet u ervoor zorgen dat de RI wordt gekoppeld aan een ander abonnement, die van de voordelen zou kunnen profiteren. In plaats daarvan kan de gebruiker er ook voor kiezen om terug te gaan naar de selectiepagina en de selectie van dit Azure-abonnement ongedaan maken.
    * **Mislukt**: het geselecteerde Azure-product kan niet worden overgedragen vanwege een fout. De gebruiker moet teruggaan naar de selectiepagina en de selectie van dit product ongedaan maken om de andere geselecteerde Azure-producten over te dragen.  
    ![Schermopname van de validatieprocedure](./media/mpa-request-ownership/validate-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>De status van de overdrachtsaanvraag controleren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.  
    ![Schermopname van zoekopdracht in Azure Portal naar kostenbeheer en facturering om de overdrachtsstatus aan te vragen.](./media/mpa-request-ownership/billing-search-cost-management-billing.png)
1. Selecteer **Klanten** aan de linkerkant.  
    [![Schermopname van de selectie van klanten](./media/mpa-request-ownership/mpa-select-customers.png)](./media/mpa-request-ownership/mpa-select-customers.png#lightbox)
1. Selecteer de klant in de lijst waarvoor u de overdrachtsaanvraag hebt verzonden.
1. Selecteer **Overdrachtsaanvragen** aan de linkerkant. Op de pagina Overdrachtsaanvragen wordt de volgende informatie weergegeven: [![Schermopname van de lijst met overdrachtsaanvragen](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png#lightbox)

   |Kolom|Definitie|
   |---------|---------|
   |Aanvraagdatum|De datum waarop de overdrachtsaanvraag is verzonden|
   |Ontvanger|Het e-mailadres van de gebruiker aan wie u de aanvraag voor het overdragen van het eigendom van facturering hebt verzonden|
   |Vervaldatum|De datum waarop de aanvraag verloopt|
   |Status|De status van de overdrachtsaanvraag|

    De overdrachtsaanvraag kan een van de volgende statussen hebben:

   |Status|Definitie|
   |---------|---------|
   |Wordt uitgevoerd|De gebruiker heeft de overdrachtsaanvraag niet geaccepteerd|
   |Wordt verwerkt|De gebruiker heeft de overdrachtsaanvraag geaccepteerd. Facturering voor abonnementen die de gebruiker heeft geselecteerd, wordt overgebracht naar uw account|
   |Voltooid| De facturering voor abonnementen die de gebruiker heeft geselecteerd, wordt overgebracht naar uw account|
   |Voltooid met fouten|De aanvraag is voltooid, maar de facturering voor sommige abonnementen die de gebruiker heeft geselecteerd, kan niet worden overgedragen|
   |Verlopen|De gebruiker heeft de aanvraag niet op tijd geaccepteerd en de aanvraag is verlopen|
   |Geannuleerd|Iemand met toegang tot de overdrachtsaanvraag heeft de aanvraag geannuleerd|
   |Geweigerd|De gebruiker heeft de overdrachtsaanvraag geweigerd|

1. Selecteer een overdrachtsaanvraag om details weer te geven. Op de pagina met overdrachtsdetails wordt de volgende informatie weergegeven: [![Schermopname van de lijst van overgedragen abonnementen](./media/mpa-request-ownership/mpa-transfer-completed.png)](./media/mpa-request-ownership/mpa-transfer-completed.png#lightbox)

   |Kolom  |Definitie|
   |---------|---------|
   |Id van overdrachtsaanvraag|De unieke id voor uw overdrachtsaanvraag. Als u een ondersteuningsaanvraag indient, deelt u deze id met de Azure-ondersteuning om uw ondersteuningsaanvraag te versnellen|
   |Overdracht aangevraagd op|De datum waarop de overdrachtsaanvraag is verzonden|
   |Overdracht aangevraagd door|Het e-mailadres van de gebruiker die de overdrachtsaanvraag heeft verzonden|
   |Overdrachtsaanvraag verloopt op| De datum waarop de overdrachtsaanvraag verloopt|
   |E-mailadres van ontvanger|Het e-mailadres van de gebruiker aan wie u de aanvraag voor het overdragen van het eigendom van facturering hebt verzonden|
   |Overdrachtskoppeling verzonden naar ontvanger|De url die naar de gebruiker is verzonden om de overdrachtsaanvraag te controleren|

## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

U kunt het eigendom van facturering aanvragen van de hieronder vermelde typen abonnementen.

* [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
* [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* U moet een Dev/Test-abonnement omzetten naar een EA Enterprise-aanbieding via een ondersteuningsticket. Een Enterprise Dev/Test-abonnement wordt gefactureerd met een tarief op basis van betalen per gebruik nadat het is overgezet. Kortingen die via de Enterprise Dev/Test-aanbieding worden aangeboden met de EA van de klant, zijn niet beschikbaar voor de CSP-partner.

## <a name="additional-information"></a>Aanvullende informatie

Het volgende gedeelte bevat aanvullende informatie over het overdragen van abonnementen.

### <a name="no-service-downtime"></a>Geen servicedowntime

Azure-services in het abonnement worden zonder onderbreking uitgevoerd. We maken de overgang alleen voor de factureringsrelatie voor de Azure-abonnementen die de gebruiker selecteert om over te dragen.

### <a name="disabled-subscriptions"></a>Uitgeschakelde abonnementen

Uitgeschakelde abonnementen kunnen niet worden overgedragen. Abonnementen moeten de status actief hebben om hun eigendom van facturering te kunnen overdragen.

### <a name="azure-resources-transfer"></a>Overdracht van Azure-bronnen

Alle resources van de abonnementen, zoals VM's, schijven en websites, worden overgebracht. Bij de overdracht blijven abonnements-id's en resource-id's behouden. 

### <a name="azure-marketplace-products-transfer"></a>Overdracht van Azure Marketplace-producten

Azure Marketplace-producten die beschikbaar zijn voor abonnementen die worden beheerd door Cloud Solution Providers (CSP's), worden samen met hun respectieve abonnementen overgedragen. Abonnementen met Azure Marketplace-producten die niet zijn ingeschakeld voor CSP's, kunnen niet worden overgedragen.

### <a name="azure-reservations-transfer"></a>Overdracht van Azure-reserveringen

Azure-reserveringen worden niet automatisch verplaatst met abonnementen. U kunt de reservering behouden in EA voor andere abonnementen, of u kunt [de reservering annuleren](../reservations/exchange-and-refund-azure-reservations.md), waarna de partner deze opnieuw kan aanschaffen in CSP.

### <a name="access-to-azure-services"></a>Toegang tot Azure-services

Toegang voor bestaande gebruikers, groepen of service-principals die is toegewezen met behulp van [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../../role-based-access-control/overview.md) wordt niet beïnvloed tijdens de overgang. De partner krijgt geen nieuwe Azure RBAC-toegang tot de abonnementen.

De partners moeten samenwerken met de klant om toegang te krijgen tot abonnementen. De partners moeten [Beheer namens - AOBO (Admin On Behalf Of)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) of [Azure Lighthouse](../../lighthouse/concepts/cloud-solution-provider.md)-toegang krijgen om ondersteuningstickets te openen.

### <a name="azure-support-plan"></a>Azure-ondersteuningsplan

Ondersteuning voor Azure wordt niet overgedragen met de abonnementen. Als de gebruiker alle Azure-abonnementen overdraagt, vraagt u deze gebruiker om het ondersteuningsplan te annuleren. Na de overdracht is de CSP-partner verantwoordelijk voor de ondersteuning. De klant moet samenwerken met de CSP-partner voor elke ondersteuningsaanvraag.  

### <a name="charges-for-transferred-subscription"></a>Kosten voor overgedragen abonnement

De oorspronkelijke factureringseigenaar van de abonnementen is verantwoordelijk voor alle kosten die zijn gerapporteerd tot het moment waarop de overdracht is voltooid. U bent verantwoordelijk voor de kosten die worden gerapporteerd vanaf het tijdstip van de overdracht. Er kunnen kosten zijn die plaatsvonden vóór de overdracht, maar die daarna werden gerapporteerd. Deze kosten worden weergegeven op uw factuur.

### <a name="cancel-a-transfer-request"></a>Een overdrachtsaanvraag annuleren

U kunt de overdrachtsaanvraag annuleren totdat de aanvraag is goedgekeurd of geweigerd. Als u de overdrachtsaanvraag wilt annuleren, gaat u naar de [pagina met overdrachtsgegevens](#check-the-transfer-request-status) en selecteert u Annuleren onderaan de pagina.

### <a name="software-as-a-service-saas-transfer"></a>Overdracht van Software als een dienst (SaaS)

SaaS-producten worden niet overgedragen met de abonnementen. Vraag de gebruiker om [Contact op te nemen met de ondersteuning voor Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om eigendom van SaaS-producten over te dragen. Samen met het eigendom van facturering kan de gebruiker ook het eigendom van resources overdragen. Met eigendom van resources kunt u beheerbewerkingen uitvoeren, zoals het verwijderen en weergeven van de details van het product. De gebruiker moet een resource-eigenaar zijn van het SaaS-product om eigendom van de resource over te dragen.

### <a name="additional-approval-for-certain-customers"></a>Aanvullende goedkeuring voor bepaalde klanten

Voor bepaalde overdrachtsaanvragen kan een extra beoordelingsproces van Microsoft vereist zijn vanwege de aard van de huidige Enterprise Enrollment-structuur van de klant. De partner wordt op de hoogte gesteld van dergelijke vereisten wanneer zij proberen een uitnodiging te verzenden naar klanten. Partners dienen samen te werken met hun Partner Development Manager en het accountteam van de klant om dit beoordelingsproces te voltooien.

### <a name="azure-subscription-directory"></a>Map van Azure-abonnement

De map van de Azure-abonnementen die worden overgedragen, moet overeenkomen met de map van de klant die is geselecteerd tijdens het tot stand brengen van de CSP-relatie.

Als deze twee mappen niet overeenkomen, kunnen de abonnementen niet worden overgedragen. U moet een nieuwe CSP-resellerrelatie met de klant tot stand brengen door de map van de Azure-abonnementen te selecteren of de map van Azure-abonnementen zo te wijzigen dat deze overeenkomt met de map van de CSP-relatie van de klant. Zie [Een bestaand abonnement koppelen aan uw Azure AD Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory).

### <a name="ea-subscription-in-the-non-organization-directory"></a>EA-abonnement in niet-organisatiemap

De EA-abonnementen in mappen die niet van de organisatie zijn, kunnen worden overgedragen op voorwaarde dat de map een resellerrelatie heeft met de CSP. Als de map geen resellerrelatie heeft, moet u ervoor zorgen dat de organisatiegebruiker zich in de map bevindt als een *globale beheerder* die de partnerrelatie kan accepteren. Het domeinnaamgedeelte van de gebruikersnaam moet bestaan uit de oorspronkelijke standaarddomeinnaam '[domeinnaam]. onmicrosoft.com' of de naam van een geverifieerd, niet-federatief aangepaste domein zoals 'contoso.com'.  

Als u een nieuwe gebruiker aan de map wilt toevoegen, raadpleegt u [Quickstart: Nieuwe gebruikers toevoegen aan Azure Active Directory om de nieuwe gebruiker toe te voegen aan de directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Toegang tot een Microsoft Partner-overeenkomst controleren

[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Hulp nodig? Contact opnemen met ondersteuning

Als u hulp nodig hebt, kunt u [contact opnemen met ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om uw probleem snel op te lossen.

## <a name="next-steps"></a>Volgende stappen

* Het eigendom van facturering van de Azure-abonnementen wordt overgezet naar u. Houd de kosten voor deze abonnementen bij in [Azure Portal.](https://portal.azure.com)
* Werk samen met de klant om toegang te krijgen tot de overgedragen Azure-abonnementen. [Wijs Azure-rollen toe met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md).
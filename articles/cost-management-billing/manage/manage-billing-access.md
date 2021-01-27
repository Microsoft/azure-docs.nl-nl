---
title: Toegang tot Azure-facturering beheren
description: Ontdek hoe u leden van uw team toegang tot uw Azure-factureringsgegevens geeft.
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/26/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 23e3adad12a726b03f6efe309dde0ec4a93bcd5b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897422"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Toegang tot factureringsgegevens beheren voor Azure

U kunt anderen toegang geven tot de factureringsgegevens voor uw account in de Azure-portal. Het type factureringsrollen en de instructies om toegang te bieden tot de factureringsgegevens variëren per type factureringsaccount. Zie [Het type van uw factureringsaccount controleren](#check-the-type-of-your-billing-account) om het type van uw factureringsaccount te bepalen.

Het artikel is van toepassing op klanten met accounts van Microsoft Online-serviceprogramma's. Als u een Azure-klant bent met een Enterprise Overeenkomst (EA) en de Enterprise-beheerder bent, kunt u machtigingen verlenen aan de beheerders van de afdeling en de eigenaren van de accounts in het Enterprise-portal. Zie [Inzicht in Azure Enterprise Overeenkomst-beheerdersrollen in Azure](understand-ea-roles.md) voor meer informatie. Als u een klant van een Microsoft-klantovereenkomst bent, raadpleegt u [Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure](understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>Accountbeheerders voor accounts van het Microsoft Online-serviceprogramma

Een Accountbeheerder is de enige eigenaar van een factureringsaccount voor het Microsoft Online-serviceprogramma. De rol wordt toegewezen aan een persoon die zich heeft aangemeld voor Azure. Accountbeheerders zijn gemachtigd om verschillende factureringstaken uit te voeren, zoals abonnementen maken, facturen in te zien of de facturering voor een abonnement wijzigen.

## <a name="give-others-access-to-view-billing-information"></a>Anderen toegang geven om factureringsgegevens in te zien

Accountbeheerders kunnen anderen toegang verlenen tot informatie over Azure-facturering door een van de volgende rollen toe te wijzen aan een abonnement in hun account.

- Servicebeheerder
- Medebeheerder
- Eigenaar
- Inzender
- Lezer
- Lezer voor facturering

Deze rollen hebben toegang tot de factureringsgegevens in de [Azure-portal](https://portal.azure.com/). Personen aan wie deze rollen zijn toegewezen, kunnen ook de [Billing-API's](consumption-api-overview.md#usage-details-api) gebruiken om programmatisch facturen en gebruiksgegevens te verkrijgen.

Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie over het toewijzen van rollen.

**Als u een EA-klant bent, kan een accounteigenaar de bovenstaande rol toewijzen aan andere gebruikers van zijn team. Maar als u wilt dat deze gebruikers factureringsgegevens kunnen inzien, moet de Enterprise-beheerder de accounteigenaar toestaan om rekeningen in het Enterprise-portal in te zien.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a>Gebruikers toestaan facturen te downloaden

Nadat een accountbeheerder de juiste rollen aan andere gebruikers heeft toegewezen, moet hij toegang inschakelen om facturen te downloaden in de Azure-portal. Facturen ouder dan december 2016 zijn alleen beschikbaar voor de accountbeheerder.

1. Meld u als accountbeheerder aan bij de [Azure-portal](https://portal.azure.com/),

1. Zoek naar **kostenbeheer en facturering**.

    ![Schermopname met Kostenbeheer en facturering gemarkeerd in de sectie Services.](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Selecteer in het linkerdeelvenster **Abonnementen**. Afhankelijk van de toegangsmogelijkheden moet u mogelijk een factureringsbereik en vervolgens **Abonnementen** selecteren.

    ![Schermopname van het selecteren van abonnementen](./media/manage-billing-access/billing-select-subscriptions.png)

1. Selecteer **Facturen** en vervolgens **Toegang tot factuur**.

    ![Schermopname laat zien hoe u de toegang tot facturen kunt delegeren](./media/manage-billing-access/aa-optin01.png)

1. Selecteer **Aan** en opslaan.

    ![Schermopname laat aan-uit zien waarmee u de toegang tot facturen kunt delegeren](./media/manage-billing-access/aa-optinallow01.png)

De accountbeheerder kan de instellingen ook zo configureren dat facturen via e-mail worden verzonden. Raadpleeg [Uw factuur per e-mail ontvangen](download-azure-invoice-daily-usage-date.md) voor meer informatie.

## <a name="give-read-only-access-to-billing"></a>Alleen-lezen toegang verlenen tot facturering

Wijs de rol Facturering voor lezer toe aan iemand waarvoor alleen-lezen toegang tot de factureringsgegevens van het abonnement is vereist, maar niet de mogelijkheid om Azure-Services te beheren of te maken. Deze rol is geschikt voor gebruikers in een organisatie die verantwoordelijk zijn voor het beheer van financiën en kosten voor Azure-abonnementen.

De functie Facturering voor lezer is beschikbaar als preview-versie en biedt nog geen ondersteuning voor niet-globale clouds.

1. Meld u als accountbeheerder aan bij de [Azure-portal](https://portal.azure.com/),

1. Zoek naar **kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht in de Azure-portal](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. Selecteer in het linkerdeelvenster **Abonnementen**. Afhankelijk van de toegangsmogelijkheden moet u mogelijk een factureringsbereik en vervolgens **Abonnementen** selecteren.

    ![Schermopname van het selecteren van abonnementen](./media/manage-billing-access/billing-select-subscriptions.png)

1. Klik op **Toegangsbeheer (IAM)** .
1. Selecteer **Toevoegen** bovenaan de pagina.

    ![Schermopname van het klikken op Roltoewijzing toevoegen](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. Kies in de vervolgkeuzelijst **Rol** **Facturering voor lezer**.
1. Typ in het tekstvak **Selecteren** de naam of het e-mailadres van de gebruiker die u wilt toevoegen.
1. Selecteer de gebruiker.
1. Selecteer **Opslaan**.
    ![Schermopname met de knop Opslaan gemarkeerd.](./media/manage-billing-access/billing-save-role-assignment.png)

1. Na enkele ogenblikken wordt aan de gebruiker de rol Facturering voor lezer toegewezen voor het abonnement.

** Als u een EA-klant bent, kan een accounteigenaar of afdelingsbeheerder de rol Facturering voor lezer toewijzen aan teamleden. Maar om voor die Facturering voor lezer de factureringsgegevens voor de afdeling of het account weer te geven, moet de Enterprise-beheerder **AO kosten weergeven** of **DA kosten weergeven** in het Enterprise-portal activeren.

## <a name="check-the-type-of-your-billing-account"></a>Controleer het type van uw factureringsaccount
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>Volgende stappen

- Gebruikers met andere rollen, zoals een Eigenaar of Inzender, hebben niet alleen toegang tot factureringsgegevens, maar ook tot Azure-services. Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure Portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie over het beheren van deze rollen.
- Zie [Ingebouwde rollen in Azure](../../role-based-access-control/built-in-roles.md) voor meer informatie over rollen.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

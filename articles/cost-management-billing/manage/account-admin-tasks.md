---
title: Taken van accountbeheerders in de Azure-portal
description: In dit artikel wordt beschreven hoe u betalingsbewerkingen uitvoert in de Azure-portal
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: bd46e7b2f0713da67842def47dfeadc837027d8f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027965"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Taken van accountbeheerders in de Azure-portal

In dit artikel wordt uitgelegd hoe u de volgende taken uitvoert in de Azure-portal:
- De betalingswijzen voor uw abonnement beheren
- De bestedingslimiet voor uw abonnement verwijderen
- Tegoed toevoegen aan uw Azure in Open-abonnement

U moet de accountbeheerder zijn om deze taken te kunnen uitvoeren.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Naar de betalingswijzen voor uw abonnement navigeren

1. Meld u als accountbeheerder aan bij de Azure-portal.

1. Zoek naar **Cost Management en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering ](./media/account-admin-tasks/search-bar.png)

1. Selecteer het abonnement waar u de creditcard aan wilt toevoegen in de lijst **Mijn abonnementen**.

   ![Schermopname van de pagina Kostenbeheer en facturering waar u een abonnement kunt selecteren.](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Als u sommige van uw abonnementen hier niet ziet, kan het zijn dat u de map voor het abonnement op een bepaald moment hebt gewijzigd. Voor deze abonnementen moet u weer de oorspronkelijke map instellen (de map waarin u zich voor het eerst hebt geregistreerd). Herhaal vervolgens stap 2.

1. Selecteer **Betalingswijzen**.

    ![Schermopname van de pagina Betalingswijzen waar u een betalingswijze kunt toevoegen.](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Hier kunt u een nieuwe creditcard toevoegen, de actieve betalingswijze wijzigen, creditcardgegevens bewerken en creditcards verwijderen.

### <a name="change-active-payment-method"></a>Actieve betalingswijze wijzigen

U kunt de actieve betalingswijze wijzigen door een nieuwe creditcard toe te voegen of door een creditcard te kiezen die al is opgeslagen. Als u een nieuwe creditcard wilt toevoegen om uw actieve betalingswijze te wijzigen, gaat u als volgt te werk:

1. Selecteer '+' in de linkerbovenhoek om een creditcard toe te voegen.

    ![Schermopname met het plusteken](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Voer de creditcardgegevens in op het formulier aan de rechterkant.

    ![Schermopname met het creditcardformulier.](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Schakel het selectievakje naast **Instellen als mijn actieve betalingswijze** boven het formulier in zodat deze creditcard de actieve betalingswijze wordt. Deze kaart wordt de actieve betalingswijze voor alle abonnementen waarvoor dezelfde kaart wordt gebruikt als voor het geselecteerde abonnement.

    ![Schermopname van het selectievakje waarmee een kaart kan worden ingesteld als de actieve betalingswijze.](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Selecteer **Volgende**.

Als u de creditcard die al is opgeslagen, wilt toevoegen om uw actieve betalingswijze te wijzigen, gaat u als volgt te werk:

1. Selecteer het vakje naast de creditcard waarvan u de actieve betalingswijze wilt maken.

    ![Schermopname waarin het selectievakje naast een creditcard is ingeschakeld](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Klik in de opdrachtbalk op **Als actief instellen**.

    ![Schermopname waarin de knop Als actief instellen is geselecteerd](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Creditcardgegevens bewerken

Als u creditcardgegevens wilt bewerken, zoals de vervaldatum of het adres, klikt u op de creditcard die u wilt bewerken. Een creditcardformulier wordt aan de rechterkant weergegeven.

![Schermopname waarin een creditcard is geselecteerd](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Werk de creditcardgegevens bij en klik op **Opslaan**.

### <a name="remove-a-credit-card-from-the-account"></a>Een creditcard verwijderen van het account

1. Selecteer het vakje naast de creditcard die u wilt verwijderen.

    ![Schermopname waarin het selectievakje naast een creditcard is ingeschakeld](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Klik in de opdrachtbalk op **Verwijderen**.

    ![Schermopname waarin de knop Verwijderen is geselecteerd](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Als uw creditcard de actieve betalingswijze is voor een van uw Microsoft-abonnementen, kunt u deze niet verwijderen uit uw Azure-account. Wijzig de actieve betalingswijze voor alle abonnementen die aan deze creditcard zijn gekoppeld en probeer het opnieuw.

### <a name="switch-to-invoice-payment"></a>Overschakelen naar betalingen via factuur

Als u in aanmerking komt voor betalingen via factuur (cheque/overschrijving), kunt u uw abonnement overzetten naar betalingen per factuur (cheque/overschrijving) in de Azure-portal.

1. Selecteer **Betalen via factuur** in de opdrachtbalk.

    ![Schermopname van de pagina Betalingswijzen met Betalen via factuur geselecteerd.](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Voer het adres in voor de factuurbetalingswijze.
1. Klik op **Volgende**.

Als u wilt worden goedgekeurd om te betalen via factuur, raadpleegt u [Per factuur betalen voor uw Azure-abonnement](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Adres voor betaling via factuur bewerken

Als u het adres voor uw factuurbetalingswijze wilt bewerken, klikt u op **Factuur** in de lijst met betalingswijzen voor uw abonnement. Het adresformulier wordt aan de rechterkant geopend.

## <a name="remove-spending-limit"></a>Bestedingslimiet verwijderen

Met de bestedingslimiet in Azure voorkomt u dat u uw tegoed overschrijdt. U kunt de bestedingslimiet op elk gewenst moment verwijderen zolang er een geldige betaalmethode aan uw Azure-abonnement is gekoppeld. Voor abonnementstypen met tegoeden die zijn verspreid over meerdere maanden, zoals Visual Studio Enterprise en Visual Studio Professional, kunt u er ook voor kiezen om de bestedingslimiet opnieuw in te schakelen aan het begin van uw volgende factureringsperiode.

De bestedingslimiet is niet beschikbaar voor abonnementen met toezeggingsplannen en tarieven voor betalen per gebruik.

1. Meld u als accountbeheerder aan bij Azure Portal.
1. Zoek naar **Cost Management en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering ](./media/account-admin-tasks/search-bar.png)

1. Selecteer uw Visual Studio Enterprise-abonnement in de lijst **Mijn abonnementen**.

   ![Schermopname van het gebied Mijn abonnementen waar u uw Visual Studio Enterprise-abonnement kunt kiezen.](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Als u sommige van uw Visual Studio-abonnementen hier niet ziet, kan het zijn dat u de map voor een abonnement op een bepaald moment hebt gewijzigd. Voor deze abonnementen moet u weer de oorspronkelijke map instellen (de map waarin u zich voor het eerst hebt geregistreerd). Herhaal vervolgens stap 2.

1. Klik in het abonnementsoverzicht op de oranje banner om de bestedingslimiet te verwijderen.

    ![Schermopname met banner voor het verwijderen van de bestedingslimiet](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Geef aan of u de bestedingslimiet voor onbepaalde tijd wilt verwijderen of alleen voor de huidige factureringsperiode.

   ![Schermopname met blade voor het verwijderen van de bestedingslimiet](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Klik op **Betalingswijze selecteren** om een betalingswijze te kiezen voor uw abonnement. Dit wordt de actieve betalingswijze voor uw abonnement.

1. Klik op **Voltooien**.

## <a name="add-credits-to-azure-in-open-subscription"></a>Tegoed toevoegen aan een Azure in Open-abonnement

Als u een abonnement voor Azure in Open Licensing hebt, kunt u in de Azure-portal tegoed aan uw abonnement toevoegen door een productcode in te wisselen of tegoed te kopen met een creditcard.

1. Meld u als accountbeheerder aan bij de Azure-portal.
1. Zoek naar **Cost Management en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering ](./media/account-admin-tasks/search-bar.png)

1. Selecteer uw Azure in Open-abonnement in de lijst **Mijn abonnementen**.

    ![Schermopname van het gebied Mijn abonnementen waar u uw Azure in Open-abonnement kunt kiezen.](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Als u uw abonnement hier niet ziet, kan het zijn dat u de map voor het abonnement op een bepaald moment hebt gewijzigd. U moet weer de oorspronkelijke map voor het abonnement instellen (de map waarin u zich voor het eerst hebt geregistreerd). Herhaal vervolgens stap 2.

1. Selecteer **Tegoedgeschiedenis**.

    ![Schermopname waarop Tegoedgeschiedenis is geselecteerd](./media/account-admin-tasks/aio-credit-history-blade.png)

1. Selecteer '+' in de linkerbovenhoek om meer tegoed toe te voegen.

    ![Schermopname met de knop Tegoed toevoegen](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Selecteer een type betalingswijze in de vervolgkeuzelijst. U kunt een productcode toevoegen of tegoed kopen met een creditcard.

    ![Schermopname met de vervolgkeuzelijst voor de betalingswijze in de blade Tegoed toevoegen](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Als u voor Productcode hebt gekozen, doet u het volgende:
    - Voer de productcode in.
    - Klik op **Valideren**.

1. Als u voor Creditcard hebt gekozen, doet u het volgende:
    - Klik op **Betalingswijze selecteren** om een creditcard toe te voegen of een bestaande creditcard te selecteren.
    - Geef aan hoeveel tegoed u wilt toevoegen.

1. Klik op **Toepassen**.

## <a name="troubleshooting"></a>Problemen oplossen
We bieden geen ondersteuning voor virtuele of prepaidkaarten. Als u fouten krijgt bij het toevoegen of bijwerken van een geldige creditcard, probeer dan uw browser te openen in de privémodus.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het analyseren van onverwachte kosten](../understand/analyze-unexpected-charges.md)

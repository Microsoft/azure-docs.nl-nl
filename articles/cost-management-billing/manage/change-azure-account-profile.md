---
title: Contactgegevens voor een Azure-factureringsaccount wijzigen
description: Beschrijft hoe u de contactgegevens voor uw Azure-factureringsaccount kunt wijzigen
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 04/08/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: f394b6b44b2030253f7b78ec68459819c82c3c27
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480899"
---
# <a name="change-contact-information-for-an-azure-billing-account"></a>Contactgegevens voor een Azure-factureringsaccount wijzigen

Dit artikel helpt u bij het bijwerken van de contactgegevens voor een *factureringsaccount* in Azure Portal. De instructies voor het bijwerken van de contactgegevens variëren per type naargelang het type factureringsaccount. Zie [Uw factureringsrekeningen weergeven in de Azure-portal](view-all-accounts.md) voor meer informatie over factureringsrekeningen en het bepalen van uw type factureringsrekening. Een Azure-facturerings account is gescheiden van uw Azure-gebruikersaccount en [Microsoft-account](https://account.microsoft.com/).

Als u de gegevens van uw Azure Active Directory-gebruikersprofiel wilt bijwerken, kan alleen een gebruikersbeheerder de wijzigingen aanbrengen. Neem contact op met de gebruikersbeheerder als u zelf niet de gebruikersbeheerdersrol hebt. Zie [Profielgegevens van een gebruiker toevoegen of bijwerken met behulp van Azure Active Directory](../../active-directory/fundamentals/active-directory-users-profile-azure-portal.md) voor meer informatie over het wijzigen van het profiel van een gebruiker.

*Verkoopadres*: het verkoopadres is het adres en de contactinformatie van de organisatie of de persoon die verantwoordelijk is voor een factureringsaccount. Dit wordt weergegeven op alle facturen die gegenereerd worden voor het factureringsaccount.

*Factureringsadres*: het factureringsadres is het adres en de contactinformatie van de organisatie of de persoon die verantwoordelijk is voor facturen die gegenereerd worden voor een factureringsaccount. Een factureringsaccount voor een Microsoft Online Subscription-programma (MOSP) heeft één factureringsadres dat wordt weergegeven op alle facturen die gegenereerd worden voor de rekening. Een factureringsaccount voor een Microsoft-klantovereenkomst (MCA) heeft een factureringsadres voor elk factureringsprofiel. Dit wordt weergegeven op facturen die gegenereerd worden voor dat factureringsprofiel.

*E-mailadres voor service- en marketinge-mails*: u kunt een e-mailadres opgeven dat verschilt van datgene waarmee u zich aanmeldt om belangrijke meldingen omtrent facturering, service en aanbevelingen over uw Azure-account te ontvangen. Servicemeldingen, bijvoorbeeld over urgente beveiligingsproblemen, prijsveranderingen of wijzigingen in door uw account gebruikte services die fouten veroorzaken, worden altijd naar uw aanmeldingsadres verzonden.

## <a name="update-an-mosp-billing-account-address"></a>Een MOSP-factureringsadres bijwerken

1. Meld u aan bij het Azure-portal met het e-mailadres dat de beheerdersmachtigingen voor het account heeft.
1. Zoek naar **Kostenbeheer en facturering**.  
    ![Schermopname die laat zien waar u kunt zoeken naar kostenbeheer en facturering in Azure Portal](./media/change-azure-account-profile/search-cmb.png)
1. Selecteer **Eigenschappen** aan de linkerkant.  
    ![Schermopname van MOSP-factureringsaccounteigenschappen](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Selecteer **Factureringsadres bijwerken** om het verkoopadres en het factureringsadres bij te werken. Voer het nieuwe adres in en selecteer vervolgens **Opslaan**.  
    ![Schermopname van het bijwerken van het adres voor het MOSP-factureringsaccount](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Een verkoopadres van een MCA-factureringsaccount bijwerken

1. Meld u aan bij het Azure-portal met het e-mailadres dat geregistreerd staat als eigenaar of bijdrager voor de factureringsaccount voor een Microsoft-klantovereenkomst.
1. Zoek naar **Kostenbeheer en facturering**.  
    ![Schermopname die laat zien waar u kunt zoeken in Azure Portal](./media/change-azure-account-profile/search-cmb.png)
1. Selecteer **Eigenschappen** aan de linkerkant en selecteer vervolgens **Verkocht-aan bijwerken**.  
    ![Schermopname die de eigenschappen laat zien voor een MCA-factureringsaccount waar het 'verkocht aan'-adres kan worden gewijzigd](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Voer het nieuwe adres in en selecteer **Opslaan**.  
    ![Schermopname van het bijwerken van het 'verkocht aan'-adres voor een MCA-account](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Voor sommige accounts is aanvullende verificatie nodig voor de verkocht-aan kan worden bijgewerkt. Als er handmatige goedkeuring nodig is voor uw account, dan wordt u gevraagd om contact op te nemen met Azure-ondersteuning.

## <a name="update-an-mca-billing-account-address"></a>Een MCA-factureringsadres bijwerken

1. Meld u aan bij het Azure-portal met het e-mailadres dat geregistreerd staat als eigenaar of bijdrager voor de factureringsaccount of factureringsprofiel voor een MCA.
1. Zoek naar **Kostenbeheer en facturering**.  
1. Selecteer aan de linkerkant de optie **Factureringsprofielen**.
1. Selecteer een factureringsprofiel om het factureringsadres bij te werken.  
    ![Schermopname van de pagina Factureringsprofielen waar u een factureringsprofiel selecteert](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Selecteer **Eigenschappen** aan de linkerkant.
1. Selecteer **Adres bijwerken**.  
    ![Schermopname die laat zien waar het adres wordt bijgewerkt](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Voer het nieuwe adres in en selecteer vervolgens **Opslaan**.  
    ![Schermopname van het bijwerken van het adres](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="update-a-po-number"></a>Een po-nummer bijwerken

Een factuur voor het factureringsprofiel heeft standaard geen bijbehorend po-nummer. Nadat u een po-nummer voor een factureringsprofiel hebt toevoegen, wordt dit weergegeven op facturen voor het factureringsprofiel.

Gebruik de volgende stappen om het po-nummer voor een factureringsprofiel toe te voegen of te wijzigen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Cost Management + Billing** selecteer vervolgens **Factureringsbereiken.**
1. Selecteer uw factureringsbereik.
1. Selecteer in het menu links **onder Facturering** de optie **Factureringsprofielen.**
1. Selecteer het juiste factureringsprofiel.
1. Selecteer eigenschappen in het menu links **onder** **Instellingen.**
1. Selecteer **Po-nummer bijwerken.**
1. Voer een po-nummer in en selecteer **bijwerken.**

## <a name="service-and-marketing-emails"></a>E-mail voor service en marketing

U wordt gevraagd om uw e-mailadres om de 90 dagen te controleren of bij te werken in de Azure-portal. Microsoft stuurt e-mails naar dit e-mailadres met informatie over het Azure-account voor:

- Servicemeldingen
- Beveiligingswaarschuwingen
- Factureringsdoeleinden
- Ondersteuning
- Marketingberichten
- Aanbevelingen best practices, op basis van uw Azure-gebruik

Voer het e-mail adres in waarop u communicatie over uw account wilt ontvangen. Door een e-mailadres in te voeren, stemt u in met het ontvangen van berichten van Microsoft.

![Voorbeeld van de vraag om uw contactgegevens bij te werken](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>Uw e-mailadres voor contactpersonen wijzigen

U kunt uw e-mailadres voor contactpersonen wijzigen met behulp van een van de volgende methoden. Als u het e-mailadres van uw contactpersoon bijwerkt, wordt het e-mailadres waarmee u zich aanmeldt niet automatisch mee bijgewerkt.

1. Als u een accountbeheerder bent voor een MOSP-account, volg dan de instructies in [Het adres van een MOSP-factureringsaccount bijwerken](#update-an-mosp-billing-account-address) en selecteer **Contactgegevens bijwerken** in de laatste stap. Voer vervolgens het nieuwe e-mailadres in.
1. Ga naar het gedeelte [Contactgegevens](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) in het Azure-portal en voer het nieuwe e-mailadres in. 
1. Selecteer in de Azure-portal het pictogram met uw initialen of afbeelding. Selecteer vervolgens het contextmenu ( **...** ). Selecteer vervolgens **Mijn contactgegevens** uit het menu en voer het nieuwe e-mailadres in.

![Voorbeeld van het bijwerken van een e-mailadres in Azure](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Afmelden voor marketingberichten

Ga als volgt te werk om geen marketingberichten meer te ontvangen:

1. Ga naar het [aanvraagformulier](https://account.microsoft.com/profile/permissions-link-request) om een aanvraag in te dienen via het e-mailadres van uw profiel. U ontvangt een koppeling via e-mail om uw voorkeuren bij te werken.
1. Selecteer de koppeling om de pagina **Communicatiemachtigingen beheren** te openen. Op deze pagina ziet u de soorten marketingcommunicatie waarvoor het e-mailadres is aangemeld. Wis de selecties waarvoor u zich wilt afmelden en selecteer vervolgens **Opslaan.**  
    ![Voorbeeld van de pagina om communicatiemachtigingen te beheren](./media/change-azure-account-profile/manage-communication-permissions.png)

Wanneer u zich afmeldt voor marketingberichten, ontvangt u nog steeds servicemeldingen op basis van uw account.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Het e-mailadres waarmee u zich aanmeldt bijwerken

U kunt het e-mailadres dat u gebruikt om uw account te openen niet bijwerken. Als u een factureringsaccount hebt voor een MOSP, dan kunt u zich aanmelden voor een ander account met het nieuwe e-mailadres en de eigendom van uw abonnementen overzetten op het nieuwe account. Voor een MCA-factureringsaccount kunt u het nieuwe e-mailadres machtigingen gegeven voor uw account.

## <a name="update-your-credit-card"></a>Uw creditcard bijwerken

Zie [De creditcard wijzigen die wordt gebruikt om een Azure-abonnement te betalen](change-credit-card.md) voor meer informatie over het bijwerken van uw creditcard.

## <a name="update-your-country-or-region"></a>Uw land of regio bijwerken

Het land of de regio wijzigen voor een bestaand account wordt niet ondersteund. U kunt echter wel een nieuw account maken in een ander land of andere regio en vervolgens contact opnemen met de ondersteuning van Azure om uw abonnement over te zetten op het nieuwe account.

## <a name="change-the-subscription-name"></a>De naam van het abonnement wijzigen

1. Meld u aan bij Azure Portal, selecteer **Abonnement** in het linkerdeelvenster en selecteer vervolgens het abonnement waarvan u de naam wilt wijzigen.
1. Selecteer **Overzicht** en selecteer vervolgens **Naam wijzigen** in de opdrachtbalk.  
    ![Voorbeeld van het wijzigen van de naam van een Azure-abonnement](./media/change-azure-account-profile/rename-sub.png)
1. Selecteer **Opslaan** nadat u de naam hebt gewijzigd.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Uw factureringsaccounts weergeven](view-all-accounts.md)

---
title: Uw creditcard wijzigen voor Azure
description: Hierin wordt beschreven hoe u de creditcard wijzigt die wordt gebruikt voor het betalen van een Azure-abonnement.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/20/2020
ms.author: banders
ms.openlocfilehash: fbb69a4449c32f85cc4be438645b654608aa7489
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026553"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Een creditcard toevoegen of bijwerken voor Azure

Dit document is van toepassing op klanten die zich online met een creditcard hebben geregistreerd voor Azure.

In de Azure Portal kunt u uw standaardbetalingswijze wijzigen in een nieuwe creditcard en uw creditcardgegevens bijwerken. U moet een [accountbeheerder](../understand/subscription-transfer.md#whoisaa) zijn of u moet de juiste [MCA-machtigingen](understand-mca-roles.md) hebben om deze wijzigingen door te voeren.

Als u een creditcard wilt verwijderen, raadpleegt u [Een betalingswijze voor Azure-facturering verwijderen](delete-azure-payment-method.md).

De ondersteunde betalingswijzen voor Microsoft Azure zijn creditcards en overschrijving per bank of cheque. Zie [Betalen voor Azure-abonnementen per factuur](pay-by-invoice.md) om goedkeuring voor betaling via cheque of overschrijving te krijgen.

Als u een Microsoft-klantovereenkomst hebt, zijn uw betalingswijzen gekoppeld aan factureringsprofielen. [Toegang tot een Microsoft-klantovereenkomst controleren](#check-the-type-of-your-account). Als u een Microsoft-klantovereenkomst hebt, gaat u naar [Creditcards beheren voor een Microsoft-klantovereenkomst](#manage-credit-cards-for-a-microsoft-customer-agreement).

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Creditcard beheren voor een Azure-abonnement

De volgende secties zijn bedoeld voor klanten die een factureringsrekening voor Microsoft Online Services hebben. [Uw type factureringsrekening controleren](#check-the-type-of-your-account). Als uw type factureringsrekening het Microsoft Online Services-programma is, worden betalingswijzen gekoppeld aan afzonderlijke Azure-abonnementen. Als u een foutmelding krijgt nadat u de creditcard hebt toegevoegd, raadpleegt u [Creditcard is geweigerd tijdens de registratie](./troubleshoot-declined-card.md).

### <a name="change-credit-card-for-a-subscription-by-adding-a-new-credit-card"></a>Creditcard voor een abonnement wijzigen door een nieuwe creditcard toe te voegen

U kunt het standaardtegoed van uw Azure-abonnement wijzigen in een nieuwe creditcard of eerder opgeslagen creditcard in de Azure-portal. U moet de accountbeheerder zijn om de creditcard te kunnen wijzigen. Als u meerdere abonnementen heeft met dezelfde actieve betalingswijze, wordt de actieve betalingswijze van al deze abonnementen bijgewerkt wanneer de betalingswijze van één van de abonnementen wordt gewijzigd.

U kunt de standaardcreditcard van uw abonnement als volgt vervangen door een andere:

1. Meld u als accountbeheerder aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.  
    ![Schermopname van de zoekopdracht](./media/change-credit-card/search.png)
1. Selecteer het abonnement waar u de creditcard aan wilt toevoegen.
1. Selecteer **Betalingswijzen**.  
    ](./media/change-credit-card/payment-methods-blade-x.png)Schermopname waarin de optie voor 'Betalingswijzen beheren' geselecteerd is![
1. Selecteer '+' In de linkerbovenhoek om een kaart toe te voegen Een creditcardformulier wordt aan de rechterkant weergegeven.
1. Voer de creditcardgegevens in.  
    ![Schermopname van het toevoegen van een nieuwe creditcard](./media/change-credit-card/sub-add-new-x.png)
1. Schakel het selectievakje naast **Instellen als mijn actieve betalingswijze** boven het formulier in zodat deze creditcard de actieve betalingswijze wordt. Deze kaart wordt de actieve betalingswijze voor alle abonnementen waarvoor dezelfde kaart wordt gebruikt als voor het geselecteerde abonnement.
1. Selecteer **Next**.

### <a name="change-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Creditcard voor een abonnement wijzigen in een eerder opgeslagen creditcard

U kunt ook de standaardcreditcard van uw abonnement wijzigen in een creditcard die al in uw account is opgeslagen door de volgende stappen uit te voeren:

1. Meld u als accountbeheerder aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.  
    ![Schermopname van de zoekopdracht](./media/change-credit-card/search.png)
1. Selecteer het abonnement waar u de creditcard aan wilt toevoegen.
1. Selecteer **Betalingswijzen**.
    ](./media/change-credit-card/payment-methods-blade-x.png)Schermopname waarin de optie voor 'Betalingswijzen beheren' geselecteerd is![
1. Selecteer het vakje naast de creditcard waarvan u de actieve betalingswijze wilt maken.
1. Klik op **Als actief instellen**.
    ![Schermopname van een geselecteerde creditcard die is ingesteld als actief](./media/change-credit-card/sub-change-active-x.png)

### <a name="edit-credit-card-details"></a>Creditcardgegevens bewerken

Als uw creditcard wordt vernieuwd en het nummer blijft hetzelfde, werkt u de bestaande creditcardgegevens bij , zoals de verloopdatum. Als uw creditcardnummer is gewijzigd, omdat de creditcard is verloren, gestolen of verlopen, volgt u de stappen in de sectie [Een creditcard toevoegen als betalingswijze](#addcard). U hoeft de CVV niet bij te werken.

1. Meld u als accountbeheerder aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.
    ![Schermopname van de zoekopdracht](./media/change-credit-card/search.png)
1. Selecteer **Betalingswijzen**.
    ](./media/change-credit-card/payment-methods-blade-x.png)Schermopname waarin de optie voor 'Betalingswijzen beheren' geselecteerd is![
1. Klik op de creditcard die u wilt bewerken. Een creditcardformulier wordt aan de rechterkant weergegeven.
    ![Schermopname waarin een creditcard is geselecteerd](./media/change-credit-card/edit-card-x.png)
1. Werk de creditcardgegevens bij.
1. Selecteer **Opslaan**.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Creditcards voor een Microsoft-klantovereenkomst beheren

De volgende secties zijn bedoeld voor klanten die een Microsoft-klantovereenkomst hebben en zich hebben geregistreerd voor Azure online met een creditcard of die de correcte [MCA-machtigingen](understand-mca-roles.md) hebben. [Controleren of u toegang hebt tot een Microsoft-klantovereenkomst](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Standaardcreditcard wijzigen

Als u een Microsoft-klantovereenkomst hebt, is uw creditcard gekoppeld aan een factureringsprofiel. Als u de betalingswijze voor een factureringsprofiel wilt wijzigen, moet u de persoon zijn die zich heeft geregistreerd bij Azure en de factureringsrekening heeft gemaakt of u moet de correcte [MCA-machtigingen](understand-mca-roles.md) hebben.

Zie [Azure-abonnementen betalen per factuur](pay-by-invoice.md) als u de standaard betalingswijze van uw factureringsprofiel wilt wijzigen in betalen per cheque of overschrijving.

Voer de volgende stappen uit om uw creditcard te wijzigen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **kostenbeheer en facturering**.
1. Klik in het menu aan de linkerkant op **Factureringsprofielen**.
1. Selecteer een factureringsprofiel.
1. Selecteer in het menu aan de linkerkant de optie **Betalingsmethoden**.  
   ![Schermopname van de betalingsmethoden in het menu](./media/change-credit-card/payment-methods-tab-mca.png)
1. Klik in de sectie **Standaard betalingswijze** op **Vervangen**.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="Schermopname met de optie Vervangen" :::
1. In het nieuwe gebied aan de rechterkant selecteert u een bestaande creditcard in de vervolgkeuzelijst of voegt u een nieuwe creditcard toe door op de blauwe link **Nieuwe betalingswijze toevoegen** te klikken.

### <a name="edit-a-credit-card"></a>Een creditcard bewerken

U kunt creditcardgegevens bewerken (zoals het bijwerken van de vervaldatum) in de Azure Portal. 

Volg de volgende stappen om een creditcard te bewerken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **kostenbeheer en facturering**.
1. Klik in het menu aan de linkerkant op **Factureringsprofielen**.
1. Selecteer een factureringsprofiel.
1. Selecteer in het menu aan de linkerkant de optie **Betalingsmethoden**.  
   ![Schermopname van de betalingsmethoden in het menu](./media/change-credit-card/payment-methods-tab-mca.png)
1. Ga in de sectie **Uw creditcards** naar de creditcard die u wilt bewerken.
1. Selecteer het beletselteken (`...`) aan het einde van de rij.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="Schermopname met het beletselteken" :::
1. Als u de details van uw creditcard wilt bewerken, selecteert u **Bewerken** in het contextmenu.

## <a name="troubleshooting"></a>Problemen oplossen

Azure biedt geen ondersteuning voor virtuele of prepaidkaarten. Als u fouten krijgt bij het toevoegen of bijwerken van een geldige creditcard, probeer dan uw browser te openen in de privémodus.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

In de volgende secties worden veelgestelde vragen beantwoord over het wijzigen van uw creditcardgegevens.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Waarom zie ik steeds: Uw aanmeldingssessie is verlopen. Klik hier om u opnieuw aan te melden?

Als u dit foutbericht blijft ontvangen, zelfs als u zich al hebt afgemeld en weer aangemeld, probeert u het opnieuw met een privébrowsersessie.

### <a name="how-do-i-use-a-different-card-for-each-subscription-i-have"></a>Hoe kan ik een andere creditcard gebruiken voor elk abonnement dat ik heb?

Als uw abonnementen al gebruikmaken van dezelfde creditcard, is het niet meer mogelijk ze te scheiden om verschillende creditcards te gebruiken. Als u zich echter aanmeldt voor een nieuw abonnement, kunt u ervoor kiezen om een nieuwe betalingswijze voor dit abonnement te gebruiken.

### <a name="how-do-i-make-payments"></a>Hoe kan ik betalingen doen?

Als u een creditcard instelt als betalingswijze, worden uw kosten na elke factureringsperiode automatisch in rekening gebracht bij uw creditcard. U hoeft hiervoor niets te doen.

Als u [betaalt via een factuur](pay-by-invoice.md), maakt u het verschuldigde bedrag over naar de rekening die onder aan de factuur wordt vermeld.

### <a name="how-do-i-change-the-tax-id"></a>Hoe kan ik het btw-nummer wijzigen?

Als u een btw-nummer wilt toevoegen of bijwerken, werkt u uw profiel bij in de [Azure-portal](https://portal.azure.com) en selecteert u vervolgens **Btw-registratie**. Dit btw-nummer wordt gebruikt voor de berekening van btw-vrijstelling en wordt vermeld op uw factuur.

## <a name="check-the-type-of-your-account"></a>Controleer uw accounttype

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- Krijg meer informatie over [Azure-reserveringen](../reservations/save-compute-costs-reservations.md) om te zien of ze geld kunnen besparen.
- Als u een creditcard wilt verwijderen, raadpleegt u [Een betalingswijze voor Azure-facturering verwijderen](delete-azure-payment-method.md).
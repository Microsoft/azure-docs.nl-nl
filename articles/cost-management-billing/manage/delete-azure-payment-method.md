---
title: Een betalingswijze voor Azure-facturering verwijderen
description: Hierin wordt beschreven hoe u een betalingswijze verwijdert die wordt gebruikt door een Azure-abonnement.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/07/2020
ms.author: banders
ms.openlocfilehash: 15d6c7731b541de638ceaf7828a7ce962cbf154a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827549"
---
# <a name="delete-an-azure-billing-payment-method"></a>Een betalingswijze voor Azure-facturering verwijderen

Dit document bevat instructies om u te helpen bij het verwijderen van een betalingswijze, zoals een creditcard, uit verschillende typen Azure-abonnementen. U kunt een betalingswijze verwijderen voor:

- Microsoft-klantovereenkomst (MCA)
- Microsoft Online Services-programma (MOSP), ook wel betalen per gebruik genoemd

Welk type Azure-abonnement u ook hebt, u moet het opzeggen zodat u de bijbehorende betalingswijze kunt verwijderen.

Het verwijderen van een betalingswijze voor andere typen Azure-abonnementen, zoals Microsoft Partner-overeenkomst en Enterprise Agreement, wordt niet ondersteund.

## <a name="delete-an-mca-payment-method"></a>Een MCA-betalingswijze verwijderen

Alleen de gebruiker die het account van de Microsoft-klantovereenkomst heeft gemaakt, kan een betalingswijze verwijderen.

Voer de volgende stappen uit om een betalingswijze voor een Microsoft-klantovereenkomst te verwijderen.

1. Meld u aan bij Azure Portal op https://portal.azure.com/.
1. Navigeer naar **Cost Management en facturering**.
1. Selecteer zo nodig een factureringsbereik.
1. Selecteer **Factureringsprofielen** onder **Facturering** in de menulijst aan de linkerkant.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Voorbeeld van een schermopname met factureringsprofielen in Azure Portal" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. Selecteer in de lijst met factureringsprofielen het factureringprofiel waarvoor de betalingswijze wordt gebruikt.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Voorbeeldafbeelding van de lijst met factureringsprofielen" :::
1. Selecteer **Betalingswijzen** onder **Instellingen** in de menulijst aan de linkerkant.
1. Op de pagina met betalingswijzen voor uw factureringsprofiel wordt een tabel met betalingswijzen weergegeven in de sectie **Uw creditcards** . Zoek de creditcard die u wilt verwijderen, selecteer het weglatingsteken ( **...** ) en selecteer vervolgens **Verwijderen**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Voorbeeld van het verwijderen van een creditcard" :::
1. De pagina Betalingswijze verwijderen wordt weergegeven. Er wordt gecontroleerd of de betalingswijze in gebruik is.
    - Wanneer de betalingswijze niet wordt gebruikt, is de optie **Verwijderen** ingeschakeld. Selecteer deze optie om de creditcardgegevens te verwijderen.
    - Als de betalingswijze wel wordt gebruikt, moet deze worden vervangen of losgekoppeld. Ga door met het lezen van de volgende secties. Daarin wordt uitgelegd hoe u de betalingswijze die wordt gebruikt door uw abonnement, kunt **loskoppelen**.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Betalingswijze die wordt gebruikt door een MCA-factureringsprofiel loskoppelen

Als uw betalingswijze wordt gebruikt door een MCA-factureringsprofiel, ziet u een bericht dat er ongeveer als volgt uitziet.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Voorbeeldafbeelding van een betalingswijze die wordt gebruikt door een Microsoft-klantovereenkomst" :::

Als u een betalingsmethode wilt loskoppelen, moet aan een lijst met voorwaarden worden voldaan. Als niet aan de voorwaarden wordt voldaan, worden instructies weergegeven waarin wordt uitgelegd wat u moet doen om wel aan de voorwaarde te voldoen. Er wordt ook een koppeling weergegeven waarmee u naar de locatie gaat waar u de voorwaarde kunt oplossen.

Wanneer aan alle voorwaarden wordt voldaan, kunt u de betalingswijze loskoppelen van het factureringsprofiel.

> [!NOTE]
> Wanneer de standaardbetalingswijze wordt losgekoppeld, krijgt het factureringsprofiel de status _Inactief_. Alles wat in dit proces wordt verwijderd, kan niet meer worden hersteld. Als een factureringsprofiel is ingesteld op inactief, moet u zich aanmelden voor een nieuw Azure-abonnement om nieuwe resources te maken.

#### <a name="to-detach-a-payment-method"></a>Een betalingswijze loskoppelen

1. Selecteer de koppeling **De huidige betalingswijze loskoppelen** in het gebied Betalingswijze verwijderen.
1. Als aan alle voorwaarden wordt voldaan, selecteert u **Loskoppelen**. Anders gaat u verder met de volgende stap.
1. Als de optie Loskoppelen niet beschikbaar is, wordt er een lijst met voorwaarden weergegeven. Voer de weergegeven acties uit. Selecteer de koppeling die wordt weergegeven in het gebied De standaardbetalingswijze loskoppelen. Hier volgt een voorbeeld van een herstelactie waarin wordt uitgelegd wat u moet doen.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Voorbeeld van een herstelactie die nodig is om een betalingswijze voor MCA los te koppelen" :::
1. Wanneer u op de koppeling naar de hestelactie klikt, wordt u omgeleid naar de Azure-pagina waar u de actie moet uitvoeren. Voer de benodigde herstelactie(s) uit.
1. Voer alle overige herstelacties uit, indien aanwezig.
1. Ga terug naar **Cost Management + Billing** > **Factureringsprofielen** > **Betalingswijzen**. Selecteer **Loskoppelen**. Selecteer **Loskoppelen** onder aan de pagina De standaardbetalingswijze loskoppelen.

> [!NOTE]
> - Als u een abonnement opzegt, kan het tot 90 dagen duren voordat het abonnement wordt verwijderd. Als u de wachttijd wilt verkorten, opent u een Azure-ondersteuningsaanvraag en dient u een verzoek in om het abonnement direct te verwijderen.
> - U kunt een betalingswijze alleen verwijderen als alle vorige kosten voor een factureringsprofiel zijn vereffend. Als u in een actieve factureringsperiode zit, moet u wachten tot de factureringsperiode is verstreken alvorens de betalingswijze te verwijderen. **Zorg ervoor dat aan alle andere voorwaarden voor het loskoppelen wordt voldaan terwijl u wacht totdat de factureringsperiode afloopt**.

## <a name="delete-a-mosp-payment-method"></a>Een MOSP-betalingswijze verwijderen

U moet een accountbeheerder zijn om een betalingswijze te kunnen verwijderen.

Als uw betalingswijze wordt gebruikt door een MOSP-abonnement, voert u de volgende stappen uit.

1. Meld u aan bij Azure Portal op https://portal.azure.com/.
1. Navigeer naar **Cost Management en facturering**.
1. Selecteer zo nodig een factureringsbereik.
1. Selecteer **Betalingswijzen** onder **Facturering** in de menulijst aan de linkerkant.
1. Selecteer in het gedeelte Betalingswijzen de _regel_ waarop uw betalingswijze wordt weergegeven. Selecteer niet de koppeling naar de betalingswijze. Er is mogelijk geen visuele indicatie dat u de betalingswijze hebt geselecteerd.
1. Selecteer **Verwijderen**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Voorbeeld van een herstelactie die nodig is om een betalingswijze voor MOSP los te koppelen" :::
1. Selecteer **Verwijderen** in het gebied Betalingswijze verwijderen als aan alle voorwaarden wordt voldaan. Als de optie Verwijderen niet beschikbaar is, gaat u verder met de volgende stap.
1. Er wordt een lijst met voorwaarden weergegeven. Voer de weergegeven acties uit. Selecteer de koppeling die wordt weergegeven in het gebied Betalingswijze loskoppelen.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Voorbeeldafbeelding van een betalingswijze die wordt gebruikt door een MOSP-abonnement" :::
1. Wanneer u op de koppeling naar de hestelactie klikt, wordt u omgeleid naar de Azure-pagina waar u de actie moet uitvoeren. Voer de benodigde herstelactie(s) uit.
1. Voer alle overige herstelacties uit, indien aanwezig.
1. Ga terug naar **Cost Management + Billing** > **Factureringsprofielen** > **Betalingswijzen** en verwijder de betalingswijze.

> [!NOTE]
> Als u een abonnement opzegt, kan het tot 90 dagen duren voordat het abonnement wordt verwijderd. Als u de wachttijd wilt verkorten, opent u een Azure-ondersteuningsaanvraag en dient u een verzoek in om het abonnement direct te verwijderen.

## <a name="next-steps"></a>Volgende stappen

- Als u meer informatie over het opzeggen van uw Azure-abonnement nodig hebt, raadpleegt u [Uw Azure-abonnement opzeggen](cancel-azure-subscription.md).
- Zie [Een creditcard toevoegen of bijwerken voor Azure](change-credit-card.md) voor meer informatie over het toevoegen of bijwerken van een creditcard.
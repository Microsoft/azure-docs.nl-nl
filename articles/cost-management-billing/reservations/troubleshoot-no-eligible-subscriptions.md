---
title: Problemen met niet in aanmerking komende abonnementen oplossen in de Azure Portal
description: Dit artikel helpt u bij het oplossen van problemen met het foutbericht Geen in aanmerking komende abonnementen in de Azure Portal wanneer u een reservering probeert aan te schaffen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.openlocfilehash: 1b36577c3c0940687f98394f8ea4faae83f371be
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94887192"
---
# <a name="troubleshoot-no-eligible-subscriptions"></a>Problemen met niet in aanmerking komende abonnementen oplossen

Dit artikel helpt u bij het oplossen van problemen met het foutbericht *Geen in aanmerking komende abonnementen* in de Azure Portal wanneer u een reservering probeert aan te schaffen.

## <a name="symptoms"></a>Symptomen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar **Reserveringen**.
1. Selecteer **Toevoegen** en selecteer vervolgens een service.
1. Het volgende foutbericht wordt weergegeven:
   ```
    No eligible subscriptions
    
    You do not have any eligible subscriptions to purchase reservations. To purchase a reservation, you should have owner or reservation purchaser permission on at least one subscription of the following type: Pay-as-you-go, CSP, Microsoft Enterprise or Microsoft Customer Agreement.
    ```
1. In het gebied **Selecteer het product dat u wilt kopen**, vouwt u de lijst **Factureringsabonnement** uit om te zien waarom een specifiek abonnement niet in aanmerking komt voor het kopen van een gereserveerde instantie. In de volgende afbeelding ziet u voorbeelden van waarom een reservering niet kan worden aangeschaft.  
    :::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" alt-text="Voorbeeld waarin wordt getoond waarom een reservering niet kan worden aangeschaft" lightbox="./media/troubleshoot-no-eligible-subscriptions/select-product-to-purchase.png" :::

## <a name="cause"></a>Oorzaak

Als u een gereserveerde instantie van Azure wilt kopen, moet u ten minste één abonnement hebben dat aan de volgende vereisten voldoet:

- Het abonnement moet een ondersteund aanbiedingstype zijn. Ondersteunde aanbiedingstypen zijn: Betalen per gebruik, Cloud Solution Provider (CSP), Microsoft Azure Enterprise of Microsoft-klantovereenkomst.
- U moet een eigenaar of inkoper van reserveringen van het abonnement zijn.

Als u geen abonnement hebt dat aan de vereisten voldoet, krijgt u de fout `No eligible subscriptions`.

### <a name="cause-1"></a>Oorzaak 1

Het abonnement moet een ondersteund aanbiedingstype zijn. Ondersteunde aanbiedingstypen zijn: Betalen naar gebruik, CSP, Microsoft Azure Enterprise of Microsoft-klantovereenkomst. Uw abonnementstype wordt niet ondersteund. Wanneer u een abonnement selecteert dat een aanbiedingstype heeft dat geen reserveringen ondersteunt, ziet u de volgende fout.

```
Subscription not eligible for purchase

This subscription is not eligible for reservation benefit an cannot be used to purchase a reservation.
```

:::image type="content" source="./media/troubleshoot-no-eligible-subscriptions/subscription-not-eligible.png" alt-text="Voorbeeld van het foutbericht Abonnement dat niet in aanmerking komt voor aanschaffen" :::

### <a name="cause-2"></a>Oorzaak 2

U moet een eigenaar of inkoper van reserveringen van het abonnement zijn. Wanneer u niet voldoende machtigingen hebt, krijgt u de volgende fout te zien.

```
You do not have owner or reservation purchaser access on the subscription

You can only purchase reservations using subscriptions on which you have owner or reservation purchaser access.
```

## <a name="solution"></a>Oplossing

- Als uw huidige aanbieding geen reserveringen ondersteunt, moet u een nieuw Azure-abonnement maken.
- Als u geen toegang hebt tot een bestaande reservering, kunt u er toegang toe krijgen via de huidige eigenaar.

### <a name="solution-1"></a>Oplossing 1

Als u een reservering wilt kopen, moet u een nieuw Azure-abonnement maken dat reserveringen ondersteunt.

- Als u een gratis proefversie van Azure hebt, kunt u [Uw abonnement upgraden](../manage/upgrade-azure-subscription.md).
- U kunt een nieuw Azure-abonnement maken met [tarieven voor betalen per gebruik](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).
- Meld u aan voor een [Microsoft-klantovereenkomst](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) en maak een nieuw abonnement.
- Schaf een nieuw abonnement aan met een [CSP](https://www.microsoft.com/solution-providers/home) en maak een nieuw abonnement.

### <a name="solution-2"></a>Oplossing 2

Als u eigenaarstoegang tot een reservering wilt krijgen, moet u toegang hebben tot:

- De reserveringsorder waarmee de reservering is aangeschaft
- De reservering zelf

De eigenaar van de huidige reserveringsorder of de eigenaar van de reservering kan de toegang aan u overdragen met behulp van de volgende stappen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Alle services** > **Reservering** om de reserveringen te bekijken waartoe u toegang hebt.
1. Selecteer de reservering waarvoor u de toegang wilt delegeren aan andere gebruikers.
1. Klik op **Toegangsbeheer (IAM)** .
1. Selecteer **Roltoewijzing toevoegen** > **Rol** > **Eigenaar**. Als u beperkte toegang wilt geven, selecteert u een andere rol.
1. Typ het e-mailadres van de gebruiker die u als eigenaar wilt toevoegen.
1. Selecteer de gebruiker en selecteer vervolgens **Opslaan**.

Zie [Gebruikers toevoegen of wijzigen die een reservering kunnen beheren](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Bekijk [Toevoegen of wijzigen van gebruikers die een reservering kunnen beheren](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation) als een reserveringseigenaar u toegang moet verlenen.

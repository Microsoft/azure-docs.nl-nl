---
title: Bestedingslimiet voor Azure
description: In dit artikel wordt beschreven hoe een bestedingslimiet van Azure werkt en hoe u deze kunt verwijderen.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.author: banders
ms.openlocfilehash: 47f1a1ed182420852682e908c7cd8b5e094bc085
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932553"
---
# <a name="azure-spending-limit"></a>Bestedingslimiet voor Azure

Met de bestedingslimiet in Azure voorkomt u dat u uw tegoed overschrijdt. Voor alle nieuwe klanten die zich aanmelden voor een gratis Azure-account of voor een abonnement met tegoeden verspreid over een aantal maanden, is de bestedingslimiet standaard ingeschakeld. De bestedings limiet is gelijk aan de hoeveelheid tegoed. U kunt het bedrag van de bestedings limiet niet wijzigen. Als u zich bijvoorbeeld hebt geregistreerd voor een gratis Azure-account, is uw bestedingslimiet $ 200 en kunt u deze niet wijzigen in $ 500. U kunt de bestedingslimiet echter wel verwijderen. U hebt dus geen limiet of u hebt een limiet die gelijk is aan de hoeveelheid van het tegoed. Hierdoor kunt u de meeste soorten uitgaven niet wijzigen. De bestedingslimiet is niet beschikbaar voor abonnementen met toezeggingsplannen en tarieven voor betalen per gebruik. Zie de [volledige lijst met Azure-abonnementstypen en de beschikbaarheid van de bestedingslimiet](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Een bestedingslimiet bereiken

Wanneer uw gebruik leidt tot kosten die hoger zijn dan uw bestedingslimiet, worden de services waarvan u gebruikt hebt gemaakt voor de rest van die factureringsperiode uitgeschakeld.

Als u bijvoorbeeld al het tegoed van uw gratis Azure-account besteedt, worden de Azure-resources die u hebt geïmplementeerd, verwijderd uit productie en worden uw virtuele Azure-machines gestopt en opnieuw toegewezen. De gegevens in uw opslagaccounts zijn beschikbaar als alleen-lezen.

Als uw type abonnement tegoeden heeft die verspreid zijn over meerdere maanden, wordt uw abonnement automatisch opnieuw ingeschakeld aan het begin van de volgende factureringsperiode. Vervolgens kunt u uw Azure-resources opnieuw implementeren en beschikt u over volledige toegang tot uw opslagaccounts en databases.

Azure verzendt e-mailmeldingen wanneer u de bestedingslimiet bereikt. Meld u aan bij de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) om meldingen te zien over abonnementen waarvan de bestedingslimiet is bereikt.

Als u een gratis Azure-account hebt en de bestedingslimiet bereikt, kunt u een upgrade uitvoeren naar tarieven voor [betalen per gebruik](upgrade-azure-subscription.md) om de bestedingslimiet te verwijderen en uw abonnement automatisch weer in te schakelen.

## <a name="remove-the-spending-limit-in-azure-portal"></a>De bestedingslimiet verwijderen in de Azure-portal

U kunt de bestedingslimiet op elk gewenst moment verwijderen zolang er een geldige betaalmethode aan uw Azure-abonnement is gekoppeld. Voor abonnementstypen met tegoeden die zijn verspreid over meerdere maanden, zoals Visual Studio Enterprise en Visual Studio Professional, kunt u er ook voor kiezen om de bestedingslimiet definitief te verwijderen of alleen voor de huidige factureringsperiode. Als u alleen voor de huidige factureringsperiode kiest, wordt de bestedingslimiet automatisch ingeschakeld aan het begin van de volgende factureringsperiode.

Als u een gratis Azure-account hebt, raadpleegt u [Uw Azure-abonnement upgraden](upgrade-azure-subscription.md) om uw bestedingslimiet te verwijderen. Ga anders als volgt te werk om de bestedingslimiet te verwijderen:

<a id="remove"></a>

1. Meld u als accountbeheerder aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Cost Management en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering ](./media/spending-limit/search-bar.png)

1. Selecteer uw abonnement in de lijst **Mijn abonnementen**. Selecteer bijvoorbeeld *Visual Studio Enterprise*.

   ![Schermopname van het overzicht van Mijn abonnementen](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Als u sommige van uw Visual Studio-abonnementen hier niet ziet, kan het zijn dat u de map voor een abonnement op een bepaald moment hebt gewijzigd. Voor deze abonnementen moet u weer de oorspronkelijke map instellen (de map waarin u zich voor het eerst hebt geregistreerd). Herhaal vervolgens stap 2.

1. Klik in het abonnementsoverzicht op de oranje banner om de bestedingslimiet te verwijderen.

    ![Schermopname met banner voor het verwijderen van de bestedingslimiet](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. Geef aan of u de bestedingslimiet voor onbepaalde tijd wilt verwijderen of alleen voor de huidige factureringsperiode.

      ![Schermopname met blade voor het verwijderen van de bestedingslimiet](./media/spending-limit/remove-spending-limit-blade-x.png)

      | Optie | Effect |
      | --- | --- |
      | Bestedingslimiet voor onbepaalde tijd verwijderen | De bestedingslimiet wordt niet automatisch weer ingeschakeld aan het begin van de volgende factureringsperiode. U kunt deze echter op elk gewenst moment zelf weer inschakelen. |
      | Bestedingslimiet voor de huidige factureringsperiode verwijderen | De bestedingslimiet wordt automatisch weer ingeschakeld aan het begin van de volgende factureringsperiode. |


1. Klik op **Betalingswijze selecteren** om een betalingswijze te kiezen voor uw abonnement. Dit wordt de actieve betalingswijze voor uw abonnement.

1. Klik op **Voltooien**.


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Waarom u de bestedingslimiet zou willen verwijderen

De bestedingslimiet kan ervoor zorgen dat u bepaalde Microsoft-services en services van derden niet meer kunt implementeren of gebruiken. Hieronder ziet u de situaties waarin u het beste de bestedingslimiet van uw abonnement kunt verwijderen.

-  U bent van plan installatiekopieën van derden zoals Oracle of services als Azure DevOps Services te implementeren. In deze situatie bereikt u bijna meteen de bestedingslimiet, waardoor uw abonnement wordt uitgeschakeld.
- U hebt services die u niet wilt onderbreken. Wanneer u de bestedingslimiet bereikt, worden Azure-resources die u hebt geïmplementeerd uit de productie genomen. Ook worden uw virtuele machines van Azure stopgezet en wordt de toewijzing ervan ongedaan gemaakt. Als u services hebt die u niet wilt onderbreken, moet u de bestedingslimiet verwijderen.
- U hebt services en bronnen met instellingen zoals virtuele IP-adressen die u niet wilt kwijtraken. Deze instellingen gaan verloren wanneer u de bestedingslimiet bereikt en de toewijzing van de services en bronnen ongedaan wordt gemaakt.

## <a name="turn-on-the-spending-limit-after-removing"></a>De bestedingslimiet inschakelen na het verwijderen

Deze functie is alleen beschikbaar wanneer de bestedingslimiet voor onbepaalde tijd is verwijderd voor abonnementstypen met tegoeden die zijn verspreid over meerdere maanden. U kunt deze functie gebruiken om uw bestedingslimiet automatisch aan het begin van de volgende factureringsperiode in te schakelen.


1. Meld u als accountbeheerder aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Cost Management en facturering**.

    ![Schermopname van de zoekopdracht naar kostenbeheer en facturering ](./media/spending-limit/search-bar.png)

1. Selecteer uw abonnement in de lijst **Mijn abonnementen**. Selecteer bijvoorbeeld *Visual Studio Enterprise*.

   ![Schermopname van het overzicht van Mijn abonnementen](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Als u sommige van uw Visual Studio-abonnementen hier niet ziet, kan het zijn dat u de map voor een abonnement op een bepaald moment hebt gewijzigd. Voor deze abonnementen moet u weer de oorspronkelijke map instellen (de map waarin u zich voor het eerst hebt geregistreerd). Herhaal vervolgens stap 2.

1. Klik in het overzicht van het abonnement op de banner boven aan de pagina om de bestedingslimiet weer in te schakelen.

## <a name="custom-spending-limit"></a>Aangepast bestedingslimiet

Aangepaste bestedingslimieten zijn niet beschikbaar.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Een bestedingslimiet voorkomt niet alle kosten

[Sommige externe services die zijn gepubliceerd in Azure Marketplace](../understand/understand-azure-marketplace-charges.md) kunnen niet worden gebruikt met uw abonnementstegoeden en er kunnen afzonderlijke kosten voor in rekening worden gebracht, zelfs wanneer uw bestedingslimiet is ingesteld. Voorbeelden hiervan zijn Visual Studio-licenties, Azure Active Directory Premium, ondersteuningsplannen en de meeste merkservices van derden. Wanneer u een nieuwe externe service inricht, wordt er een waarschuwing weergegeven om u te laten weten dat de services afzonderlijk worden gefactureerd:

![Waarschuwing Marketplace-aankoop](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- Voer een upgrade uit naar abonnement met prijzen voor [betalen per gebruik](upgrade-azure-subscription.md).

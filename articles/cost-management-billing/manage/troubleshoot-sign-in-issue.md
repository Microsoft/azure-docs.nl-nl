---
title: Aanmeldingsproblemen voor Azure-abonnementen oplossen
description: Hulp bij het oplossen van problemen waardoor u zich niet kunt aanmelden bij de Azure-portal of het Azure-accountcentrum.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 915b4fb289b6af6f4aac84d8bfa4d491a7f77efe
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050467"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Aanmeldingsproblemen voor Azure-abonnementen oplossen

Deze handleiding helpt bij het oplossen van problemen waardoor u zich niet kunt aanmelden bij Azure Portal of het Azure-accountcentrum.

> [!NOTE]
> Als u problemen ondervindt bij het aanmelden voor een nieuw Azure-account, raadpleegt u [Problemen met de registratie van Azure-abonnementen oplossen](./troubleshoot-azure-sign-up.md).

## <a name="page-hangs-in-the-loading-status"></a>De pagina loopt vast tijdens de laadstatus

Als uw internetbrowserpagina vastloopt, voert u elk van de volgende stappen uit totdat de Azure-portal bereikbaar is.

- Vernieuw de pagina.
- Gebruik een andere internetbrowser.
- Gebruik de modus voor privé browsen van uw browser:

   - **Edge:** Open **instellingen** (de drie puntjes op uw profiel afbeelding), selecteer **Nieuw InPrivate-venster** en blader door en meld u aan bij de [Azure Portal](https://portal.azure.com/). 
   - **Chrome:** Kies de **Incognito**-modus.
   - **Safari:** Kies **Bestand** en vervolgens **Nieuw privévenster**.

- Wis de cache en verwijder internetcookies:

   - **Microsoft Edge:** Open **Instellingen** en selecteer **Privacy en services**. Volg de stappen onder **Browsegegevens wissen**. Controleer of de selectievakjes voor **Browsegeschiedenis**, **Downloadgeschiedenis**, en **In cache opgeslagen afbeeldingen en bestanden** zijn geselecteerd, en selecteer vervolgens **Verwijderen**.
   - **Chrome:** Kies **Instellingen** en selecteer **Browsegegevens wissen** onder **Privacy en beveiliging**.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>U wordt automatisch aangemeld als een andere gebruiker

Dit probleem kan optreden als u meer dan één gebruikersaccount gebruikt in een internetbrowser.

Probeer een van de volgende methoden om dit probleem op te lossen:

- Wis de cache en verwijder internetcookies.

   - **Microsoft Edge:** Open **Instellingen** en selecteer **Privacy en services**. Volg de stappen onder **Browsegegevens wissen**. Controleer of de selectievakjes voor **Browsegeschiedenis**, **Downloadgeschiedenis**, **Cookies** en **In cache opgeslagen afbeeldingen en bestanden** zijn geselecteerd, en selecteer vervolgens **Verwijderen**.
   - **Chrome:** Kies **Instellingen** en selecteer **Browsegegevens wissen** onder **Privacy en beveiliging**.
- Stel uw browserinstellingen opnieuw in op de standaardwaarden.
- Gebruik de modus voor privé browsen van uw browser. 
   - **Edge:** Open **instellingen** (de drie puntjes op uw profiel afbeelding), selecteer **Nieuw InPrivate-venster** en blader door en meld u aan bij de [Azure Portal](https://portal.azure.com/). 
   - **Chrome:** Kies de **Incognito**-modus.
   - **Safari:** Kies **Bestand** en vervolgens **Nieuw privévenster**.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Ik kan me aanmelden, maar ik zie de fout dat er geen abonnementen zijn gevonden

Dit probleem treedt op als u de verkeerde map hebt geselecteerd of als uw account niet over voldoende machtigingen beschikt.

**Scenario 1:** U ziet het probleem bij het aanmelden bij de [Azure-portal](https://portal.azure.com/)

Dit probleem oplossen:

- Controleer of de juiste Azure-map is geselecteerd door in de rechterbovenhoek uw account te selecteren.
- Als de juiste Azure-map is geselecteerd maar u nog steeds het foutbericht ontvangt, moet uw account worden [toegevoegd als eigenaar](./add-change-subscription-administrator.md).

**Scenario 2:** U ziet het probleem bij het aanmelden bij het [Azure-accountcentrum](https://account.windowsazure.com/Subscriptions)

Controleer of het account dat u gebruikt de accountbeheerder is. Voer de volgende stappen uit om te controleren wie de accountbeheerder is:

1.  Meld u aan bij de [Abonnementenweergave in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1.  Selecteer het abonnement dat u wilt controleren en selecteer vervolgens **Instellingen**.
1.  Selecteer **Eigenschappen**. De accountbeheerder van het abonnement wordt weergegeven in het vak **Accountbeheerder**.

## <a name="additional-help-resources"></a>Aanvullende ondersteuningsresources

Andere artikelen met probleemoplossingen voor Azure Billing en abonnementen

- [Geweigerde kaart](./troubleshoot-declined-card.md)
- [Registratieproblemen voor abonnementen](./troubleshoot-azure-sign-up.md)
- [Geen abonnementen gevonden](./no-subscriptions-found.md)
- [Weergave voor bedrijfskosten is uitgeschakeld](./enterprise-mgmt-grp-troubleshoot-cost-view.md)
- [Documentatie voor Azure Billing](../index.yml)

## <a name="contact-us-for-help"></a>Contact met ons opnemen voor ondersteuning

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
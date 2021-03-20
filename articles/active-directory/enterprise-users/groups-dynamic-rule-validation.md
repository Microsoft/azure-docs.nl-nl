---
title: Regels valideren voor dynamisch groepslid maatschap (preview)-Azure AD | Microsoft Docs
description: Leden testen op basis van een lidmaatschaps regel voor een dynamische groep in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1048284e8e7a492bf0810a16e29409546ed414b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547556"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Een dynamische regel voor groepslid maatschap valideren (preview) in Azure Active Directory

Azure Active Directory (Azure AD) biedt nu de mogelijkheid om dynamische groeps regels te valideren (in open bare preview). Op het tabblad **regels valideren** kunt u uw dynamische regel valideren op basis van voor beelden van groeps leden om te bevestigen dat de regel werkt zoals verwacht. Bij het maken of bijwerken van dynamische groeps regels willen beheerders weten of een gebruiker of een apparaat lid is van de groep. Dit helpt u te evalueren of de gebruiker of het apparaat voldoet aan de regel criteria en hulp bij het oplossen van het lidmaatschap.

## <a name="step-by-step-walk-through"></a>Step-by-Step-instructies

Ga naar **Azure Active Directory** groepen om aan de slag te gaan  >  . Selecteer een bestaande dynamische groep of maak een nieuwe dynamische groep en klik op dynamische lidmaatschaps regels. Vervolgens ziet u het tabblad **regels valideren** .

![Zoek het tabblad regels valideren en begin met een bestaande regel](./media/groups-dynamic-rule-validation/validate-tab.png)

Op het tabblad **regels valideren** kunt u gebruikers selecteren om hun lidmaatschappen te valideren. u kunt Maxi maal 20 gebruikers of apparaten tegelijk selecteren.

![Gebruikers toevoegen om de bestaande regel te valideren](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Nadat u de gebruikers of apparaten uit de kiezer hebt gekozen en **selecteert**, wordt validatie automatisch gestart en worden de validatie resultaten weer gegeven.

![De resultaten van de validatie van de regel weer geven](./media/groups-dynamic-rule-validation/validate-tab-results.png)

De resultaten geven aan of een gebruiker lid is van de groep of niet. Als de regel ongeldig is of als er een netwerk probleem is, wordt het resultaat weer gegeven als **onbekend**. In het geval van **onbekend** wordt in het gedetailleerde fout bericht het probleem en de vereiste acties beschreven.

![De details van de resultaten van de validatie van de regel weer geven](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

U kunt de regel en validatie van lidmaatschappen wijzigen. Als u wilt zien waarom de gebruiker geen lid is van de groep, klikt u op Details weer geven en toont de verificatie gegevens het resultaat van elke expressie waarmee de regel wordt samengesteld. Klik op **OK** om af te sluiten.

## <a name="next-steps"></a>Volgende stappen

- [Dynamische lidmaatschaps regels voor groepen](groups-dynamic-membership.md)

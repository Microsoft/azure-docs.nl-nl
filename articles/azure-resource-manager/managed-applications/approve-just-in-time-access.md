---
title: Just-in-time-toegang goedkeuren
description: Hierin wordt beschreven hoe gebruikers van Azure Managed Applications aanvragen voor Just-in-time-toegang tot een beheerde toepassing goed keuren.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75651018"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Just-in-time-toegang voor Azure Managed Applications configureren en goed keuren

Als gebruiker van een beheerde toepassing is het wellicht niet handig om de uitgever permanente toegang te geven tot de beheerde resource groep. Om u meer controle te geven over het verlenen van toegang tot beheerde bronnen, biedt Azure Managed Applications een functie met de naam just-in-time (JIT) toegang, die momenteel als preview-versie beschikbaar is. Hiermee kunt u goed keuren wanneer en hoe lang de uitgever toegang heeft tot de resource groep. De uitgever kan tijdens die tijd vereiste updates maken, maar wanneer deze tijd overschrijdt, verloopt de toegang van de uitgever.

De werk stroom voor het verlenen van toegang is:

1. De uitgever voegt een beheerde toepassing aan de Marketplace toe en geeft aan dat JIT-toegang beschikbaar is.

1. Tijdens de implementatie schakelt u JIT-toegang in voor uw exemplaar van de beheerde toepassing.

1. Na de implementatie kunt u de instellingen voor JIT-toegang wijzigen.

1. De uitgever verzendt een aanvraag voor toegang.

1. U keurt de aanvraag goed.

In dit artikel wordt gekeken naar de acties die gebruikers uitvoeren om JIT-toegang en-goedkeurings aanvragen mogelijk te maken. Zie [just-in-time-toegang aanvragen in azure Managed Applications](request-just-in-time-access.md)voor meer informatie over het publiceren van een beheerde toepassing met JIT-toegang.

> [!NOTE]
> Als u just-in-time-toegang wilt gebruiken, moet u een [Azure Active Directory P2-licentie](../../active-directory/privileged-identity-management/subscription-requirements.md)hebben.

## <a name="enable-during-deployment"></a>Inschakelen tijdens implementatie

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek een Marketplace-vermelding voor een beheerde toepassing waarvoor JIT is ingeschakeld. Selecteer **Maken**.

1. Tijdens het leveren van waarden voor de nieuwe beheerde toepassing, kunt u met de **JIT-configuratie** stap JIT-toegang voor de beheerde toepassing in-of uitschakelen. Selecteer **Ja** om **JIT-toegang in te scha kelen**. Deze optie is standaard ingeschakeld voor beheerde toepassingen die zijn gedefinieerd met JIT ingeschakeld in de Marketplace.

   ![Toegang configureren](./media/approve-just-in-time-access/configure-jit-access.png)

   U kunt JIT-toegang alleen inschakelen tijdens de implementatie. Als u **Nee** selecteert, krijgt de uitgever permanente toegang tot de beheerde resource groep. U kunt JIT-toegang later niet meer inschakelen.

1. Selecteer de optie **JIT-configuratie aanpassen** als u de standaard instellingen voor goed keuring wilt wijzigen.

   ![Toegang aanpassen](./media/approve-just-in-time-access/customize-jit-access.png)

   Een beheerde toepassing met JIT-functionaliteit heeft standaard de volgende instellingen:

   * Goedkeurings modus-automatisch
   * Maximale toegangs duur: 8 uur
   * Goed keurders – geen

   Wanneer de goedkeurings modus is ingesteld op **automatisch**, ontvangen de goed keurders een melding voor elke aanvraag, maar de aanvraag wordt automatisch goedgekeurd. Als deze instelling is ingesteld op **hand matig**, ontvangen de goed keurders een melding voor elke aanvraag en moeten ze deze goed keuren.

   De maximale duur van de activering bepaalt de maximale hoeveelheid tijd die een uitgever kan aanvragen voor toegang tot de beheerde resource groep.

   De lijst met goed keurders is de Azure Active Directory gebruikers die JIT-toegangs aanvragen kunnen goed keuren. Als u een goed keurder wilt toevoegen, selecteert u **goed keurder toevoegen** en zoekt u naar de gebruiker.

   Nadat u de instelling hebt bijgewerkt, selecteert u **Opslaan**.

## <a name="update-after-deployment"></a>Bijwerken na implementatie

U kunt de waarden wijzigen voor de manier waarop aanvragen worden goedgekeurd. Als u JIT-toegang niet hebt ingeschakeld tijdens de implementatie, kunt u deze later echter niet meer inschakelen.

De instellingen voor een geïmplementeerde beheerde toepassing wijzigen:

1. Selecteer in de Portal de toepassing beheren.

1. Selecteer **JIT-configuratie** en wijzig de instellingen naar wens.

   ![Toegangs instellingen wijzigen](./media/approve-just-in-time-access/change-settings.png)

1. Selecteer **Opslaan** wanneer u klaar bent.

## <a name="approve-requests"></a>Aanvragen goedkeuren

Wanneer de uitgever toegang vraagt, ontvangt u een melding van de aanvraag. U kunt JIT-toegangs aanvragen direct via de beheerde toepassing of via de Azure AD Privileged Identity Management-service goed keuren voor alle beheerde toepassingen. Als u just-in-time-toegang wilt gebruiken, moet u een [Azure Active Directory P2-licentie](../../active-directory/privileged-identity-management/subscription-requirements.md)hebben.

Aanvragen goed keuren via de beheerde toepassing:

1. Selecteer **JIT-toegang** voor de beheerde toepassing en selecteer **aanvragen goed keuren**.

   ![Aanvragen goedkeuren](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selecteer de aanvraag die u wilt goed keuren.

   ![Aanvraag selecteren](./media/approve-just-in-time-access/select-request.png)

1. Geef de reden voor de goed keuring op in het formulier en selecteer **goed keuren**.

Aanvragen goed keuren via Azure AD Privileged Identity Management:

1. Selecteer **alle services** en begin met zoeken naar **Azure AD privileged Identity Management**. Selecteer Tags bij de beschikbare opties.

   ![Zoeken naar service](./media/approve-just-in-time-access/search.png)

1. Selecteer **aanvragen goed keuren**.

   ![Aanvragen goed keuren selecteren](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selecteer **Azure Managed Applications** en selecteer de aanvraag die u wilt goed keuren.

   ![Aanvragen selecteren](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Volgende stappen

Zie [just-in-time-toegang aanvragen in azure Managed Applications](request-just-in-time-access.md)voor meer informatie over het publiceren van een beheerde toepassing met JIT-toegang.

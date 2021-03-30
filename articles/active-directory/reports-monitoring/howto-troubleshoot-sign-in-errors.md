---
title: Problemen oplossen met rapporten over het registreren van fouten | Microsoft Docs
description: Meer informatie over het oplossen van aanmeldings fouten met behulp van Azure Active Directory-rapporten in de Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de9f5a21f38f741dcf65d285446d9482716d4f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85608123"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Procedure: Fouten bij het aanmelden oplossen met behulp van Azure Active Directory-rapporten

Met het [aanmeld rapport](concept-sign-ins.md) in azure Active Directory (Azure AD) kunt u antwoorden vinden op vragen over het beheren van de toegang tot de toepassingen in uw organisatie, waaronder:

- Wat is het aanmeldingspatroon van een gebruiker?
- Hoeveel keer hebben gebruikers zich aangemeld gedurende een week?
- Wat is de status van deze aanmeldingen?


Daarnaast kan het rapport aanmeldingen u helpen bij het oplossen van aanmeldings fouten voor gebruikers in uw organisatie. In deze hand leiding leert u hoe u een mislukte aanmelding kunt isoleren in het rapport aanmeldingen, en hoe u deze gebruikt om de hoofd oorzaak van de fout te begrijpen.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende zaken nodig:

* Een Azure AD-Tenant met een Premium-licentie (P1/P2). Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden.
* Een gebruiker die zich in de rol **globale beheerder**, **beveiligings beheerder**, **beveiligings lezer** of **rapport lezer** voor de Tenant bevindt. Bovendien kan elke gebruiker toegang krijgen tot eigen aanmeldingen. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Fouten bij het aanmelden oplossen met behulp van het rapport aanmeldingen

1. Ga naar de [Azure-portal](https://portal.azure.com) en selecteer uw map.
2. Selecteer **Azure Active Directory** en selecteer in de sectie **Controle** de optie **Aanmeldingen**. 
3. Gebruik de meegeleverde filters om de fout te verfijnen, hetzij door de gebruikers naam of object-id of de datum van de toepassing. Selecteer bovendien **fout** in de vervolg keuzelijst **status** om alleen de mislukte aanmeldingen weer te geven. 

    ![Resultaten filteren](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identificeer de mislukte aanmelding die u wilt onderzoeken. Selecteer deze optie om het venster met aanvullende details te openen met meer informatie over de mislukte aanmelding. Noteer de **fout code voor aanmelding** en de **reden** van de fout. 

    ![Record selecteren](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. U kunt deze informatie ook vinden op het tabblad **probleem oplossing en ondersteuning** in het detail venster.

    ![Probleem oplossing en ondersteuning](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. De reden van de fout is een beschrijving van de fout. In het bovenstaande scenario is de reden van de fout bijvoorbeeld een **Ongeldige gebruikers naam of wacht woord of een ongeldige on-premises gebruikers naam of wacht woord**. De oplossing is om gewoon opnieuw aan te melden met de juiste gebruikers naam en wacht woord.

7. U kunt aanvullende informatie krijgen, inclusief ideeën voor herstel, door te zoeken naar de fout code **50126** in dit voor beeld, in de [Naslag Gids voor fout codes voor aanmeldingen](reference-sign-ins-error-codes.md). 

8. Als dat niet het geval is, of als het probleem zich blijft voordoen ondanks de aanbevolen actie, [opent u een ondersteunings ticket](../fundamentals/active-directory-troubleshooting-support-howto.md) volgens de stappen op het tabblad **probleem oplossing en ondersteuning** . 

## <a name="next-steps"></a>Volgende stappen

* [Naslag informatie over fout codes voor aanmeldingen](reference-sign-ins-error-codes.md)
* [Overzicht van het rapport met aanmeldingen](concept-sign-ins.md)

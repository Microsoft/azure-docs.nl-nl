---
title: Gebruikers met directe licenties toevoegen aan groeps licenties-Azure AD | Microsoft Docs
description: Migratie van afzonderlijke gebruikers licenties naar een groeps licentie met behulp van Azure Active Directory
services: active-directory
keywords: Azure AD-licenties
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: adc80cf579ce3086abd4171b065f859acd0b9294
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546502"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Gebruikers met afzonderlijke licenties migreren naar groepen voor licentie verlening

Mogelijk hebt u bestaande licenties die zijn geïmplementeerd voor gebruikers in de organisaties via directe toewijzing; dat wil zeggen, met behulp van Power shell-scripts of andere hulpprogram ma's om afzonderlijke gebruikers licenties toe te wijzen. Voordat u begint met het gebruik van groeps licenties voor het beheren van licenties in uw organisatie, kunt u dit migratie plan gebruiken om bestaande oplossingen naadloos te vervangen door op groepen gebaseerde licentie verlening.

Het belangrijkste voor deel is dat u een situatie moet vermijden waarbij de migratie naar op groep gebaseerde licentie verlening ertoe leidt dat gebruikers de momenteel toegewezen licenties tijdelijk kwijt raken. Elk proces dat kan leiden tot het verwijderen van licenties moet worden vermeden om het risico te verwijderen dat gebruikers de toegang tot services en hun gegevens verliezen.

## <a name="recommended-migration-process"></a>Aanbevolen migratie proces

1. U hebt al een automatisering (bijvoorbeeld Power shell) voor het beheren van de licentie toewijzing en het verwijderen van gebruikers. Zorg ervoor dat het actief blijft.

1. Maak een nieuwe licentie groep (of bepaal welke bestaande groepen u wilt gebruiken) en zorg ervoor dat alle vereiste gebruikers worden toegevoegd als leden.

1. Wijs de vereiste licenties toe aan deze groepen. het doel moet overeenkomen met dezelfde licentie status als uw bestaande automatisering (bijvoorbeeld Power shell) voor deze gebruikers.

1. Controleer of de licenties zijn toegepast op alle gebruikers in die groepen. U kunt deze toepassing uitvoeren door de verwerkings status van elke groep te controleren en door audit logboeken te controleren.

   - U kunt afzonderlijke gebruikers controleren door hun licentie gegevens te bekijken. U ziet dat ze dezelfde licenties hebben toegewezen als ' direct ' en ' overgenomen ' van groepen.

   - U kunt een Power shell-script uitvoeren om te [controleren hoe licenties aan gebruikers worden toegewezen](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Wanneer dezelfde product licentie zowel rechtstreeks als via een groep wordt toegewezen aan de gebruiker, wordt slechts één licentie door de gebruiker gebruikt. Er zijn dus geen extra licenties nodig om de migratie uit te voeren.

1. Controleer of er geen licentie toewijzingen zijn mislukt door elke groep te controleren op gebruikers met de status fout. Zie [licentie problemen voor een groep identificeren en oplossen](licensing-groups-resolve-problems.md)voor meer informatie.

Overweeg de oorspronkelijke directe toewijzingen te verwijderen. We raden u aan dit geleidelijk uit te voeren en het resultaat op een subset van gebruikers eerst te controleren. Als u de oorspronkelijke directe toewijzingen voor gebruikers kunt verlaten, maar wanneer de gebruikers hun gelicentieerde groepen verlaten, behouden ze de rechtstreeks toegewezen licenties. Dit is mogelijk niet wat u wilt.

## <a name="an-example"></a>Een voor beeld

Een organisatie heeft 1.000 gebruikers. Voor alle gebruikers zijn Office 365 Enter prise E3-licenties nodig. Op dit moment heeft de organisatie een Power shell-script dat wordt uitgevoerd op locatie, het toevoegen en verwijderen van licenties van gebruikers wanneer ze binnenkomen. De organisatie wil het script echter vervangen door op groepen gebaseerde licentie verlening zodat de licenties automatisch door Azure AD kunnen worden beheerd.

Het migratie proces kan er als volgt uitzien:

1. Wijs de Office 365 E3-licentie toe aan de groep **alle gebruikers** in azure AD met behulp van de Azure Portal.

1. Controleer of de licentie toewijzing is voltooid voor alle gebruikers. Ga naar de pagina overzicht voor de groep, selecteer **licenties** en controleer de verwerkings status boven aan de Blade **licenties** .

   - Zoek naar "nieuwste licentie wijzigingen zijn toegepast op alle gebruikers" om te bevestigen dat de verwerking is voltooid.

   - Zoek op de hoogte van alle gebruikers voor wie de licenties mogelijk niet zijn toegewezen. Zijn er geen licenties meer voor sommige gebruikers? Hebben sommige gebruikers conflicterende licentie plannen die voor komen dat ze groeps licenties overnemen?

1. Let op dat sommige gebruikers controleren of ze de directe en groeps licenties hebben toegepast. Ga naar de profiel pagina voor een gebruiker, selecteer **licenties** en controleer de status van licenties.

   - Dit is de verwachte gebruikers status tijdens de migratie:

      ![de verwachte gebruikers status tijdens de migratie](./media/licensing-groups-migrate-users/expected-user-state.png)

     Hiermee wordt bevestigd dat de gebruiker zowel directe als overgenomen licenties heeft. U ziet dat Office 365 E3 is toegewezen.

   - Selecteer elke licentie om te zien welke services zijn ingeschakeld. Als u wilt controleren of de directe en de groeps licenties exact dezelfde services voor de gebruiker inschakelen, selecteert u **toewijzingen**.

1. Nadat u hebt bevestigd dat zowel directe als groeps licenties gelijkwaardig zijn, kunt u beginnen met het verwijderen van directe licenties van gebruikers. U kunt dit testen door ze te verwijderen voor afzonderlijke gebruikers in de portal en vervolgens Automation-scripts uit te voeren, zodat ze bulksgewijs worden verwijderd. Hier volgt een voor beeld van dezelfde gebruiker met de direct-licenties die zijn verwijderd via de portal. U ziet dat de licentie status ongewijzigd blijft, maar er worden geen directe toewijzingen meer weer gegeven.

   ![bevestigen dat de directe licenties zijn verwijderd](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere scenario's voor het beheer van groeps licenties:

- [Wat is op een groep gebaseerde licentie verlening in Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Licenties toewijzen aan een groep in Azure Active Directory](licensing-groups-assign.md)
- [Licentieproblemen voor een groep vaststellen en oplossen in Azure Active Directory](licensing-groups-resolve-problems.md)
- [Gebruikers tussen product licenties migreren met op groepen gebaseerde licentie verlening in Azure Active Directory](licensing-groups-change-licenses.md)
- [Aanvullende scenario’s voor Azure Active Directory-licenties op basis van groepen](licensing-group-advanced.md)
- [Power shell-voor beelden voor op groep gebaseerde licentie verlening in Azure Active Directory](licensing-ps-examples.md)

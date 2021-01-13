---
title: Gebruikers migratie benaderingen
titleSuffix: Azure AD B2C
description: Migreer gebruikers accounts van een andere ID-provider naar Azure AD B2C met behulp van de pre-migratie of naadloze migratie methoden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fea8d93121fcb5622f86257febcc7d834b444098
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178803"
---
# <a name="migrate-users-to-azure-ad-b2c"></a>Gebruikers migreren naar Azure AD B2C

Voor de migratie van een andere ID-provider naar Azure Active Directory B2C (Azure AD B2C) moeten mogelijk ook bestaande gebruikers accounts worden gemigreerd. Hier worden twee migratie methoden besproken, *vooraf migreren* en *naadloze migratie*. Bij beide methoden moet u een toepassing of script schrijven die gebruikmaakt van de Microsoft Graph- [API](manage-user-accounts-graph-api.md) om gebruikers accounts te maken in azure AD B2C.

## <a name="pre-migration"></a>Vooraf migreren

In de stroom voorafgaand aan de migratie voert de migratie toepassing de volgende stappen uit voor elk gebruikers account:

1. Lees het gebruikers account van de oude ID-provider, met inbegrip van de huidige referenties (gebruikers naam en wacht woord).
1. Maak een overeenkomend account in uw Azure AD B2C Directory met de huidige referenties.

Gebruik de pre-migratie stroom in een van deze twee situaties:

- U hebt toegang tot de Lees bare referenties van een gebruiker (de gebruikers naam en het wacht woord).
- De referenties zijn versleuteld, maar u kunt ze ontsleutelen.

Zie [Azure AD B2C gebruikers accounts beheren met Microsoft Graph](manage-user-accounts-graph-api.md)voor meer informatie over het programmatisch maken van gebruikers accounts.

## <a name="seamless-migration"></a>Naadloze migratie

Gebruik de naadloze migratie stroom als niet-gecodeerde wacht woorden in de oude ID-provider niet toegankelijk zijn. Dit kan bijvoorbeeld nodig zijn in de volgende situaties:

- Het wacht woord wordt opgeslagen in een eenrichtings gecodeerde indeling, zoals met een hash-functie.
- Het wacht woord wordt opgeslagen door de verouderde ID-provider op een manier die u niet kunt openen. Bijvoorbeeld wanneer de ID-provider referenties valideert door een webservice aan te roepen.

Voor de naadloze migratie stroom is nog steeds een vooraf-migratie van gebruikers accounts vereist, maar vervolgens een [aangepast beleid](custom-policy-get-started.md) voor het uitvoeren van een query op een [rest API](custom-policy-rest-api-intro.md) (die u maakt) om het wacht woord van elke gebruiker bij de eerste aanmelding in te stellen.

De naadloze migratie stroom heeft daarom twee fasen: *pre-migratie* en *Referenties instellen*.

### <a name="phase-1-pre-migration"></a>Fase 1: vooraf migreren

1. Uw migratie toepassing leest de gebruikers accounts van de oude ID-provider.
1. De migratie toepassing maakt corresponderende gebruikers accounts in uw Azure AD B2C Directory, maar *stelt geen wacht woorden* in.

### <a name="phase-2-set-credentials"></a>Fase 2: Referenties instellen

Nadat de accounts vooraf zijn gemigreerd, worden uw aangepaste beleid en REST API het volgende uitgevoerd wanneer een gebruiker zich aanmeldt:

1. Lees het Azure AD B2C gebruikers account dat overeenkomt met het opgegeven e-mail adres.
1. Controleer of het account is gemarkeerd voor migratie door een Boole-extensie kenmerk te evalueren.
    - Als het uitbreidings kenmerk wordt geretourneerd `True` , belt u uw rest API om het wacht woord te valideren voor de verouderde ID-provider.
      - Als de REST API bepaalt of het wacht woord onjuist is, geeft u een beschrijvende fout door aan de gebruiker.
      - Als de REST API bepaalt of het wacht woord juist is, schrijft u het wacht woord naar het Azure AD B2C account en wijzigt u het kenmerk van de Boole-extensie in `False` .
    - Als het kenmerk van de Boole-extensie wordt geretourneerd `False` , kunt u het aanmeldings proces het normaal voortzetten.

Als u een voor beeld van een aangepast beleid en REST API wilt bekijken, raadpleegt u het voor beeld [naadloze gebruikers migratie](https://aka.ms/b2c-account-seamless-migration) op github.

![Stroom diagram van de naadloze migratie benadering voor gebruikers migratie](./media/user-migration/diagram-01-seamless-migration.png)<br />*Diagram: naadloze migratie stroom*

## <a name="best-practices"></a>Aanbevolen procedures

### <a name="security"></a>Beveiliging

De naadloze migratie aanpak gebruikt uw eigen aangepaste REST API om de referenties van een gebruiker te valideren voor de verouderde ID-provider.

**U moet uw REST API beschermen tegen beveiligings aanvallen.** Een aanvaller kan verschillende wacht woorden indienen in de hoop dat uiteindelijk de referenties van een gebruiker worden geraden. Om dergelijke aanvallen te verslaanen, moet u stoppen met het door sturen van aanvragen aan uw REST API wanneer het aantal aanmeldings pogingen een bepaalde drempel waarde geeft. Beveilig ook de communicatie tussen Azure AD B2C en uw REST API. Zie [Secure rest API](secure-rest-api.md)(Engelstalig) voor meer informatie over het beveiligen van uw rest-api's voor productie.

### <a name="user-attributes"></a>Gebruikers kenmerken

Niet alle informatie in de ID-provider moet worden gemigreerd naar uw Azure AD B2C Directory. Bepaal de juiste set gebruikers kenmerken die moeten worden opgeslagen in Azure AD B2C voordat u de migratie uitvoert.

- **Opslaan in** Azure AD B2C
  - Gebruikers naam, wacht woord, e-mail adres, telefoon nummer, lidmaatschaps nummers/id's.
  - Markering van toestemming voor privacybeleid en gebruiksrecht overeenkomsten voor eind gebruikers.
- **Niet** opslaan in azure AD B2C
  - Gevoelige gegevens zoals creditcard nummers, sofi (Social Security Number), medische dossiers of andere gegevens die worden geregeld door overheids-of industriële nalevings instellingen.
  - Marketing-of communicatie voorkeuren, gebruikers gedrag en inzichten.

### <a name="directory-clean-up"></a>Opschonen van Directory

Voordat u met het migratie proces begint, kunt u de mogelijkheid om uw directory op te schonen.

- Bepaal de set gebruikers kenmerken die moeten worden opgeslagen in Azure AD B2C en migreer alleen wat u nodig hebt. Indien nodig kunt u [aangepaste kenmerken](user-flow-custom-attributes.md) maken om meer gegevens over een gebruiker op te slaan.
- Als u migreert van een omgeving met meerdere verificatie bronnen (bijvoorbeeld elke toepassing heeft een eigen gebruikers Directory), migreert u naar een uniform account in Azure AD B2C.
- Als meerdere toepassingen verschillende gebruikers namen hebben, kunt u deze allemaal opslaan in een Azure AD B2C gebruikers account met behulp van de verzameling Identities. Laat de gebruiker met betrekking tot het wacht woord één kiezen en stel deze in de map in. Met de naadloze migratie wordt alleen het gekozen wacht woord opgeslagen in het Azure AD B2C-account.
- Verwijder ongebruikte gebruikers accounts vóór de migratie of Migreer geen verlopen accounts.

### <a name="password-policy"></a>Wachtwoord beleid

Als de accounts die u wilt migreren, een zwakkere wachtwoord sterkte hebben dan de [sterke wachtwoord sterkte](../active-directory/authentication/concept-sspr-policy.md) die wordt afgedwongen door Azure AD B2C, kunt u de vereiste voor sterke wacht woorden uitschakelen. Zie voor meer informatie [wachtwoord beleid-eigenschap](user-profile-attributes.md#password-policy-attribute).

## <a name="next-steps"></a>Volgende stappen

De opslag plaats [Azure-AD-B2C/User-Migration](https://github.com/azure-ad-b2c/user-migration) op github bevat een naadloos voor beeld van een aangepast beleid voor migratie en rest API code voorbeeld:

[Het aangepaste beleid voor naadloze gebruikers migratie & REST API code voorbeeld](https://aka.ms/b2c-account-seamless-migration)

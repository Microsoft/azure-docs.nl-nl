---
title: Aangepaste besturings elementen in voorwaardelijke toegang voor Azure AD
description: Meer informatie over het werken met aangepaste besturings elementen in Azure Active Directory voorwaardelijke toegang.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837580"
---
# <a name="custom-controls-preview"></a>Aangepaste besturings elementen (preview-versie)

Aangepaste besturings elementen zijn een preview-functie van de Azure Active Directory. Wanneer u aangepaste besturings elementen gebruikt, worden uw gebruikers omgeleid naar een compatibele service om te voldoen aan de verificatie vereisten buiten Azure Active Directory. Om aan dit besturings element te voldoen, wordt de browser van een gebruiker omgeleid naar de externe service, wordt elke vereiste verificatie uitgevoerd en wordt vervolgens teruggeleid naar Azure Active Directory. Azure Active Directory controleert het antwoord en, als de gebruiker is geverifieerd of gevalideerd, wordt de gebruiker in de stroom voor voorwaardelijke toegang voortgezet.

> [!NOTE]
> Zie voor meer informatie over wijzigingen die voor aangepaste besturings elementen worden gepland, het [Archief van februari 2020 voor wat er nieuw is](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls).

## <a name="creating-custom-controls"></a>Aangepaste besturings elementen maken

Aangepaste besturings elementen werken met een beperkt aantal goedgekeurde verificatie providers. Als u een aangepast besturings element wilt maken, moet u eerst contact opnemen met de provider die u wilt gebruiken. Elke niet-micro soft-provider heeft een eigen proces en vereisten om zich aan te melden, te abonneren of anderszins deel te nemen aan de service, en om aan te geven dat u wilt integreren met voorwaardelijke toegang. Op dat moment geeft de provider u een blok gegevens in JSON-indeling. Met deze gegevens kan de provider en de voorwaardelijke toegang samen werken voor uw Tenant, wordt het nieuwe besturings element gemaakt en wordt gedefinieerd hoe voorwaardelijke toegang kan zien of uw gebruikers verificatie hebben uitgevoerd met de provider.

Kopieer de JSON-gegevens en plak deze in het bijbehorende tekstvak. Breng geen wijzigingen aan in de JSON, tenzij u de wijziging die u aanbrengt expliciet begrijpt. Als u een wijziging aanbrengt, kan de verbinding tussen de provider en micro soft worden verbroken en kunnen u en uw gebruikers uw accounts vergren delen.

De optie voor het maken van een aangepast besturings element bevindt zich in de sectie **beheren** van de pagina **voorwaardelijke toegang** .

![Aangepaste besturings elementen interface in voorwaardelijke toegang](./media/controls/custom-controls-conditional-access.png)

Als u op **nieuw aangepast besturings element** klikt, wordt een Blade geopend met een tekstvak voor de JSON-gegevens van uw besturings element.  

![Nieuw aangepast besturings element](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Aangepaste besturings elementen verwijderen

Als u een aangepast besturings element wilt verwijderen, moet u er eerst voor zorgen dat het niet wordt gebruikt in een beleid voor voorwaardelijke toegang. Eenmaal voltooid:

1. Ga naar de lijst met aangepaste besturings elementen
1. Klik op...  
1. Selecteer **Verwijderen**.

## <a name="editing-custom-controls"></a>Aangepaste besturings elementen bewerken

Als u een aangepast besturings element wilt bewerken, moet u het huidige besturings element verwijderen en een nieuw besturings element maken met de bijgewerkte gegevens.

## <a name="known-limitations"></a>Bekende beperkingen

Aangepaste besturings elementen kunnen niet worden gebruikt met automatisering van identiteits beveiliging waarvoor Azure AD-Multi-Factor Authentication, selfservice voor wachtwoord herstel (SSPR) van Azure AD zijn vereist, voldoen aan de vereisten voor de multi-factor Authentication-claim voor het verhogen van de rollen in privileged Identity Manager (PIM), als onderdeel van de inschrijving van intune-apparaten of bij het toevoegen van apparaten aan Azure AD.

## <a name="next-steps"></a>Volgende stappen

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus Alleen rapporteren](concept-conditional-access-report-only.md)

- [Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

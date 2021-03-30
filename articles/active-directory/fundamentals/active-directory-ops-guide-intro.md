---
title: Naslag Gids voor Azure Active Directory bewerkingen
description: In deze hand leiding voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om identiteits-en toegangs beheer, verificatie, governance en bewerkingen te onderhouden.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74535312"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Naslag Gids voor Azure Active Directory bewerkingen

In deze hand leiding voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om de volgende gebieden te beveiligen en te onderhouden:

- **[Identiteits-en toegangs beheer](active-directory-ops-guide-iam.md)** : de levens cyclus van identiteiten en hun rechten kan worden beheerd.
- **[Verificatie beheer](active-directory-ops-guide-auth.md)** : de mogelijkheid om referenties te beheren, authenticatie-ervaring te definiëren, toewijzing te delegeren, gebruik te meten en toegangs beleid te definiëren op basis van beveiligings postuur van de onderneming.
- **[Governance](active-directory-ops-guide-govern.md)** : de toegang tot niet-privilegede en bevoorrechte identiteiten, audit en controle wijzigingen in de omgeving beoordelen en certificeert.
- **[Bewerkingen](active-directory-ops-guide-ops.md)** : Optimaliseer de bewerkingen Azure Active Directory (Azure AD).

Enkele aanbevelingen zijn mogelijk niet van toepassing op alle klanten in de omgeving, bijvoorbeeld AD FS aanbevolen procedures zijn mogelijk niet van toepassing als uw organisatie gebruikmaakt van wachtwoord-hash-synchronisatie.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de datum van publicatie, maar kunnen worden gewijzigd in de loop van de tijd. Organisaties moeten voortdurend hun identiteits methoden evalueren omdat micro soft-producten en-services zich in de loop van de tijd ontwikkelen. Aanbevelingen kunnen veranderen wanneer organisaties zich abonneren op een andere Azure AD Premium licentie. Azure AD Premium P2 bevat bijvoorbeeld meer governance-aanbevelingen.

## <a name="stakeholders"></a>Belanghebbenden

In elke sectie van deze hand leiding wordt geadviseerd om belanghebbenden toe te wijzen om de belangrijkste taken goed te plannen en te implementeren. De volgende tabel bevat een overzicht van alle belanghebbenden in deze hand leiding:

| Belanghebbende | Beschrijving |
| :- | :- |
| IAM Operations-team | Dit team behandelt het beheer van de dagelijkse bewerkingen van het identiteits-en toegangs beheer systeem |
| Team van productiviteit | Dit team is eigenaar van en beheert de productiviteits toepassingen zoals e-mail, het delen van bestanden en samen werking, chat berichten en vergaderingen. |
| Eigenaar van de toepassing | Dit team is eigenaar van de specifieke toepassing van een bedrijf en doorgaans een technisch perspectief in een organisatie. |
| Team van InfoSec-architectuur | Dit team plant en ontwerpt de informatie beveiligings procedures van een organisatie. |
| InfoSec Operations-team | Dit team voert en bewaakt de geïmplementeerde beveiligings procedures voor gegevens van het InfoSec Architecture-Team. |

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [controles en acties voor identiteits-en toegangs beheer](active-directory-ops-guide-iam.md).

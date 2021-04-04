---
title: Levenscyclus instellingen voor een toegangs pakket wijzigen in azure AD-recht beheer-Azure Active Directory
description: Meer informatie over het wijzigen van de gegevens van de aanvrager & levenscyclus instellingen voor een toegangs pakket in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fd119238476b842a4dba1efeec86544ca263920
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980101"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Levenscyclus instellingen voor een toegangs pakket wijzigen in het beheer van rechten van Azure AD

Als Access Package Manager kunt u de levenscyclus instellingen voor een toegangs pakket op elk gewenst moment wijzigen door een bestaand beleid te bewerken. Als u de verval datum voor een beleid wijzigt, wordt de verval datum voor aanvragen waarvoor al een goed keuring in behandeling of goedgekeurd staat, niet gewijzigd.

In dit artikel wordt beschreven hoe u de levenscyclus instellingen voor een bestaand toegangs pakket wijzigt.

## <a name="open-requestor-information"></a>Gegevens van de aanvrager openen
Om ervoor te zorgen dat gebruikers over de juiste toegang tot een toegangs pakket beschikken, kunnen aangepaste vragen worden geconfigureerd om gebruikers te vragen toegang te krijgen tot bepaalde toegangs pakketten. De volgende configuratie opties zijn beschikbaar: lokalisatie, vereist/optioneel, en tekst/meerkeuze antwoord indelingen. Aanvragers zien de vragen wanneer ze het pakket en de goed keurders vragen de antwoorden op de vragen te bekijken om hen te helpen hun beslissing te nemen. Gebruik de volgende stappen om vragen te configureren in een toegangs pakket:

## <a name="open-lifecycle-settings"></a>Open levenscyclus instellingen

Als u de levenscyclus instellingen voor een toegangs pakket wilt wijzigen, moet u het bijbehorende beleid openen. Volg deze stappen om de levenscyclus instellingen voor een toegangs pakket te openen.

**Vereiste rol:** globale beheerder, gebruikersbeheerder, cataloguseigenaar of toegangspakketbeheerder

1. Klik in de Azure-portal op **Azure Active Directory** en vervolgens op **Identity Governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **beleids regels** en klik vervolgens op het beleid met de levenscyclus instellingen die u wilt bewerken.

    Het deel venster beleids Details wordt geopend onder aan de pagina.

    ![Toegangs venster pakket-Details van beleid](./media/entitlement-management-shared/policy-details.png)

1. Klik op **bewerken** om het beleid te bewerken.

    ![Toegangs beleid voor pakket bewerken](./media/entitlement-management-shared/policy-edit.png)

1. Klik op het tabblad **levens cyclus** om de levenscyclus instellingen te openen.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Volgende stappen

- [Instellingen voor aanvraag-en goed keuring wijzigen voor een toegangs pakket](entitlement-management-access-package-request-policy.md)
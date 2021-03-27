---
title: Beleid voor Azure-abonnementen beheren
description: Meer informatie over het beheren van Azure-abonnements beleid voor het beheer van de verplaatsing van Azure-abonnementen vanuit en naar directory's.
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 105e090655021ed4046f8880ef9816d7f7ff559f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105631853"
---
# <a name="manage-azure-subscription-policies"></a>Beleid voor Azure-abonnementen beheren

>[!NOTE]
>Deze functie is momenteel beschikbaar als preview-versie en wordt geleidelijk geïmplementeerd, zodat niet iedereen deze ervaring kan zien op de Azure Portal.

Dit artikel helpt u bij het configureren van Azure-abonnements beleid voor abonnements bewerkingen om de verplaatsing van Azure-abonnementen vanuit en naar directory's te beheren.

## <a name="prerequisites"></a>Vereisten

- Alleen [globale beheerders](../../active-directory/roles/permissions-reference.md#global-administrator) van mappen kunnen abonnements beleid bewerken. Voordat u het abonnements beleid bewerkt, moet de globale beheerder de [toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren](../../role-based-access-control/elevate-access-global-admin.md). Vervolgens kunnen ze abonnements beleid bewerken.
- Alle andere gebruikers kunnen alleen de huidige beleids instelling lezen.

## <a name="available-subscription-policy-settings"></a>Beschik bare instellingen voor abonnements beleid

Gebruik de volgende beleids instellingen om de verplaatsing van Azure-abonnementen vanuit en naar directory's te beheren.

### <a name="subscriptions-leaving-aad-directory"></a>Abonnementen die AAD-Directory verlaten

Met het beleid kunnen gebruikers geen abonnementen meer verplaatsen uit de huidige map. [Abonnements eigenaren](../../role-based-access-control/built-in-roles.md#owner) kunnen [de map van een Azure-abonnement wijzigen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) in een andere locatie waar ze lid van zijn. De IT-mede werkers hebben problemen met het beheer, zodat globale beheerders de Directory kunnen wijzigen of niet toestaan.

### <a name="subscriptions-entering-aad-directory"></a>Abonnementen bij het invoeren van AAD Directory

Met het beleid kunnen gebruikers van andere directory's, die toegang hebben tot de huidige map, de abonnementen verplaatsen naar de huidige map. [Abonnements eigenaren](../../role-based-access-control/built-in-roles.md#owner) kunnen [de map van een Azure-abonnement wijzigen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) in een andere locatie waar ze lid van zijn. De IT-mede werkers hebben problemen met het beheer, zodat globale beheerders de Directory kunnen wijzigen of niet toestaan.

### <a name="exempted-users"></a>Uitgesloten gebruikers

Om redenen van beheer kunnen globale beheerders alle verplaatsingen van de abonnements mappen in onze out van de huidige map blok keren. Het is echter mogelijk dat bepaalde gebruikers bewerkingen kunnen uitvoeren. In beide gevallen kan een lijst met uitgesloten gebruikers worden geconfigureerd, waarmee de gebruikers de beleids instelling die van toepassing is op iedereen kan overs Laan.

## <a name="setting-subscription-policy"></a>Abonnements beleid instellen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Navigeer naar **abonnementen**. **Beleids regels beheren** wordt weer gegeven op de opdracht balk.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Scherm opname van het beheren van beleids regels in abonnementen." lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Selecteer **beleid beheren** om details weer te geven over de huidige abonnements beleidsregels die zijn ingesteld voor de Directory. Een globale beheerder met [verhoogde machtigingen](../../role-based-access-control/elevate-access-global-admin.md) kan wijzigingen aanbrengen in de instellingen, waaronder het toevoegen of verwijderen van uitgesloten gebruikers.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Scherm opname waarin specifieke beleids instellingen en uitgesloten gebruikers worden weer gegeven." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. Selecteer onder **wijzigingen opslaan** onder om de wijzigingen op te slaan. De wijzigingen zijn onmiddellijk van kracht.

## <a name="read-subscription-policy"></a>Abonnements beleid lezen

Niet-globale beheerders kunnen nog steeds naar het gedeelte voor het abonnements beleid navigeren om de beleids instellingen van de Directory weer te geven. Ze kunnen geen bewerkingen uitvoeren. Ze kunnen de lijst met uitgesloten gebruikers niet zien om privacy-redenen. Ze kunnen hun globale beheerders weer geven om aanvragen voor beleids wijzigingen te verzenden, mits de Directory-instellingen deze toestaan.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Scherm opname van het beheer beleid in abonnementen als lezer." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Volgende stappen

- Lees de [documentatie voor Cost Management en facturering](../index.yml)
---
title: Op rollen gebaseerde toegang verlenen voor gebruikers
titleSuffix: Azure Maps
description: Op rollen gebaseerd toegangs beheer gebruiken om gebruikers toestemming te geven Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101102815"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Op rollen gebaseerde toegang verlenen aan gebruikers Azure Maps

U verleent op *rollen gebaseerd toegangs beheer voor Azure (Azure RBAC)* door een Azure AD-groep of beveiligings-principals toe te wijzen aan een of meer Azure Maps roldefinities. Als u de definities van Azure-functies wilt weer geven die beschikbaar zijn voor Azure Maps, gaat u naar **toegangs beheer (IAM)**. Selecteer **rollen** en zoek vervolgens naar rollen die beginnen met *Azure Maps*.

* Als u een grote hoeveelheid gebruikers toegang tot Azure Maps efficiënt wilt beheren, raadpleegt u [Azure ad-groepen](../../active-directory/fundamentals/active-directory-manage-groups.md).
* Gebruikers kunnen zich alleen bij de toepassing verifiëren als ze zijn gemaakt in azure AD. Zie [gebruikers toevoegen of verwijderen met Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Meer informatie vindt u in [Azure AD](../../active-directory/fundamentals/index.yml) om een directory voor gebruikers effectief te beheren.

1. Ga naar uw **Azure Maps-account**. Selecteer de roltoewijzing van **toegangs beheer (IAM)**  >  .

    ![Toegang verlenen met behulp van Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Selecteer op **het tabblad roltoewijzingen,** onder **rol**, een ingebouwde Azure Maps roldefinitie, zoals **Azure Maps gegevens lezer** of **Azure Maps gegevensinzender**. Onder **toegang toewijzen aan** selecteert u **Azure AD-gebruiker,-groep of Service-Principal**. Selecteer de principal op naam. Selecteer vervolgens **Opslaan**.

   * Zie voor meer informatie over het [toewijzen van Azure-rollen](../../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Azure Maps ingebouwde roldefinities bieden een zeer grote autorisatie toegang tot veel Azure Maps REST-Api's. Zie [een aangepaste roldefinitie maken en gebruikers toewijzen](../../role-based-access-control/custom-roles.md) aan de definitie van de aangepaste rol om api's voor gebruikers tot een minimum te beperken. Hiermee kunnen gebruikers de mini maal benodigde bevoegdheden voor de toepassing hebben.
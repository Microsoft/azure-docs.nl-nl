---
title: Meer informatie over Azure deny-toewijzingen-Azure RBAC
description: Meer informatie over Azure deny-toewijzingen in azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: a5f17f009caa9306631debf511f2c890f8f2a450
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "82733768"
---
# <a name="understand-azure-deny-assignments"></a>Meer informatie over Azure deny-toewijzingen

Net als bij een roltoewijzing koppelt een *weigerings toewijzing* een reeks weigerings acties toe aan een gebruiker, groep of Service-Principal in een bepaald bereik voor het weigeren van de toegang. Toewijzingen weigeren blok keren dat gebruikers specifieke Azure-resource acties kunnen uitvoeren, zelfs als een roltoewijzing deze toegang verleent.

In dit artikel wordt beschreven hoe weigerings toewijzingen worden gedefinieerd.

## <a name="how-deny-assignments-are-created"></a>Hoe weiger toewijzingen worden gemaakt?

Geweigerde toewijzingen worden door Azure gemaakt en beheerd om resources te beveiligen. Azure-blauw drukken en Azure Managed apps gebruiken toewijzingen weigeren voor het beveiligen van door het systeem beheerde bronnen. Azure-blauw drukken en Azure Managed apps zijn de enige manier waarop het weigeren van toewijzingen kan worden gemaakt. U kunt niet rechtstreeks uw eigen weigerings toewijzingen maken. Zie informatie over het [vergren delen van resources in azure-blauw drukken](../governance/blueprints/concepts/resource-locking.md)voor meer informatie over het gebruik van opdrachten voor het weigeren van een resource.

> [!NOTE]
> U kunt niet rechtstreeks uw eigen weigerings toewijzingen maken.

## <a name="compare-role-assignments-and-deny-assignments"></a>Roltoewijzingen vergelijken en toewijzingen weigeren

Het weigeren van toewijzingen volgt een vergelijkbaar patroon als roltoewijzingen, maar er zijn ook enkele verschillen.

| Mogelijkheid | Roltoewijzing | Toewijzing weigeren |
| --- | --- | --- |
| Toegang verlenen | :heavy_check_mark: |  |
| Toegang weigeren |  | :heavy_check_mark: |
| Kan rechtstreeks worden gemaakt | :heavy_check_mark: |  |
| Toep assen op een bereik | :heavy_check_mark: | :heavy_check_mark: |
| Principals uitsluiten |  | :heavy_check_mark: |
| Overname voor komen op onderliggende bereiken |  | :heavy_check_mark: |
| Toep assen op [klassieke abonnements beheerders](rbac-and-directory-admin-roles.md) toewijzingen |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>Eigenschappen van toewijzing weigeren

 Een weiger toewijzing heeft de volgende eigenschappen:

> [!div class="mx-tableFixed"]
> | Eigenschap | Vereist | Type | Beschrijving |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | Ja | Tekenreeks | De weergave naam van de weigerings toewijzing. De namen moeten uniek zijn voor een bepaald bereik. |
> | `Description` | Nee | Tekenreeks | De beschrijving van de weigerings toewijzing. |
> | `Permissions.Actions` | Ten minste één actie of één DataActions | Teken reeks [] | Een matrix met teken reeksen waarmee de beheer bewerkingen worden opgegeven waarnaar de weigerings toewijzing toegang blokkeert. |
> | `Permissions.NotActions` | Nee | Teken reeks [] | Een matrix met teken reeksen waarmee de beheer bewerkingen worden opgegeven die moeten worden uitgesloten van de weigerings toewijzing. |
> | `Permissions.DataActions` | Ten minste één actie of één DataActions | Teken reeks [] | Een matrix met teken reeksen waarmee de gegevens bewerkingen worden opgegeven waarnaar de weigerings toewijzing toegang blokkeert. |
> | `Permissions.NotDataActions` | Nee | Teken reeks [] | Een matrix met teken reeksen waarmee de gegevens bewerkingen worden opgegeven die moeten worden uitgesloten van de weigerings toewijzing. |
> | `Scope` | Nee | Tekenreeks | Een teken reeks die het bereik opgeeft waarop de weigerings toewijzing van toepassing is. |
> | `DoNotApplyToChildScopes` | Nee | Booleaans | Hiermee geeft u op of de weigerings toewijzing van toepassing is op onderliggende bereiken. De standaard waarde is False. |
> | `Principals[i].Id` | Ja | Teken reeks [] | Een matrix van Azure AD Principal-object-Id's (gebruiker, groep, Service-Principal of beheerde identiteit) waarop de weigerings toewijzing van toepassing is. Stel in op een lege GUID `00000000-0000-0000-0000-000000000000` om alle principals weer te geven. |
> | `Principals[i].Type` | Nee | Teken reeks [] | Een matrix van object typen vertegenwoordigd door principals [i]. id. Stel in `SystemDefined` om alle principals weer te geven. |
> | `ExcludePrincipals[i].Id` | Nee | Teken reeks [] | Een matrix van Azure AD Principal-object-Id's (gebruiker, groep, Service-Principal of beheerde identiteit) waarop de weigerings toewijzing niet van toepassing is. |
> | `ExcludePrincipals[i].Type` | Nee | Teken reeks [] | Een matrix met object typen vertegenwoordigd door ExcludePrincipals [i]. id. |
> | `IsSystemProtected` | Nee | Booleaans | Hiermee wordt aangegeven of deze deny-toewijzing is gemaakt door Azure en niet kan worden bewerkt of verwijderd. Op dit moment worden alle deny-toewijzingen met het systeem beveiligd. |

## <a name="the-all-principals-principal"></a>De principal alle principals

Voor het ondersteunen van weigerings toewijzingen is een door het systeem gedefinieerde principal met *de naam alle principals* geïntroduceerd. Deze principal vertegenwoordigt alle gebruikers, groepen, service-principals en beheerde identiteiten in een Azure AD-adres lijst. Als de principal-ID een nul-GUID is `00000000-0000-0000-0000-000000000000` en het Principal-type is `SystemDefined` , vertegenwoordigt de principal alle principals. In Azure PowerShell uitvoer ziet u dat alle principals er als volgt uitzien:

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

Alle principals kunnen worden gecombineerd met `ExcludePrincipals` om alle principals te weigeren, met uitzonde ring van sommige gebruikers. Voor alle principals gelden de volgende beperkingen:

- Kan alleen worden gebruikt in `Principals` en kan niet worden gebruikt in `ExcludePrincipals` .
- `Principals[i].Type` moet worden ingesteld op `SystemDefined` .

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Nieuwe resources beveiligen met resourcevergrendelingen in Azure Blueprints](../governance/blueprints/tutorials/protect-new-resources.md)
* [Azure deny-toewijzingen weer geven met behulp van de Azure Portal](deny-assignments-portal.md)

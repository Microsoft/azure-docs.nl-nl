---
title: Synapse RBAC-toewijzingen beheren in Synapse Studio
description: In dit artikel wordt beschreven hoe u Synapse RBAC-rollen toewijst en intrekt voor AAD-beveiligings-principals
author: billgib
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: a4016751944e5b7ec5d32dc586e9034db99c9d73
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523461"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Synapse RBAC-roltoewijzingen beheren in Synapse Studio

Synapse RBAC gebruikt rollen om machtigingen toe te wijzen aan gebruikers, groepen en andere beveiligings-principals om toegang tot en gebruik van Synapse-resources en code artefacten mogelijk te maken.  [Meer informatie](./synapse-workspace-synapse-rbac.md)

Dit artikel laat u zien hoe u Synapse RBAC-roltoewijzingen toevoegt en verwijdert.

>[!Note]
>- Als u de Synapse RBAC-roltoewijzingen wilt beheren, moet u de Synapse-beheerdersrol hebben in de werk ruimte of op een bereik op een lager niveau dat de objecten bevat die u wilt beheren. Als u een Synapse-beheerder bent in de werk ruimte, kunt u toegang verlenen tot alle objecten in de werk ruimte. 
>- Om u te helpen weer toegang te krijgen tot een werk ruimte in het geval dat er geen Synapse-beheerders zijn toegewezen of voor u beschikbaar zijn, kunnen gebruikers met machtigingen voor het beheren van **Azure RBAC** -roltoewijzingen in de werk ruimte ook **Synapse RBAC** -roltoewijzingen beheren, waardoor de toewijzing van Synapse-beheerder of andere roltoewijzingen in Synapse RBAC kan worden toegevoegd.
>- Toegang tot SQL-groepen wordt beheerd met behulp van SQL-machtigingen.  Met uitzonde ring van de SQL-beheerders rollen Synapse beheerder en Synapse, verlenen Synapse RBAC-rollen geen toegang tot SQL-groepen.

>[!important]
>- Wijzigingen die zijn aangebracht in Synapse RBAC-roltoewijzingen, kunnen 2-5 minuten in beslag nemen. 
>- Als u Synapse RBAC-machtigingen beheert door het lidmaatschap van beveiligings groepen te wijzigen, worden wijzigingen in het lidmaatschap beheerd met behulp van Azure Active Directory.  Het kan enkele minuten duren voordat wijzigingen in het groepslid maatschap van kracht zijn.

## <a name="open-synapse-studio"></a>Synapse Studio openen  

Als u een rol wilt toewijzen aan een gebruiker, groep, Service-Principal of beheerde identiteit, opent u eerst [de Synapse Studio](https://web.azuresynapse.net/) en selecteert u uw werk ruimte. 

![Aanmelden bij werk ruimte](./media/common/login-workspace.png) 
 
 Wanneer u uw werk ruimte hebt geopend, vouwt u de sectie **beveiliging** aan de linkerkant uit en selecteert u **toegangs beheer**. 

 ![Selecteer Access Control in de sectie Beveiliging links](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

In het scherm toegangs beheer worden de huidige roltoewijzingen weer gegeven.  U kunt de lijst filteren op Principal-naam of e-mail adres en selectief de object typen, rollen of bereiken filteren die zijn opgenomen. In dit scherm kunt u roltoewijzingen toevoegen of verwijderen.  

## <a name="add-a-synapse-role-assignment"></a>Een roltoewijzing voor een Synapse toevoegen

Selecteer op het scherm toegangs beheer de optie **+ toevoegen** om een nieuwe roltoewijzing te maken

![Klik op + toevoegen om een nieuwe roltoewijzing te maken](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

Op het tabblad toewijzing van rollen toevoegen kunt u roltoewijzingen maken voor het bereik van de werk ruimte of het item werk ruimte. 

## <a name="add-workspace-scoped-role-assignment"></a>Roltoewijzing voor werk ruimte toevoegen

Selecteer eerst **werk ruimte** als bereik en selecteer vervolgens de **Synapse RBAC-rol**.  Selecteer de **principal (s)** die u wilt toewijzen aan de rol en maak vervolgens de functie toewijzing (en). 

![Toewijzing werk ruimte toevoegen-rol selecteren](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

De toegewezen rol wordt toegepast op alle toepasselijke objecten in de werk ruimte.

## <a name="add-workspace-item-scoped-role-assignment"></a>Functie toewijzing voor het bereik van de werk ruimte toevoegen

Als u een rol wilt toewijzen aan een nauw keurig bereik, selecteert u **werkruimte item** als bereik en selecteert u vervolgens het **item type** bereik.       

![Toewijzing van rol van werk ruimte-item toevoegen-item type selecteren](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Selecteer het specifieke **item** dat moet worden gebruikt als bereik en selecteer vervolgens de **rol** die u wilt toewijzen in de vervolg keuzelijst.  In de vervolg keuzelijst worden alleen de rollen weer gegeven die geldig zijn voor het geselecteerde item type. [Meer informatie](https://go.microsoft.com/fwlink/?linkid=2148306).  

![Rol toewijzing werk ruimte-item toevoegen-rol selecteren](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
**Selecteer vervolgens de principal (s)** waaraan de rol moet worden toegewezen.  U kunt iteratief meerdere principals selecteren.  Selecteer **Toep assen** om de functie toewijzing (en) te maken.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Een Synapse RBAC-roltoewijzing verwijderen

Als u Synapse RBAC Access wilt intrekken, verwijdert u de juiste roltoewijzingen.  Gebruik in het scherm toegangs beheer de filters om de roltoewijzing te vinden die moeten worden verwijderd.  Controleer de roltoewijzingen en selecteer vervolgens **toegang verwijderen**.   

![Een roltoewijzing verwijderen om de toegang te verwijderen](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Houd er rekening mee dat wijzigingen in roltoewijzingen 2-5 minuten in beslag nemen.   

## <a name="next-steps"></a>Volgende stappen

[Inzicht in de Synapse RBAC-rollen die nodig zijn om algemene taken uit te voeren](./synapse-workspace-understand-what-role-you-need.md) 

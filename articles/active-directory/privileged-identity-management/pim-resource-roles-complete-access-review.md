---
title: De toegangs beoordeling van Azure-resource rollen in PIM volt ooien-Azure AD | Microsoft Docs
description: Meer informatie over het volt ooien van een toegangs beoordeling van Azure resource roles Privileged Identity Management in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1601c9fc750df1f8e18bdaa072ede1b5ee164329
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84743810"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Een toegangs beoordeling van Azure-resource rollen in Privileged Identity Management volt ooien

Beheerders van geprivilegieerde rollen kunnen bevoorrechte toegang controleren nadat ze [een toegangs beoordeling hebben gestart](pim-resource-roles-start-access-review.md). Privileged Identity Management (PIM) in Azure Active Directory (Azure AD) verzendt automatisch een e-mail bericht waarin gebruikers wordt gevraagd hun toegang te controleren. Als een gebruiker geen e-mail bericht ontvangt, kunt u de instructies voor het [uitvoeren van een toegangs beoordeling](pim-resource-roles-perform-access-review.md)verzenden.

Volg de stappen in dit artikel om de controle te beheren en de resultaten te bekijken nadat de toegangs beoordelings periode is afgelopen of nadat alle gebruikers de zelf beoordeling hebben voltooid.

## <a name="manage-access-reviews"></a>Toegangs beoordelingen beheren

1. Ga naar de [Azure Portal](https://portal.azure.com/). Selecteer de **Azure-resources** service op het dash board.

2. Selecteer uw resource.

3. Selecteer de sectie **toegangs beoordelingen** van het dash board.

    ![Azure-resources-lijst met toegangs beoordelingen met rol, eigenaar, begin datum, eind datum en status](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Selecteer de toegangs beoordeling die u wilt beheren.

Op de detail pagina voor de toegangs beoordeling zijn er een aantal opties voor het beheren van die beoordeling. Dit zijn de opties:

![Opties voor het beheren van een beoordeling-stoppen, opnieuw instellen, Toep assen, verwijderen](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Stoppen

Alle toegangs beoordelingen hebben een eind datum. Selecteer **stoppen** om de bewerking te volt ooien. Gebruikers die hun beoordeling niet op dit moment hebben voltooid, kunnen deze niet meer volt ooien nadat de controle is gestopt. U kunt een beoordeling niet opnieuw starten nadat deze is gestopt.

### <a name="reset"></a>Opnieuw instellen

U kunt een toegangs beoordeling opnieuw instellen om alle beslissingen te verwijderen die erop zijn gemaakt. Nadat u een toegangs beoordeling opnieuw hebt ingesteld, worden alle gebruikers gemarkeerd als niet opnieuw gecontroleerd.

### <a name="apply"></a>Toepassen

Wanneer een toegangs beoordeling is voltooid, selecteert u **Toep assen** om het resultaat van de beoordeling te implementeren. Als de toegang van een gebruiker bij de controle is geweigerd, wordt met deze stap de roltoewijzing verwijderd.  

### <a name="delete"></a>Verwijderen

Als u de controle niet meer wilt doen, verwijdert u deze. Selecteer **verwijderen** yo de beoordeling uit de privileged Identity Management-service verwijderen.

## <a name="results"></a>Resultaten

Op de pagina **resultaten** kunt u een lijst met de resultaten van de beoordeling bekijken en downloaden.

![Pagina met resultaten met vermelding van gebruikers, resultaat, reden, gereviseerd door, toegepast op en resultaat Toep assen](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Reviewers

Revisoren weer geven en toevoegen aan uw bestaande toegangs beoordeling. Herinner revisoren om hun beoordelingen te volt ooien.

![Pagina revisoren naam en user principal name](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling starten voor Azure-resource rollen in Privileged Identity Management](pim-resource-roles-start-access-review.md)
- [Een toegangs beoordeling van mijn Azure-resource rollen uitvoeren in Privileged Identity Management](pim-resource-roles-perform-access-review.md)

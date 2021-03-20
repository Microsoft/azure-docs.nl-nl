---
title: De toegangs beoordeling van Azure AD-rollen in PIM volt ooien-Azure AD | Microsoft Docs
description: Meer informatie over het uitvoeren van een toegangs beoordeling van Azure AD-rollen in Azure AD Privileged Identity Management (PIM) en het weer geven van de resultaten
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa7e8089fbbf2ee653100a05383fdbdc877ffda4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84742213"
---
# <a name="complete-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Een toegangs beoordeling van Azure AD-rollen in Privileged Identity Management volt ooien

Beheerders met bevoegde rollen kunnen bevoorrechte toegang controleren zodra een [toegangs beoordeling is gestart](pim-how-to-start-security-review.md).  Met Privileged Identity Management (PIM) wordt automatisch een e-mail verzonden naar gebruikers in uw Azure Active Directory (Azure AD)-organisatie om hen te vragen hun toegang te controleren. Als een gebruiker geen e-mail bericht heeft ontvangen, kunt u de instructies [voor het uitvoeren van een toegangs beoordeling](pim-how-to-perform-security-review.md)verzenden.

Volg de stappen in dit artikel om de controle te beheren en de resultaten te bekijken nadat de toegangs beoordelings periode is afgelopen of als alle gebruikers hun eigen beoordeling hebben voltooid.

## <a name="manage-access-reviews"></a>Toegangs beoordelingen beheren

1. Ga naar de [Azure Portal](https://portal.azure.com/) en selecteer de **Azure AD privileged Identity Management** -service op uw dash board.
1. Selecteer de sectie **toegangs beoordelingen** van het dash board.
1. Selecteer de toegangs beoordeling die u wilt beheren.

Op de Blade Details van de toegangs beoordeling vindt u een aantal opties voor het beheren van die beoordeling.

![Privileged Identity Management toegangs beoordelings knoppen-scherm afbeelding](./media/pim-how-to-complete-review/review-buttons.png)

### <a name="remind"></a>Herinneren

Als er een toegangs beoordeling is ingesteld zodat gebruikers zichzelf kunnen bekijken, verzendt de knop **herinneren** een melding.

### <a name="stop"></a>Stoppen

Alle toegangs beoordelingen hebben een eind datum, maar u kunt de knop **stoppen** gebruiken om deze te volt ooien. Als een of meer gebruikers nog niet zijn gecontroleerd, kunnen ze niet meer nadat u de beoordeling hebt gestopt. U kunt een beoordeling niet opnieuw starten nadat deze is gestopt.

### <a name="apply"></a>Toepassen

Wanneer een toegangs beoordeling is voltooid, omdat u de eind datum hebt bereikt of hand matig hebt gestopt, implementeert de knop **Toep assen** het resultaat van de controle. Als de toegang van een gebruiker bij de controle is geweigerd, is dit de stap die de roltoewijzing verwijdert.  

### <a name="export"></a>Exporteren

Als u de resultaten van de toegangs beoordeling hand matig wilt Toep assen, kunt u de controle exporteren. Met de knop **exporteren** wordt nu een CSV-bestand gedownload. U kunt de resultaten beheren in Excel of andere Program ma's waarmee CSV-bestanden worden geopend.

### <a name="delete"></a>Verwijderen

Als u niet meer wilt weten over de beoordeling, verwijdert u deze. Met de knop **verwijderen** verwijdert u de controle van de privileged Identity Management-service.

> [!IMPORTANT]
> U hoeft deze destructieve wijziging niet te bevestigen. Controleer daarom of u die beoordeling wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [Een toegangs beoordeling starten voor Azure AD-rollen in Privileged Identity Management](pim-how-to-start-security-review.md)
- [Een toegangs beoordeling van mijn Azure AD-rollen uitvoeren in Privileged Identity Management](pim-how-to-perform-security-review.md)

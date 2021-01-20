---
title: Onopzettelijke verwijderingen van Cloud synchronisatie Azure AD Connect
description: In dit onderwerp wordt beschreven hoe u de functie voor onbedoeld verwijderen gebruikt om verwijderingen te voor komen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ef7b6d9495b1431e03808b830671e839b90d436
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613804"
---
# <a name="accidental-delete-prevention"></a>Onbedoeld verwijderen voor komen

In het volgende document wordt de functie voor onbedoeld verwijderen voor Azure AD Connect Cloud synchronisatie beschreven.  De functie voor onbedoeld verwijderen is ontworpen om u te beschermen tegen onbedoelde configuratie wijzigingen en wijzigingen in uw on-premises Directory die van invloed zijn op veel gebruikers en groepen.  Met deze functie kunt u het volgende doen:

- Configureer de mogelijkheid om onbedoelde verwijderingen automatisch te voor komen. 
- Het aantal objecten (drempel) instellen waarboven de configuratie van kracht wordt 
- Stel een e-mail adres voor meldingen in, zodat ze een e-mail melding kunnen ontvangen zodra de desbetreffende synchronisatie taak in quarantaine is geplaatst voor dit scenario 

Als u deze functie wilt gebruiken, stelt u de drempel waarde in voor het aantal objecten dat, indien verwijderd, de synchronisatie moet worden gestopt.  Als dit aantal is bereikt, wordt de synchronisatie gestopt en wordt er een melding verzonden naar het opgegeven e-mail adres.  Zo kunt u onderzoeken wat er gebeurt.


## <a name="configure-accidental-delete-prevention"></a>Onopzettelijke Verwijder preventie configureren
Volg de onderstaande stappen om de nieuwe functie te gebruiken.


1.  Selecteer in Azure Portal **Azure Active Directory**.
2.  Selecteer **Azure AD Connect**.
3.  Selecteer **Cloud synchronisatie beheren**.
4. Selecteer uw configuratie onder **configuratie**.
5. Onder **instellingen** vult u het volgende in:
    - **E-mail melding** : e-mail gebruikt voor meldingen
    - **Onopzettelijke verwijderingen voor komen** : Schakel dit selectie vakje in om de functie in te scha kelen
    - **Drempel waarde voor onbedoeld verwijderen** -Voer een aantal objecten in voor het activeren van de synchronisatie stoppen en-melding

![Onbedoeld verwijderen](media/how-to-accidental-deletes/accident-1.png)

## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect Cloud synchronisatie?](what-is-cloud-sync.md)
- [Azure AD Connect Cloud synchronisatie installeren](how-to-install.md)
 


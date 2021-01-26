---
title: Onopzettelijke verwijderingen van Cloud synchronisatie Azure AD Connect
description: In dit onderwerp wordt beschreven hoe u de functie voor onbedoeld verwijderen gebruikt om verwijderingen te voor komen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785180"
---
# <a name="accidental-delete-prevention"></a>Onbedoeld verwijderen voor komen

In het volgende document wordt de functie voor onbedoeld verwijderen voor Azure AD Connect Cloud synchronisatie beschreven.  De functie voor onbedoeld verwijderen is ontworpen om u te beschermen tegen onbedoelde configuratie wijzigingen en wijzigingen in uw on-premises Directory die van invloed zijn op veel gebruikers en groepen.  Met deze functie kunt u het volgende doen:

- Configureer de mogelijkheid om onbedoelde verwijderingen automatisch te voor komen. 
- Het aantal objecten (drempel) instellen waarboven de configuratie van kracht wordt 
- Stel een e-mail adres voor meldingen in zodat ze een e-mail melding kunnen ontvangen zodra de desbetreffende synchronisatie taak in quarantaine is geplaatst voor dit scenario 

Als u deze functie wilt gebruiken, stelt u de drempel waarde in voor het aantal objecten dat, indien verwijderd, de synchronisatie moet worden gestopt.  Als dit aantal is bereikt, wordt de synchronisatie gestopt en wordt er een melding verzonden naar het opgegeven e-mail adres.  Met deze melding kunt u onderzoeken wat er gebeurt.


## <a name="configure-accidental-delete-prevention"></a>Onopzettelijke Verwijder preventie configureren
Volg de onderstaande stappen om de nieuwe functie te gebruiken.


1.  Selecteer in Azure Portal **Azure Active Directory**.
2.  Selecteer **Azure AD Connect**.
3.  Selecteer **Cloud synchronisatie beheren**.
4. Selecteer uw configuratie onder **configuratie**.
5. Onder **instellingen** vult u het volgende in:
    - **E-mail melding** : e-mail gebruikt voor meldingen
    - **Onopzettelijke verwijderingen voor komen** : Schakel dit selectie vakje in om de functie in te scha kelen
    - **Drempel waarde voor onopzettelijk verwijderen** : Voer het aantal objecten in om de synchronisatie te stoppen en een melding te verzenden

![Onbedoeld verwijderen](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Herstellen van een onbedoeld verwijderings exemplaar
Als u een onbedoelde verwijdering ondervindt, ziet u deze op de status van de configuratie van uw inrichtings agent.  De **drempel waarde voor verwijderen is overschreden**.
 
![Onopzettelijke verwijderings status](media/how-to-accidental-deletes/delete-1.png)

Als u op **drempel waarde voor verwijderen** klikt, wordt de informatie over de synchronisatie status weer geven.  Hiermee krijgt u meer informatie. 
 
 ![Synchronisatie status](media/how-to-accidental-deletes/delete-2.png)

Door met de rechter muisknop op de weglatings tekens te klikken, krijgt u de volgende opties:
 - Inrichtings logboek weer geven
 - Agent weer geven
 - Verwijderingen toestaan

 ![Klikken met de rechtermuisknop](media/how-to-accidental-deletes/delete-3.png)

Met behulp van het **inrichtings logboek weer geven** kunt u de **StagedDelete** -vermeldingen zien en de informatie bekijken die is verstrekt op de gebruikers die zijn verwijderd.
 
 ![Inrichtingslogboeken](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Verwijderingen toestaan

Met de actie verwijderingen **toestaan** worden de objecten verwijderd die de drempel voor onbedoeld verwijderen hebben geactiveerd.  Gebruik de volgende procedure om de verwijderingen te accepteren.  

1. Klik met de rechter muisknop op de weglatings tekens en selecteer **Verwijderingen toestaan**.
2. Klik op **Ja** in de bevestiging om de verwijderingen toe te staan.
 
 ![Ja bij bevestiging](media/how-to-accidental-deletes/delete-4.png)

3. U ziet dat de verwijdering is geaccepteerd en dat de status in orde wordt weer gegeven met de volgende cyclus. 
 
 ![Verwijderingen accepteren](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Verwijderingen afwijzen

Als u de verwijderingen niet wilt toestaan, moet u het volgende doen:
- de bron van de verwijderingen onderzoeken
- Los het probleem op (de organisatie-eenheid is bijvoorbeeld per ongeluk verplaatst en u hebt deze nu weer opnieuw toegevoegd aan het bereik)
- **Synchronisatie van opnieuw starten** uitvoeren op de configuratie van de agent

## <a name="next-steps"></a>Volgende stappen 

- [Azure AD Connect het oplossen van Cloud synchronisatie?](how-to-troubleshoot.md)
- [Azure AD Connect fout codes voor Cloud synchronisatie](reference-error-codes.md)
 


---
title: Inrichting op aanvraag in Azure AD Connect Cloud synchronisatie
description: In dit artikel wordt beschreven hoe u de functie Cloud synchronisatie van Azure AD Connect gebruikt om configuratie wijzigingen te testen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554272"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Inrichting op aanvraag in Azure AD Connect Cloud synchronisatie

U kunt de Cloud synchronisatie functie van Azure Active Directory (Azure AD) gebruiken om de configuratie wijzigingen te testen door deze wijzigingen toe te passen op één gebruiker. Met deze inrichting op aanvraag kunt u valideren en controleren of de wijzigingen die zijn aangebracht in de configuratie correct zijn toegepast en correct worden gesynchroniseerd met Azure AD.  

> [!IMPORTANT] 
> Wanneer u inrichtings op aanvraag gebruikt, worden de bereik filters niet toegepast op de gebruiker die u hebt geselecteerd. U kunt inrichten op aanvraag gebruiken voor gebruikers die zich niet in de organisatie-eenheden bevinden die u hebt opgegeven.

## <a name="validate-a-user"></a>Een gebruiker valideren
Voer de volgende stappen uit om inrichting op aanvraag te gebruiken:

1.  Selecteer in Azure Portal **Azure Active Directory**.
2.  Selecteer **Azure AD Connect**.
3.  Selecteer **Cloud synchronisatie beheren**.

    ![Scherm afbeelding met de koppeling voor het beheren van Cloud synchronisatie.](media/how-to-install/install-6.png)
4. Selecteer uw configuratie onder **configuratie**.
5. Onder **valideren** selecteert u de knop **een gebruiker inrichten** . 

   ![Scherm afbeelding met de knop voor het inrichten van een gebruiker.](media/how-to-on-demand-provision/on-demand-2.png)

6. Op het scherm **inrichten op aanvraag** voert u de DN-naam van een gebruiker in en selecteert u de knop **inrichten** .  
 
   ![Scherm afbeelding met een gebruikers naam en een inrichtings knop.](media/how-to-on-demand-provision/on-demand-3.png)
7. Nadat het inrichten is voltooid, wordt het scherm geslaagd weer gegeven met vier groene vinkjes. Er worden fouten weer gegeven aan de linkerkant.

   ![Scherm opname waarin geslaagde inrichting wordt weer gegeven.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Details over het inrichten ophalen
Nu kunt u de gebruikers gegevens bekijken en bepalen of de wijzigingen die u hebt aangebracht in de configuratie zijn toegepast. In de rest van dit artikel worden de afzonderlijke secties beschreven die in de details van een gesynchroniseerde gebruiker worden weer gegeven.

### <a name="import-user"></a>Gebruiker importeren
De sectie **gebruiker importeren** bevat informatie over de gebruiker die is geïmporteerd uit Active Directory. Dit is wat de gebruiker ziet voordat deze wordt ingericht in azure AD. Selecteer de koppeling **Details weer geven** om deze informatie weer te geven.

![Scherm afbeelding van de knop voor het weer geven van Details van een geïmporteerde gebruiker.](media/how-to-on-demand-provision/on-demand-5.png)

Met behulp van deze informatie kunt u de verschillende kenmerken (en hun waarden) zien die zijn geïmporteerd. Als u een aangepaste kenmerk toewijzing hebt gemaakt, kunt u hier de waarde zien.

![Scherm opname van de gebruikers details.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Bepalen of de gebruiker binnen het bereik ligt
De sectie **bepalen of de gebruiker zich in het bereik bevindt** geeft informatie over of de gebruiker die is geïmporteerd in azure AD, binnen het bereik valt. Selecteer de koppeling **Details weer geven** om deze informatie weer te geven.

![Scherm afbeelding van de knop voor het weer geven van Details over het gebruikers bereik.](media/how-to-on-demand-provision/on-demand-7.png)

Met behulp van deze informatie kunt u zien of de gebruiker binnen het bereik is.

![Scherm opname van Details van het gebruikers bereik.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Overeenkomende gebruikers tussen het bron-en doel systeem
De sectie **gebruiker overeenkomen tussen bron-en doel systeem** bevat informatie over de vraag of de gebruiker al bestaat in azure AD en of een koppeling moet worden uitgevoerd in plaats van een nieuwe gebruiker in te richten. Selecteer de koppeling **Details weer geven** om deze informatie weer te geven.

![Scherm afbeelding van de knop voor het weer geven van Details van een overeenkomende gebruiker.](media/how-to-on-demand-provision/on-demand-8.png)

Met behulp van deze informatie kunt u zien of er een overeenkomst is gevonden of dat er een nieuwe gebruiker wordt gemaakt.

![Scherm opname van de gebruikers gegevens.](media/how-to-on-demand-provision/on-demand-11.png)

In de overeenkomende Details wordt een bericht weer gegeven met een van de drie volgende bewerkingen:
- **Maken**: er wordt een gebruiker gemaakt in azure AD.
- **Update**: een gebruiker wordt bijgewerkt op basis van een wijziging die is aangebracht in de configuratie.
- **Verwijderen**: een gebruiker wordt verwijderd uit Azure AD.

Afhankelijk van het type bewerking dat u hebt uitgevoerd, is het bericht afhankelijk.

### <a name="perform-action"></a>Actie uitvoeren
De sectie **actie uitvoeren** bevat informatie over de gebruiker die is ingericht of geëxporteerd naar Azure AD nadat de configuratie is toegepast. Dit is wat de gebruiker ziet nadat deze is ingericht in azure AD. Selecteer de koppeling **Details weer geven** om deze informatie weer te geven.

![Scherm afbeelding van de knop voor het weer geven van Details van een uitgevoerde actie.](media/how-to-on-demand-provision/on-demand-9.png)

Met behulp van deze informatie kunt u de waarden van de kenmerken zien nadat de configuratie is toegepast. Zien ze er ongeveer uit wat er is geïmporteerd, of zijn ze verschillend? Is de configuratie met succes toegepast?  

Met dit proces kunt u de kenmerk transformatie traceren terwijl deze door de Cloud en in uw Azure AD-Tenant wordt verplaatst.

![Scherm opname van Details van traceer kenmerken.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Volgende stappen 

- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)
- [Azure AD Connect Cloud synchronisatie installeren](how-to-install.md)
 
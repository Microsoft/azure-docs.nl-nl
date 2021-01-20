---
title: Nieuwe agent configuratie voor de Cloud synchronisatie Azure AD Connect
description: In dit artikel wordt beschreven hoe u Cloud synchronisatie installeert.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eacf3cad0a610ae75f38dbb6f1bd7bdab816d54
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613785"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Een nieuwe configuratie maken voor Azure AD Connect Cloud synchronisatie

Nadat u de Azure AD Connect-inrichtings agent hebt geïnstalleerd, moet u zich aanmelden bij de Azure Portal en configureren. Volg deze stappen om de agent in te scha kelen.

## <a name="configure-provisioning"></a>Inrichting configureren
Volg deze stappen voor het configureren van de inrichting.

 1. Selecteer in het Azure Portal **Azure Active Directory**
 2. Selecteer **Azure AD Connect**.
 3. Selecteer **Cloud synchronisatie beheren**.

 ![Inrichting beheren](media/how-to-install/install-6.png)
 
 4. Selecteer **nieuwe configuratie**.
 5. Selecteer uw domein op het scherm configuratie en geef aan of wachtwoord-hash-synchronisatie moet worden ingeschakeld.  Klik op **maken**.  
 
 ![Nieuwe configuratie maken](media/how-to-configure/configure-1.png)


 6.  Het scherm inrichtings configuratie bewerken wordt geopend.

   ![Configuratie bewerken](media/how-to-configure/con-1.png)

 7. Voer een **e-mail melding** in. Deze e-mail wordt gewaarschuwd wanneer het inrichten niet in orde is.  Het is raadzaam om **onbedoeld verwijderen te voor komen** en de drempel voor **onbedoeld verwijderen** in te stellen op een getal waarvoor u een melding wilt ontvangen.  Zie voor meer informatie [onbedoelde verwijderingen](#accidental-deletions) hieronder.
 8. Verplaats de selector om deze in te scha kelen en selecteer Opslaan.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Scopeing inrichten voor specifieke gebruikers en groepen
U kunt het bereik van de agent voor het synchroniseren van specifieke gebruikers en groepen met behulp van on-premises Active Directory groepen of organisatie-eenheden. U kunt geen groepen en organisatie-eenheden configureren binnen een configuratie. 

 1.  Selecteer in Azure Portal **Azure Active Directory**.
 2. Selecteer **Azure AD Connect**.
 3. Selecteer **Cloud synchronisatie beheren**.
 4. Selecteer uw configuratie onder **configuratie**.

 ![Configuratie sectie](media/how-to-configure/scope-1.png)
 
 5. Selecteer onder **configureren** de optie **bereik filters bewerken** om het bereik van de configuratie regel te wijzigen.
 ![Bereik bewerken](media/how-to-configure/scope-3.png)
 7. Aan de rechter kant kunt u het bereik wijzigen.  Klik op **gereed**  en **Sla** op wanneer u klaar bent.
 8. Nadat u het bereik hebt gewijzigd, moet u de [inrichting opnieuw opstarten](#restart-provisioning) om een onmiddellijke synchronisatie van de wijzigingen te initiëren.

## <a name="attribute-mapping"></a>Kenmerktoewijzing
Met Azure AD Connect Cloud synchronisatie kunt u eenvoudig kenmerken toewijzen tussen uw on-premises gebruikers-en groeps objecten en de objecten in azure AD.  U kunt de standaardtoewijzingen van kenmerken aanpassen op basis van de behoeften van uw bedrijf. Dit betekent dat u bestaande kenmerktoewijzingen kunt wijzigen of verwijderen, of nieuwe kenmerktoewijzingen kunt maken.  Zie [kenmerk toewijzing](how-to-attribute-mapping.md)voor meer informatie.

## <a name="on-demand-provisioning"></a>Inrichting on-demand
Met Azure AD Connect Cloud synchronisatie kunt u configuratie wijzigingen testen door deze wijzigingen toe te passen op één gebruiker of groep.  U kunt dit gebruiken om te valideren en te controleren of de wijzigingen die zijn aangebracht in de configuratie correct zijn toegepast en correct worden gesynchroniseerd met Azure AD.  Zie [on-demand inrichten](how-to-on-demand-provision.md)voor meer informatie.

## <a name="accidental-deletions"></a>Onbedoeld verwijderen
De functie voor onbedoeld verwijderen is ontworpen om u te beschermen tegen onbedoelde configuratie wijzigingen en wijzigingen in uw on-premises Directory die van invloed zijn op veel gebruikers en groepen.  Met deze functie kunt u het volgende doen:

- Configureer de mogelijkheid om onbedoelde verwijderingen automatisch te voor komen. 
- Het aantal objecten (drempel) instellen waarboven de configuratie van kracht wordt 
- Stel een e-mail adres voor meldingen in, zodat ze een e-mail melding kunnen ontvangen zodra de desbetreffende synchronisatie taak in quarantaine is geplaatst voor dit scenario 

Zie voor meer informatie [onbedoeld verwijderen](how-to-accidental-deletes.md)

## <a name="quarantines"></a>In quarantaine geplaatst
Met Cloud Sync wordt de status van uw configuratie gecontroleerd en worden de beschadigde objecten in een quarantaine status geplaatst. Als de meeste of alle aanroepen voor het doel systeem consistent mislukken vanwege een fout, bijvoorbeeld ongeldige beheerders referenties, wordt de synchronisatie taak gemarkeerd als in quarantaine.  Zie voor meer informatie de sectie probleem oplossing in [quarantaine](how-to-troubleshoot.md#provisioning-quarantined-problems).

## <a name="restart-provisioning"></a>Inrichting opnieuw starten 
Als u niet wilt wachten op de volgende geplande uitvoering, moet u de inrichtings uitvoering activeren met behulp van de knop **inrichting opnieuw opstarten** . 
 1.  Selecteer in Azure Portal **Azure Active Directory**.
 2. Selecteer **Azure AD Connect**.
 3.  Selecteer **Cloud synchronisatie beheren**.
 4. Selecteer uw configuratie onder **configuratie**.

   ![Configuratie selectie voor het opnieuw opstarten van de inrichting](media/how-to-configure/scope-1.png)

 5. Selecteer aan de bovenkant de optie **inrichting opnieuw opstarten**.

## <a name="remove-a-configuration"></a>Een configuratie verwijderen
Voer de volgende stappen uit om een configuratie te verwijderen.

 1.  Selecteer in Azure Portal **Azure Active Directory**.
 2. Selecteer **Azure AD Connect**.
 3. Selecteer **Cloud synchronisatie beheren**.
 4. Selecteer uw configuratie onder **configuratie**.
   
   ![Configuratie selectie voor het verwijderen van de configuratie](media/how-to-configure/scope-1.png)

 5. Selecteer boven aan het configuratie scherm de optie **verwijderen**.

>[!IMPORTANT]
>Er is geen bevestiging voordat een configuratie wordt verwijderd. Zorg ervoor dat dit de actie is die u wilt uitvoeren voordat u **verwijderen** selecteert.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud synchronisatie?](what-is-cloud-sync.md)

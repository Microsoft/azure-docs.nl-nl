---
title: Bekende problemen met het inrichten van toepassingen in azure AD
description: Meer informatie over bekende problemen bij het werken met geautomatiseerde toepassings inrichting in azure AD.
author: kenwith
ms.author: kenwith
manager: daveba
services: active-directory
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.reviewer: arvinh
ms.openlocfilehash: 9eba671f6c824c8c88388f2b9d61512dfb1d122f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256640"
---
# <a name="known-issues-application-provisioning"></a>Bekende problemen: toepassings inrichting
Bekende problemen waarvan u zich bewust moet zijn bij het werken met app-inrichting. U kunt feedback geven over de Application Provisioning-Service op UserVoice, de [Azure AD-toepassing inrichten UserVoice](https://aka.ms/appprovisioningfeaturerequest). We kijken naar UserVoice zodat we de service kunnen verbeteren. 

> [!NOTE]
> Dit is geen uitgebreide lijst met bekende problemen. Als u een probleem kent dat niet wordt vermeld, kunt u aan de onderkant van de pagina feedback geven.

## <a name="authorization"></a>Autorisatie 

**Kan niet opslaan nadat de verbindings test is geslaagd**

Als u een verbinding wel of niet kunt opslaan, hebt u de toegestane opslag limiet voor referenties overschreden. Zie probleem bij het [opslaan van beheerders referenties](./user-provisioning.md)voor meer informatie.

**Kan niet opslaan**

De Tenant-URL, het geheim token en de e-mail melding moeten worden ingevuld om op te slaan. U kunt slechts een van beide opgeven. 

**Kan de inrichtings modus niet opnieuw instellen op hand matig**

Nadat u de inrichting voor de eerste keer hebt geconfigureerd, ziet u dat de inrichtings modus is overgeschakeld van hand matig naar automatisch. U kunt deze niet weer wijzigen in hand matig. U kunt het inrichten echter uitschakelen via de gebruikers interface. Het in-of uitschakelen van het inrichten in de gebruikers interface doet hetzelfde als het instellen van de vervolg keuzelijst in hand matig.  


## <a name="attribute-mappings"></a>Kenmerk toewijzingen 

**SamAccountName of User type van het kenmerk is niet beschikbaar als bron kenmerk**

De kenmerken SamAccountName en User type zijn standaard niet beschikbaar als bron kenmerk. Breid uw schema uit om het kenmerk toe te voegen. U kunt de kenmerken toevoegen aan de lijst met beschik bare bron kenmerken door het schema uit te breiden. Zie [ontbrekend bron kenmerk](user-provisioning-sync-attributes-for-mapping.md)voor meer informatie. 

**Bron kenmerk vervolg keuzelijst ontbreekt voor schema-uitbrei ding**

Uitbrei dingen van uw schema kunnen soms ontbreken in de vervolg keuzelijst voor het bron kenmerk in de gebruikers interface. Ga naar de geavanceerde instellingen van uw kenmerk toewijzingen en voeg de kenmerken hand matig toe. Zie [kenmerk toewijzingen aanpassen](customize-application-attributes.md)voor meer informatie.

**NULL-kenmerk kan niet worden ingericht**

Azure AD kan momenteel geen null-kenmerken inrichten. Als een kenmerk null is voor het gebruikers object, wordt het overgeslagen. 

**Maximum aantal tekens voor expressies voor kenmerk toewijzing**

Expressies voor kenmerk toewijzing mogen Maxi maal 10.000 tekens bevatten. 

**Niet-ondersteunde bereik filters**

Directory-extensies, appRoleAssignments, User type en accountExpires worden niet ondersteund als bereik filters.


## <a name="service-issues"></a>Serviceproblemen 

**Niet-ondersteunde scenario's**

- Het inrichten van wacht woorden wordt niet ondersteund. 
- Het inrichten van geneste groepen wordt niet ondersteund. 
- Het inrichten van B2C-tenants wordt niet ondersteund vanwege de grootte van de tenants.
- Niet alle inrichtings-apps zijn beschikbaar in alle Clouds. Bijvoorbeeld, Atlassian is nog niet beschikbaar in de Government Cloud. We werken samen met app-ontwikkel aars om hun apps te gebruiken voor alle Clouds.

**Automatische inrichting is niet beschikbaar op mijn op OIDC gebaseerde toepassing**

Als u een app-registratie maakt, wordt de bijbehorende service-principal in Enter prise-apps niet ingeschakeld voor automatische gebruikers inrichting. U moet een aanvraag indienen om de app toe te voegen aan de galerie als deze is bedoeld voor gebruik door meerdere organisaties, of u kunt een tweede niet-galerie-app maken voor het inrichten. 

**Het inrichtings interval is vast**

De [tijd](./application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users) tussen de inrichtings cycli kan momenteel niet worden geconfigureerd. 

**Wijzigingen die niet worden verplaatst van de doel-app naar Azure AD**

De app Provisioning Service is niet op de hoogte van wijzigingen die zijn aangebracht in externe apps. Er wordt dus geen actie ondernomen om terug te draaien. De app Provisioning Service is afhankelijk van de wijzigingen die zijn aangebracht in azure AD. 

**Overschakelen van alles synchroniseren naar synchronisatie is niet actief**

Nadat u het bereik hebt gewijzigd van ' Alles synchroniseren ' toegewezen ', moet u er ook voor zorgen dat de wijziging wordt doorgevoerd. U kunt de computer opnieuw opstarten vanuit de gebruikers interface.

**De inrichtings cyclus gaat verder tot de voltooiing**

Bij het instellen van inrichting `enabled = off` of voor het aanwijzen van stoppen wordt de huidige inrichtings cyclus voortgezet totdat de bewerking is voltooid. De service stopt met het uitvoeren van toekomstige cycli totdat u de inrichting opnieuw inschakelt.

**Lid van groep niet ingericht**

Wanneer een groep zich in een bereik bevindt en een lid buiten het bereik valt, wordt de groep ingericht. De buitenste bereik gebruiker wordt niet ingericht. Als het lid weer binnen het bereik komt, detecteert de service de wijziging niet onmiddellijk. Bij het opnieuw starten van de inrichting wordt het probleem opgelost. We raden u aan om de service periodiek opnieuw te starten om ervoor te zorgen dat alle gebruikers op de juiste wijze worden ingericht.  

**Manager is niet ingericht**

Als een gebruiker en hun manager zich in het bereik van het inrichten bevinden, wordt de gebruiker door de service ingericht en wordt de Manager bijgewerkt. Als de gebruiker echter op één dag binnen het bereik van de scope ligt en de Manager buiten het bereik valt, zullen we de gebruiker inrichten zonder de Manager-referentie. Wanneer de Manager binnen het bereik komt, wordt de verwijzing naar de manager pas bijgewerkt als u de inrichting opnieuw start en de service opnieuw alle gebruikers opnieuw moet evalueren. 

## <a name="next-steps"></a>Volgende stappen
- [Hoe inrichting werkt](how-provisioning-works.md)

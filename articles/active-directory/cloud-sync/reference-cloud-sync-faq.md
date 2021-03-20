---
title: Veelgestelde vragen over Cloud synchronisatie Azure AD Connect
description: In dit document worden veelgestelde vragen over Cloud synchronisatie beschreven.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39d1554fd1b6cac1a90a794cfd93def97e494bfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613238"
---
# <a name="azure-active-directory-connect-cloud-sync-faq"></a>Veelgestelde vragen over Cloud synchronisatie Azure Active Directory Connect

Meer informatie over veelgestelde vragen over Azure Active Directory (Azure AD) Connect Cloud Sync.

## <a name="general-installation"></a>Algemene installatie

**V: hoe vaak wordt de Cloud synchronisatie uitgevoerd?**

De inrichting van de Cloud is gepland om elke 2 minuten te worden uitgevoerd. Elke 2 minuten, alle gebruikers-, groep-en wachtwoord hash-wijzigingen worden ingericht in azure AD.

**V: bekijken van mislukte wachtwoord-hash-synchronisaties bij de eerste uitvoering. Waarom?**

Dit is normaal. De fouten worden veroorzaakt doordat het gebruikers object niet aanwezig is in azure AD. Zodra de gebruiker is ingericht voor Azure AD, moeten wacht woord-hashes in de volgende uitvoering worden ingericht. Wacht op een aantal uitvoeringen en controleer of de wachtwoord-hash-synchronisatie geen fouten meer bevat.

**V: wat gebeurt er als het Active Directory-exemplaar kenmerken heeft die niet worden ondersteund door Cloud synchronisatie (bijvoorbeeld Directory-extensies)?**

De inrichting van de Cloud wordt uitgevoerd en de ondersteunde kenmerken worden ingericht. De niet-ondersteunde kenmerken worden niet ingericht in azure AD. Controleer de Directory-extensies in Active Directory en zorg ervoor dat u deze kenmerken niet nodig hebt om naar Azure AD te stromen. Als er een of meer kenmerken zijn vereist, kunt u overwegen Azure AD Connect synchronisatie te gebruiken of de vereiste gegevens te verplaatsen naar een van de ondersteunde kenmerken (bijvoorbeeld extensie kenmerken 1-15).

**V: wat is het verschil tussen Azure AD Connect synchronisatie en Cloud synchronisatie?**

Met Azure AD Connect Sync wordt het inrichten uitgevoerd op de on-premises synchronisatie server. De configuratie wordt opgeslagen op de on-premises synchronisatie server. Met Azure AD Connect Cloud synchronisatie wordt de inrichtings configuratie opgeslagen in de Cloud en uitgevoerd in de Cloud als onderdeel van de Azure AD-inrichtings service. 

**V: kan ik de Cloud synchronisatie gebruiken om te synchroniseren vanuit meerdere Active Directory forests?**

Ja. Cloud inrichting kan worden gebruikt om te synchroniseren vanuit meerdere Active Directory forests. In de omgeving met meerdere forests moeten alle verwijzingen (voor beeld, Manager) binnen het domein zijn.  

**V: hoe wordt de agent bijgewerkt?**

De agents worden automatisch bijgewerkt door micro soft. Voor het IT-team vermindert dit de belasting van het testen en valideren van nieuwe agent versies. 

**V: kan ik de automatische upgrade uitschakelen?**

Er wordt geen ondersteunde manier geboden om automatische upgrades uit te scha kelen.

**V: kan ik het bron anker voor Cloud synchronisatie wijzigen?**

Cloud Sync maakt standaard gebruik van MS-DS-consistentie-GUID met een terugval op ObjectGUID als bron anker. Er is geen ondersteunde manier om het bron anker te wijzigen.

**V: Ik zie nieuwe service-principals met de AD-domein naam (en) wanneer Cloud synchronisatie wordt gebruikt. Wordt deze verwacht?**

Ja, de Cloud synchronisatie maakt een service-principal voor de inrichtings configuratie met de domein naam als Service Principal Name. Breng geen wijzigingen aan in de configuratie van de Service-Principal.

**V: wat gebeurt er wanneer een gesynchroniseerde gebruiker het wacht woord moet wijzigen bij de volgende aanmelding?**

Als wacht woord-hash-synchronisatie is ingeschakeld in Cloud synchronisatie en de gesynchroniseerde gebruiker moet het wacht woord wijzigen bij de volgende aanmelding bij een on-premises AD, wordt in de Cloud synchronisatie de wacht woord-hash van ' to-changed ' niet ingericht voor Azure AD. Zodra de gebruiker het wacht woord heeft gewijzigd, wordt de hash van het gebruikers wachtwoord van AD naar Azure AD ingericht.

**V: biedt Cloud synchronisatie ondersteuning voor het terugschrijven van MS-DS-consistencyGUID voor elk object?**

Nee, Cloud synchronisatie biedt geen ondersteuning voor het terugschrijven van MS-DS-consistencyGUID voor een object (inclusief gebruikers objecten). 

**V: ik richt gebruikers in met behulp van Cloud synchronisatie. Ik heb de configuratie verwijderd. Waarom zie ik nog steeds de oude gesynchroniseerde objecten in azure AD?** 

Wanneer u de configuratie verwijdert, worden de gesynchroniseerde objecten in azure AD niet automatisch door de synchronisatie van de Cloud verwijderd. Om ervoor te zorgen dat u niet over de oude objecten beschikt, wijzigt u het bereik van de configuratie in een lege groep of organisatie-eenheden. Wanneer de inrichting wordt uitgevoerd en de objecten worden opgeschoond, kunt u de configuratie uitschakelen en verwijderen. 

**V: wat betekent het dat Exchange hybride niet wordt ondersteund?**

Met de functie voor hybride implementatie van Exchange kunnen Exchange-post vakken zowel on-premises als in Microsoft 365 naast elkaar bestaan. Azure AD Connect synchroniseert een specifieke set kenmerken vanuit Azure AD naar uw on-premises directory.  De Cloud Provisioning agent synchroniseert deze kenmerken momenteel niet terug in uw on-premises Directory en wordt daarom niet ondersteund als vervanging voor Azure AD Connect.

**V: kan ik de inrichtings agent voor de Cloud installeren op Windows Server Core?**

Nee, het installeren van de agent op Server Core wordt niet ondersteund.

**V: kan ik een staging-server met de Cloud inrichtings agent gebruiken?**

Nee, staging-servers worden niet ondersteund.

**V: kan ik gast gebruikers accounts synchroniseren?**

Nee, het synchroniseren van gast gebruikers accounts wordt niet ondersteund.

**V: als ik een gebruiker Verplaats van een organisatie-eenheid die is toegewezen voor Cloud synchronisatie naar een organisatie-eenheid met een bereik voor Azure AD Connect, wat gebeurt er dan?**

De gebruiker wordt verwijderd en opnieuw gemaakt.  Het verplaatsen van een gebruiker van een organisatie-eenheid die is gericht op Cloud synchronisatie, wordt weer gegeven als een verwijderings bewerking.  Als de gebruiker wordt verplaatst naar een organisatie-eenheid die wordt beheerd door Azure AD Connect, wordt deze opnieuw ingericht naar Azure AD en een nieuwe gebruiker gemaakt.

**V: als ik de organisatie-eenheid die binnen het bereik van het Cloud synchronisatie filter valt, een andere naam geven of verplaatsen, wat gebeurt er dan met de gebruiker die is gemaakt in azure AD?**

Niets.  De gebruikers worden niet verwijderd als de naam van de organisatie-eenheid wordt gewijzigd of als deze wordt verplaatst.

**V: biedt Azure AD Connect Cloud synchronisatie ondersteuning voor grote groepen?**

Ja. Vandaag ondersteunen we tot 50.000 groeps leden die zijn gesynchroniseerd met het filter voor OE-bereik. Wanneer u het filteren van groeps bereik gebruikt, raden we u aan de groeps grootte te beperken tot meer dan 1500 leden. De reden hiervoor is dat hoewel u een grote groep kunt synchroniseren als onderdeel van het filter bereik van de groep, wanneer u leden aan deze groep toevoegt door batches van meer dan 1500, de Delta synchronisatie mislukt. 

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)

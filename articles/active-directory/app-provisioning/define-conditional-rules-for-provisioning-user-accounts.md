---
title: Apps inrichten met bereik filters | Microsoft Docs
description: Meer informatie over het gebruik van bereik filters om te voor komen dat objecten in apps die ondersteuning bieden voor automatische gebruikers inrichting, worden ingericht als een object niet voldoet aan uw zakelijke vereisten.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
ms.openlocfilehash: 7fff456b7ad6e980fc3c9bda36cfcab02e2ed863
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255828"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Toewijzing van toepassingen op basis van kenmerken met bereik filters
In dit artikel wordt uitgelegd hoe u bereik filters gebruikt voor het definiëren van op kenmerken gebaseerde regels die bepalen welke gebruikers worden ingericht voor een toepassing.

## <a name="scoping-filter-use-cases"></a>Filter-use cases in bereik

Met een bereik filter kan de inrichtings service van Azure Active Directory (Azure AD) alle gebruikers opnemen of uitsluiten die een kenmerk hebben dat overeenkomt met een specifieke waarde. Als u bijvoorbeeld gebruikers van Azure AD inricht in een SaaS-toepassing die door een verkoop team wordt gebruikt, kunt u opgeven dat alleen gebruikers met het kenmerk ' afdeling ' van ' verkoop ' binnen bereik moeten zijn voor inrichting.

Bereik filters kunnen anders worden gebruikt, afhankelijk van het type inrichtings connector:

* **Uitgaande inrichting van Azure AD naar SaaS-toepassingen**. Wanneer Azure AD het bron systeem is, zijn [gebruikers-en groeps toewijzingen](../manage-apps/assign-user-or-group-access-portal.md) de meest voorkomende methode om te bepalen welke gebruikers binnen het bereik van het inrichten vallen. Deze toewijzingen worden ook gebruikt om eenmalige aanmelding in te scha kelen en één methode te bieden voor het beheren van toegang en inrichting. U kunt ook de bereik filters gebruiken, naast toewijzingen of in plaats daarvan, om gebruikers te filteren op basis van kenmerk waarden.

    >[!TIP]
    > U kunt het inrichten uitschakelen op basis van toewijzingen voor een bedrijfs toepassing door de instellingen in het menu [bereik](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) onder de inrichtings instellingen te wijzigen om **alle gebruikers en groepen te synchroniseren**. 

* **Inkomende Provisioning van HCM-toepassingen naar Azure AD en Active Directory**. Wanneer een [HCM-toepassing, zoals workday](../saas-apps/workday-tutorial.md) , het bron systeem is, zijn bereik filters de primaire methode om te bepalen welke gebruikers moeten worden ingericht van de HCM-toepassing naar Active Directory of Azure AD.

Standaard hebben Azure AD-inrichtings connectors geen bereik filters geconfigureerd op basis van kenmerken. 

## <a name="scoping-filter-construction"></a>Bereik van filter constructie

Een bereik filter bestaat uit een of meer *componenten*. -Componenten bepalen welke gebruikers de bereik filter mogen door geven door de kenmerken van elke gebruiker te evalueren. U kunt bijvoorbeeld een-component hebben waarvoor het kenmerk ' state ' van een gebruiker is ingesteld op ' New York ', zodat alleen New York-gebruikers in de toepassing worden ingericht. 

Een enkele component definieert één voor waarde voor één kenmerk waarde. Als er meerdere componenten worden gemaakt in één bereik filter, worden ze samen met de logica geëvalueerd. Dit betekent dat alle-componenten moeten worden geëvalueerd als ' True ' zodat een gebruiker kan worden ingericht.

Ten slotte kunnen meerdere bereik filters worden gemaakt voor één toepassing. Als er meerdere bereik filters aanwezig zijn, worden ze samen met de logica geëvalueerd. Dit betekent dat als alle componenten in een van de geconfigureerde bereik filters op ' True ' worden geëvalueerd, de gebruiker is ingericht.

Elke gebruiker of groep die door de Azure AD Provisioning-Service is verwerkt, wordt altijd afzonderlijk geëvalueerd op basis van elk filter bereik.

Bekijk bijvoorbeeld het volgende bereik filter:

![Bereik filter](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

Op basis van dit bereik filter moeten gebruikers voldoen aan de volgende criteria die moeten worden ingericht:

* Ze moeten zich in New York bevinden.
* Ze moeten werken in de technische afdeling.
* De werk nemer-ID van het bedrijf moet tussen 1.000.000 en 2.000.000.
* De functie mag niet null of leeg zijn.

## <a name="create-scoping-filters"></a>Bereik filters maken
Bereik filters worden geconfigureerd als onderdeel van de kenmerk toewijzingen voor elke Azure AD-gebruikers inrichtings connector. In de volgende procedure wordt ervan uitgegaan dat u al automatisch inrichten hebt ingesteld voor [een van de ondersteunde toepassingen](../saas-apps/tutorial-list.md) en hoe u er een filter voor een bereik aan toevoegt.

### <a name="create-a-scoping-filter"></a>Een bereik filter maken
1. Ga in het [Azure Portal](https://portal.azure.com)naar de sectie **Azure Active Directory**  >  **Enter prise Applications**  >  **all applications** '.

2. Selecteer de toepassing waarvoor u automatische inrichting hebt geconfigureerd: bijvoorbeeld ' ServiceNow '.

3. Selecteer het tabblad **Inrichten**.

4. Selecteer in de sectie **toewijzingen** de toewijzing waarvoor u een bereik filter wilt configureren: bijvoorbeeld ' Synchroniseer Azure Active Directory gebruikers naar ServiceNow '.

5. Selecteer het bereik menu van het **bron object** .

6. Selecteer **filter bereik toevoegen**.

7. Definieer een-component door een bron **kenmerk naam**, een **operator** en een **kenmerk waarde** op te geven die moet overeenkomen. De volgende Opera tors worden ondersteund:

   a. **Is gelijk aan**. -Component retourneert ' True ' als het geëvalueerde kenmerk precies overeenkomt met de waarde van de invoer teken reeks (hoofdletter gevoelig).

   b. **niet gelijk aan**. -Component retourneert ' True ' als het geëvalueerde kenmerk niet overeenkomt met de invoer teken reeks waarde (hoofdletter gevoelig).

   c. **is waar**. -Component retourneert ' True ' als het geëvalueerde kenmerk een Booleaanse waarde True bevat.

   d. **is onwaar**. -Component retourneert ' True ' als het geëvalueerde kenmerk een Booleaanse waarde heeft van ONWAAR.

   e. **is null**. -Component retourneert ' True ' als het geëvalueerde kenmerk leeg is.

   f. **is niet null**. -Component retourneert ' True ' als het geëvalueerde kenmerk niet leeg is.

   g. **regex match**. -Component retourneert ' True ' als het geëvalueerde kenmerk overeenkomt met een reguliere-expressie patroon. Bijvoorbeeld: ([1-9] [0-9]) komt overeen met een getal tussen 10 en 99.

   h. **geen regex-overeenkomst**. -Component retourneert ' True ' als het geëvalueerde kenmerk niet overeenkomt met een reguliere-expressie patroon.
   
   i. **Greater_Than.** -Component retourneert ' True ' als het geëvalueerde kenmerk groter is dan de waarde. De waarde die is opgegeven voor het filter bereik moet een geheel getal zijn en het kenmerk van de gebruiker moet een geheel getal zijn [0, 1, 2,...]. 
   
   j. **Greater_Than_OR_EQUALS.** -Component retourneert ' True ' als het geëvalueerde kenmerk groter is dan of gelijk is aan de waarde. De waarde die is opgegeven voor het filter bereik moet een geheel getal zijn en het kenmerk van de gebruiker moet een geheel getal zijn [0, 1, 2,...]. 
   
   k. **Neem.** -Component retourneert ' True ' als het geëvalueerde kenmerk de teken reeks waarde (hoofdletter gevoelig) bevat, zoals [hier](/dotnet/api/system.string.contains)wordt beschreven. 


>[!IMPORTANT] 
> - Het IsMemberOf-filter wordt momenteel niet ondersteund.
> - Is gelijk aan en niet gelijk aan, wordt niet ondersteund voor kenmerken met meerdere waarden

9. Herhaal desgewenst stap 7-8 om meer scoping-componenten toe te voegen.

10. Voeg in **filter titel bereik** een naam toe voor het filter bereik.

11. Selecteer **OK**.

12. Selecteer **OK** in het scherm **filteren op bereik** . Herhaal de stappen 6-11 om nog een filter voor het bereik toe te voegen.

13. Selecteer **Opslaan** in het scherm **kenmerk toewijzing** . 

>[!IMPORTANT] 
> Als u een nieuw bereik filter opslaat, wordt een nieuwe volledige synchronisatie voor de toepassing geactiveerd, waarbij alle gebruikers in het bron systeem opnieuw worden geëvalueerd op basis van het nieuwe bereik filter. Als een gebruiker in de toepassing eerder is ingericht voor het inrichten, maar buiten het bereik valt, wordt het account uitgeschakeld of in de toepassing onbeschikbaar gemaakt. Als u dit standaard gedrag wilt overschrijven, raadpleegt u [verwijdering overs laan voor gebruikers accounts die buiten het bereik vallen](../app-provisioning/skip-out-of-scope-deletions.md).


## <a name="common-scoping-filters"></a>Algemene bereik filters
| Doel kenmerk| Operator | Waarde | Beschrijving|
|----|----|----|----|
|userPrincipalName|OVEREENKOMENDE REGEX|.\*@domain.com |Alle gebruikers met userPrincipal die het domein hebben @domain.com , zijn in het bereik voor inrichting.|
|userPrincipalName|GEEN REGEX-OVEREENKOMST|.\*@domain.com|Alle gebruikers met userPrincipal die het domein hebben @domain.com , zijn buiten het bereik voor het inrichten|
|department|GELIJK is aan|verkoop|Alle gebruikers van de verkoop afdeling vallen binnen het bereik van de inrichting|
|workerID|OVEREENKOMENDE REGEX|(1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])| Alle werk nemers met workerIDs tussen 1000000 en 2000000 zijn binnen het bereik van de inrichting.|

## <a name="related-articles"></a>Verwante artikelen:
* [Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-toepassingen automatiseren](../app-provisioning/user-provisioning.md)
* [Kenmerktoewijzingen aanpassen voor het inrichten van gebruikers](../app-provisioning/customize-application-attributes.md)
* [Expressies schrijven voor kenmerktoewijzingen](functions-for-customizing-application-data.md)
* [Meldingen over accountinrichting](../app-provisioning/user-provisioning.md)
* [Gebruik SCIM om automatische inrichting van gebruikers en groepen in te scha kelen van Azure Active Directory naar toepassingen](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Lijst met handleidingen voor het integreren van SaaS-apps](../saas-apps/tutorial-list.md)

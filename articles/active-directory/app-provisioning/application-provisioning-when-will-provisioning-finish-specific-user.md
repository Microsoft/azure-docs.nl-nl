---
title: Ontdekken wanneer een specifieke gebruiker toegang heeft tot een app
description: Ontdekken wanneer een zeer belangrijke gebruiker toegang kan krijgen tot een toepassing die u hebt geconfigureerd voor het inrichten van gebruikers met Azure AD
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9835ba2b6db2d71d0ff5825f2eb1996133e75537
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530826"
---
# <a name="check-the-status-of-user-provisioning"></a>De status van het inrichten van gebruikers controleren

De Azure AD-inrichtingsservice voert een eerste inrichtingscyclus uit op basis van het bronsysteem en doelsysteem, gevolgd door periodieke incrementele cycli. Wanneer u inrichting voor een app configureert, kunt u de huidige status van de inrichtingsservice controleren en zien wanneer een gebruiker toegang heeft tot een app.

## <a name="view-the-provisioning-progress-bar"></a>De voortgangsbalk van de inrichting weergeven

 Op de **pagina Inrichting** voor een app kunt u de status van de Azure AD-inrichtingsservice bekijken. In **de sectie Huidige** status onder aan de pagina ziet u of het inrichten van gebruikersaccounts is gestart met een inrichtingscyclus. U kunt de voortgang van de cyclus bekijken, zien hoeveel gebruikers en groepen zijn ingericht en zien hoeveel rollen er zijn gemaakt.

Wanneer u automatische inrichting voor het eerst configureert, wordt in de sectie Huidige **status** onder aan de pagina de status van de eerste inrichtingscyclus weergegeven. Deze sectie wordt telkens bijgewerkt als een incrementele cyclus wordt uitgevoerd. De volgende details worden weergegeven:
- Het type inrichtingscyclus (eerste of incrementele) dat momenteel wordt uitgevoerd of voor het laatst is voltooid.
- Een **voortgangsbalk** met het percentage van de inrichtingscyclus dat is voltooid. Het percentage weerspiegelt het aantal pagina's dat is ingericht. Houd er rekening mee dat elke pagina meerdere gebruikers of groepen kan bevatten, zodat het percentage niet rechtstreeks overeenkomt met het aantal gebruikers, groepen of rollen dat is ingericht.
- Een **knop** Vernieuwen die u kunt gebruiken om de weergave bijgewerkt te houden.
- Het aantal gebruikers **en** groepen in **het** connectorgegevensopslag. Het aantal neemt toe wanneer een object wordt toegevoegd aan het bereik van de inrichting. Het aantal wordt niet omlaag getrokken als een gebruiker soft-deleted of hard-deleted is, omdat hiermee het object niet uit het connectorgegevensopslag wordt verwijderd. Het aantal wordt opnieuw berekend bij de eerste synchronisatie nadat het CDS opnieuw is [ingesteld](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) 
- Een **koppeling Auditlogboeken** weergeven, waarmee de Azure AD-inrichtingslogboeken worden geopend voor meer informatie over alle [](#use-provisioning-logs-to-check-a-users-provisioning-status) bewerkingen die worden uitgevoerd door de service voor het inrichten van gebruikers, inclusief de inrichtingsstatus voor afzonderlijke gebruikers (zie de sectie Inrichtingslogboeken gebruiken hieronder).

Nadat een inrichtingscyclus is  voltooid, toont de sectie Statistieken tot heden de cumulatieve aantallen gebruikers en groepen die tot heden zijn ingericht, samen met de voltooiingsdatum en duur van de laatste cyclus. De **activiteits-id** is een unieke identificatie van de meest recente inrichtingscyclus. De **taak-id** is een unieke id voor de inrichtings job en is specifiek voor de app in uw tenant.

De voortgang van de inrichting kan worden weergegeven in de Azure Portal, op het tabblad Azure Active Directory enterprise apps application name Provisioning ( **&gt; &gt; \[ \] &gt; Inrichting).**

![Voortgangsbalk van de inrichtingspagina](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Inrichtingslogboeken gebruiken om de inrichtingsstatus van een gebruiker te controleren

Als u de inrichtingsstatus voor een geselecteerde gebruiker wilt bekijken, raadpleegt u de inrichtingslogboeken [(preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in Azure AD. Alle bewerkingen die worden uitgevoerd door de service voor het inrichten van gebruikers, worden vastgelegd in de inrichtingslogboeken van Azure AD. Dit omvat alle lees- en schrijfbewerkingen die worden uitgevoerd naar de bron- en doelsystemen, en de gebruikersgegevens die tijdens elke bewerking zijn gelezen of geschreven.

U kunt de inrichtingslogboeken in de  Azure Portal door Azure Active Directory &gt; **Enterprise Apps** &gt; **Provisioning Logs (preview)** te selecteren in de **sectie** Activiteit. U kunt de inrichtingsgegevens doorzoeken op basis van de naam van de gebruiker of de id in het bronsysteem of het doelsysteem. Zie Inrichtingslogboeken [(preview) voor meer informatie.](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 

De inrichtingslogboeken registreren alle bewerkingen die door de inrichtingsservice worden uitgevoerd, waaronder:

* Query's uitvoeren in Azure AD voor toegewezen gebruikers die binnen het bereik van inrichting vallen
* Query's uitvoeren op de doel-app voor het bestaan van deze gebruikers
* Vergelijking van de gebruikersobjecten tussen het systeem
* Toevoegen, bijwerken of uitschakelen van het gebruikersaccount in het doelsysteem op basis van de vergelijking

Zie de rapportagehandleiding voor inrichting voor meer informatie over [](check-status-user-account-provisioning.md)het lezen van de inrichtingslogboeken in de Azure Portal.

## <a name="how-long-will-it-take-to-provision-users"></a>Hoe lang duurt het om gebruikers in terichten?
Wanneer u automatische inrichting van gebruikers met een toepassing gebruikt, worden gebruikersaccounts [](../manage-apps/assign-user-or-group-access-portal.md) in Azure AD automatisch ingericht en bijgewerkt in een app op basis van zaken zoals gebruikers- en groepstoewijzingen op een regelmatig gepland tijdsinterval, meestal om de 40 minuten.

De tijd die het kost om een bepaalde gebruiker in te richten, is voornamelijk afhankelijk van of uw inrichtings job een initiële cyclus of een incrementele cyclus wordt uitgevoerd.

- Voor **de eerste cyclus** is de taaktijd afhankelijk van veel factoren, waaronder het aantal gebruikers en groepen binnen het bereik voor inrichting en het totale aantal gebruikers en groepen in het bronsysteem. De eerste synchronisatie tussen Azure AD en een app kan tussen 20 minuten en enkele uren duren, afhankelijk van de grootte van de Azure AD-directory en het aantal gebruikers binnen het bereik voor inrichting. Een uitgebreide lijst met factoren die van invloed zijn op de prestaties van de initiële cyclus worden verder in deze sectie samengevat.

- Voor **incrementele cycli** na de initiële cyclus zijn de taaktijden doorgaans sneller (bijvoorbeeld binnen 10 minuten), omdat de inrichtingsservice watermerken op slaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van volgende synchronisaties worden verbeterd. De taaktijd is afhankelijk van het aantal wijzigingen dat in die inrichtingscyclus is gedetecteerd. Als er minder dan 5000 wijzigingen in het gebruikers- of groepslidmaatschap zijn, kan de taak binnen één incrementele inrichtingscyclus worden beëindigd. 

De volgende tabel bevat een overzicht van synchronisatietijden voor algemene inrichtingsscenario's. In deze scenario's is het bronsysteem Azure AD en is het doelsysteem een SaaS-toepassing. De synchronisatietijden worden afgeleid van een statistische analyse van synchronisatietaken voor de SaaS-toepassingen ServiceNow, Workplace, Salesforce en G Suite.


| Bereikconfiguratie | Gebruikers, groepen en leden binnen het bereik | Initiële cyclustijd | Incrementele cyclustijd |
| -------- | -------- | -------- | -------- |
| Alleen toegewezen gebruikers en groepen synchroniseren |  < 1000 |  < 30 minuten | < 30 minuten |
| Alleen toegewezen gebruikers en groepen synchroniseren |  1,000 - 10,000 | 142 - 708 minuten | < 30 minuten |
| Alleen toegewezen gebruikers en groepen synchroniseren |   10,000 - 100,000 | 1170 - 2340 minuten | < 30 minuten |
| Alle gebruikers en groepen synchroniseren in Azure AD |  < 1000 | < 30 minuten  | < 30 minuten |
| Alle gebruikers en groepen synchroniseren in Azure AD |  1,000 - 10,000 | < 30 - 120 minuten | < 30 minuten |
| Alle gebruikers en groepen synchroniseren in Azure AD |  10,000 - 100,000  | 713 - 1425 minuten | < 30 minuten |
| Alle gebruikers synchroniseren in Azure AD|  < 1000  | < 30 minuten | < 30 minuten |
| Alle gebruikers synchroniseren in Azure AD | 1,000 - 10,000  | 43 - 86 minuten | < 30 minuten |

Voor de configuratie **die alleen door Sync** aan gebruikers en groepen is toegewezen, kunt u de volgende formules gebruiken om de minimale en maximale verwachte begincyclustijden **te** bepalen:

- Minimum aantal minuten = 0,01 x [aantal toegewezen gebruikers, groepen en groepsleden]
- Maximum aantal minuten = 0,08 x [aantal toegewezen gebruikers, groepen en groepsleden]

Samenvatting van factoren die van invloed zijn op de tijd die nodig is om een initiële cyclus **te voltooien:**

- Het totale aantal gebruikers en groepen binnen het bereik voor inrichting.

- Het totale aantal gebruikers, groepen en groepsleden dat aanwezig is in het bronsysteem (Azure AD).

- Of gebruikers binnen het bereik voor inrichting overeenkomen met bestaande gebruikers in de doeltoepassing of voor de eerste keer moeten worden gemaakt. Synchronisatietaken waarvoor alle gebruikers voor de  eerste keer worden gemaakt, duren ongeveer twee keer zo lang als synchronisatietaken waarvoor alle gebruikers overeenkomen met bestaande gebruikers.

- Aantal fouten in de [inrichtingslogboeken](check-status-user-account-provisioning.md). De prestaties zijn trager als er veel fouten zijn en de inrichtingsservice in quarantaine is geplaatst. 

- Limieten voor aanvraagsnelheid en beperking die door het doelsysteem zijn geïmplementeerd. Sommige doelsystemen implementeren aanvraagfrequentielimieten en -beperking, wat van invloed kan zijn op de prestaties tijdens grote synchronisatiebewerkingen. Onder deze omstandigheden kan een app die te veel aanvragen te snel ontvangt, de reactiesnelheid vertragen of de verbinding sluiten. Om de prestaties te verbeteren, moet de connector zich aanpassen door de app-aanvragen niet sneller te verzenden dan de app kan verwerken. Het inrichten van connectors die door Microsoft zijn gebouwd, maken deze aanpassing. 

- Het aantal en de grootte van toegewezen groepen. Het synchroniseren van toegewezen groepen duurt langer dan het synchroniseren van gebruikers. Zowel het aantal als de grootte van de toegewezen groepen zijn van invloed op de prestaties. Als voor een toepassing [toewijzingen](customize-application-attributes.md#editing-group-attribute-mappings)zijn ingeschakeld voor groepsobjectsynchronisatie, worden naast gebruikers ook groepseigenschappen, zoals groepsnamen en lidmaatschappen, gesynchroniseerd. Deze extra synchronisaties duren langer dan alleen het synchroniseren van gebruikersobjecten.

- Als de prestaties een probleem worden en u de meeste gebruikers en groepen in uw tenant wilt inrichten, gebruikt u bereikfilters. Met bereikfilters kunt u de gegevens die de inrichtingsservice uit Azure AD extraheert, afstemmen door gebruikers te filteren op basis van specifieke kenmerkwaarden. Zie Op kenmerken gebaseerde toepassing inrichten met bereikfilters voor meer informatie over [bereikfilters.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

## <a name="next-steps"></a>Volgende stappen
[Automate User Provisioning and Deprovisioning to SaaS Applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)
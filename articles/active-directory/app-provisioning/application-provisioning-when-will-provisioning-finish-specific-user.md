---
title: Nagaan wanneer een specifieke gebruiker toegang kan krijgen tot een app
description: Nagaan of een belang rijke gebruiker toegang heeft tot een toepassing die u hebt geconfigureerd voor het inrichten van gebruikers met Azure AD
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
ms.openlocfilehash: 890c4a8a5aec3b15b150908c64bb114bd85a61a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256759"
---
# <a name="check-the-status-of-user-provisioning"></a>De status van het inrichten van gebruikers controleren

De Azure AD-inrichtings service voert een eerste inrichtings cyclus uit op het bron systeem en het doel systeem, gevolgd door periodieke incrementele cycli. Wanneer u inrichting voor een app configureert, kunt u de huidige status van de inrichtings service controleren en zien wanneer een gebruiker toegang heeft tot een app.

## <a name="view-the-provisioning-progress-bar"></a>De voortgangs balk van de inrichting weer geven

 Op de **inrichtings** pagina voor een app kunt u de status van de Azure AD-inrichtings service weer geven. In het gedeelte **huidige status** onder aan de pagina ziet u of een inrichtings cyclus is gestart met het inrichten van gebruikers accounts. U kunt de voortgang van de cyclus bekijken, bekijken hoeveel gebruikers en groepen zijn ingericht en bekijken hoeveel rollen er zijn gemaakt.

Wanneer u voor het eerst automatische inrichting configureert, ziet u in de sectie **huidige status** onder aan de pagina de status van de eerste inrichtings cyclus. Deze sectie wordt telkens bijgewerkt wanneer een incrementele cyclus wordt uitgevoerd. De volgende details worden weer gegeven:
- Het type inrichtings cyclus (eerste of incrementeel) dat momenteel wordt uitgevoerd of de laatste keer is voltooid.
- Een **voortgangs balk** met het percentage van de inrichtings cyclus dat is voltooid. Het percentage weerspiegelt het aantal pagina's dat is ingericht. Houd er rekening mee dat elke pagina meerdere gebruikers of groepen kan bevatten, zodat het percentage niet rechtstreeks overeenkomt met het aantal gebruikers, groepen of rollen dat is ingericht.
- Een knop voor **vernieuwen** die u kunt gebruiken om de weer gave bijgewerkt te laten worden.
- Het aantal **gebruikers** en **groepen** in het gegevens archief van de connector. De telling neemt toe wanneer een object wordt toegevoegd aan het bereik van de inrichting. Het aantal wordt niet weer gegeven als een gebruiker zacht wordt verwijderd of permanent wordt verwijderd omdat hiermee het object niet uit de gegevens opslag van de connector wordt verwijderd. De telling wordt opnieuw berekend op de eerste synchronisatie nadat de CD'S [opnieuw zijn ingesteld](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta) 
- Een koppeling **voor controle logboeken weer geven** waarmee de Azure AD-inrichtings logboeken worden geopend voor meer informatie over alle bewerkingen die worden uitgevoerd door de gebruikers Provisioning Service, inclusief de inrichtings status voor afzonderlijke gebruikers (Zie de sectie [inrichtings Logboeken gebruiken](#use-provisioning-logs-to-check-a-users-provisioning-status) ).

Nadat een inrichtings cyclus is voltooid, toont de sectie **statistieken tot datum** de cumulatieve aantallen gebruikers en groepen die zijn ingericht tot datum, samen met de voltooiings datum en duur van de laatste cyclus. Met de **activiteit-ID** wordt de meest recente inrichtings cyclus uniek geïdentificeerd. De **taak-id** is een unieke id voor de inrichtings taak en is specifiek voor de app in uw Tenant.

De inrichtings voortgang kan worden weer gegeven in de Azure Portal op het tabblad **Azure Active Directory &gt; &gt; \[ toepassings \] &gt; naam** van de toepassing.

![Voortgangs balk van de inrichtings pagina](./media/application-provisioning-when-will-provisioning-finish-specific-user/provisioning-progress-bar-section.png)

## <a name="use-provisioning-logs-to-check-a-users-provisioning-status"></a>Inrichtings Logboeken gebruiken om de inrichtings status van een gebruiker te controleren

Als u de inrichtings status voor een geselecteerde gebruiker wilt weer geven, raadpleegt u de [inrichtings Logboeken (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in azure AD. Alle bewerkingen die worden uitgevoerd door de User Provisioning Service worden vastgelegd in de Azure AD-inrichtings Logboeken. Dit omvat alle Lees-en schrijf bewerkingen die zijn aangebracht op de bron-en doel systemen en de gebruikers gegevens die tijdens elke bewerking zijn gelezen of geschreven.

U kunt toegang krijgen tot de inrichtings Logboeken in de Azure portal door **Azure Active Directory** &gt; **Enter prise apps** &gt; **Provisioning logs (preview)** te selecteren in de sectie **activiteit** . U kunt de inrichtings gegevens zoeken op basis van de naam van de gebruiker of de id in het bron systeem of het doel systeem. Zie [inrichtings Logboeken (preview-versie)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)voor meer informatie. 

De inrichtings logboeken registreren alle bewerkingen die worden uitgevoerd door de inrichtings service, waaronder:

* Query's uitvoeren voor Azure AD voor toegewezen gebruikers die binnen het bereik van de inrichting vallen
* Query's uitvoeren op de doel-app voor het bestaan van deze gebruikers
* De gebruikers objecten tussen het systeem vergelijken
* Toevoegen, bijwerken of uitschakelen van het gebruikers account in het doel systeem op basis van de vergelijking

Voor meer informatie over het lezen van de inrichtings Logboeken in de Azure Portal raadpleegt u de [hand leiding](check-status-user-account-provisioning.md)voor het inrichten van de rapportage.

## <a name="how-long-will-it-take-to-provision-users"></a>Hoe lang duurt het om gebruikers in te richten?
Wanneer u automatische gebruikers inrichting met een toepassing gebruikt, worden gebruikers accounts in een app automatisch door Azure AD ingericht en bijgewerkt op basis van dingen zoals [gebruikers-en groeps toewijzing](../manage-apps/assign-user-or-group-access-portal.md) met een regel matig gepland tijds interval, meestal elke 40 minuten.

Hoe lang het duurt voordat een bepaalde gebruiker is ingericht, hangt voornamelijk af van de vraag of uw inrichtings taak een eerste cyclus of een incrementele cyclus uitvoert.

- Voor de **eerste cyclus** is de tijd van de taak afhankelijk van veel factoren, zoals het aantal gebruikers en groepen in het bereik voor inrichting en het totale aantal gebruikers en de groep in het bron systeem. De eerste synchronisatie tussen Azure AD en een app kan van 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-adres lijst en het aantal gebruikers in het bereik dat moet worden ingericht. Verderop in deze sectie vindt u een uitgebreide lijst met factoren die van invloed zijn op de prestaties van de initiële cyclus.

- Voor **incrementele cycli** na de eerste cyclus zijn taak tijden korter (bijvoorbeeld binnen tien minuten), omdat de inrichtings service water merken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van volgende synchronisaties worden verbeterd. De tijd van de taak is afhankelijk van het aantal wijzigingen dat is gedetecteerd in die inrichtings cyclus. Als er minder dan 5.000 gebruikers-of groepslid maatschappen worden gewijzigd, kan de taak binnen één incrementele inrichtings cyclus worden voltooid. 

De volgende tabel bevat een overzicht van de synchronisatie tijden voor veelvoorkomende inrichtings scenario's. In deze scenario's is het bron systeem Azure AD en is het doel systeem een SaaS-toepassing. De synchronisatie tijden zijn afgeleid van een statistische analyse van synchronisatie taken voor de SaaS-toepassingen ServiceNow, Workplace, Sales Force en G suite.


| Scope configuratie | Gebruikers, groepen en leden in bereik | Eerste cyclus tijd | Incrementele cyclus tijd |
| -------- | -------- | -------- | -------- |
| Alleen toegewezen gebruikers en groepen synchroniseren |  < 1.000 |  < 30 minuten | < 30 minuten |
| Alleen toegewezen gebruikers en groepen synchroniseren |  1.000-10.000 | 142-708 minuten | < 30 minuten |
| Alleen toegewezen gebruikers en groepen synchroniseren |   10.000-100.000 | 1.170-2.340 minuten | < 30 minuten |
| Alle gebruikers en groepen in azure AD synchroniseren |  < 1.000 | < 30 minuten  | < 30 minuten |
| Alle gebruikers en groepen in azure AD synchroniseren |  1.000-10.000 | < 30-120 minuten | < 30 minuten |
| Alle gebruikers en groepen in azure AD synchroniseren |  10.000-100.000  | 713-1.425 minuten | < 30 minuten |
| Alle gebruikers in azure AD synchroniseren|  < 1.000  | < 30 minuten | < 30 minuten |
| Alle gebruikers in azure AD synchroniseren | 1.000-10.000  | 43-86 minuten | < 30 minuten |

Voor de configuratie **synchronisatie alleen gebruiker en groepen toegewezen**, kunt u de volgende formules gebruiken om de geschatte minimale en maximale verwachte **initiële cyclus** tijden te bepalen:

- Minimale minuten = 0,01 x [aantal toegewezen gebruikers, groepen en groeps leden]
- Maxi maal minuten = 0,08 x [aantal toegewezen gebruikers, groepen en groeps leden]

Samen vatting van factoren die van invloed zijn op de tijd die nodig is om een **eerste cyclus** te volt ooien:

- Het totale aantal gebruikers en groepen in het bereik voor inrichting.

- Het totale aantal gebruikers, groepen en groeps leden dat aanwezig is in het bron systeem (Azure AD).

- Of gebruikers in het bereik voor het inrichten worden afgestemd op bestaande gebruikers in de doel toepassing of moeten worden gemaakt voor de eerste keer. Synchronisatie taken waarvoor alle gebruikers voor de eerste keer *twee keer zo lang* duren als synchronisatie taken waarvoor alle gebruikers overeenkomen met bestaande gebruikers.

- Aantal fouten in de [inrichtings logboeken](check-status-user-account-provisioning.md). De prestaties zijn langzamer als er veel fouten zijn en de inrichtings service een quarantaine status heeft. 

- Frequentie limieten en beperking van aanvragen die worden geïmplementeerd door het doel systeem. Sommige doel systemen implementeren limieten en beperking van aanvragen, wat de prestaties kan beïnvloeden tijdens grote synchronisatie bewerkingen. Onder deze omstandigheden kan een app die te snel te veel aanvragen ontvangt, de reactie snelheid vertragen of de verbinding sluiten. Om de prestaties te verbeteren, moet de connector worden aangepast door de app-aanvragen sneller te verzenden dan deze door de app kunnen worden verwerkt. Voor het inrichten van connectors die door micro soft zijn gebouwd, doet u deze aanpassing. 

- Het aantal en de grootte van toegewezen groepen. Het synchroniseren van toegewezen groepen duurt langer dan gebruikers synchroniseren. Zowel het aantal als de grootte van de toegewezen groepen zijn van invloed op de prestaties. Als een toepassing [toewijzingen voor groeps object synchronisatie heeft ingeschakeld](customize-application-attributes.md#editing-group-attribute-mappings), worden groeps eigenschappen zoals groeps namen en lidmaatschappen naast gebruikers gesynchroniseerd. Deze aanvullende synchronisaties nemen meer tijd in beslag dan alleen gebruikers objecten synchroniseren.

- Als de prestaties een probleem vormen en u probeert het meren deel van de gebruikers en groepen in uw Tenant in te richten, gebruikt u bereik filters. Met behulp van bereik filters kunt u de gegevens die door de inrichtings service worden geëxtraheerd, afstemmen op de gebruikers op basis van specifieke kenmerk waarden. Zie voor meer informatie over het bereik van filters [kenmerk toepassing inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="next-steps"></a>Volgende stappen
[Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory)
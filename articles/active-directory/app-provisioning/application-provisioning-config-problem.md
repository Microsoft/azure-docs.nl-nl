---
title: Probleem bij het configureren van de gebruikers inrichting voor een Azure AD Gallery-app
description: Algemene problemen oplossen die zich voordoen bij het configureren van het inrichten van gebruikers voor een toepassing die al wordt vermeld in de Azure AD-toepassings galerie
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 651bc199a43881add7faa00a9d1cf78000de6944
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256269"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Probleem bij het configureren van de gebruikers inrichting voor een Azure AD Gallery-toepassing

Voor het configureren van de [automatische gebruikers inrichting](user-provisioning.md) voor een app (indien ondersteund) moeten specifieke instructies worden gevolgd om de toepassing voor te bereiden voor automatische inrichting. Vervolgens kunt u de Azure Portal gebruiken om de inrichtings service te configureren om gebruikers accounts te synchroniseren met de toepassing.

U moet altijd eerst de installatie-zelf studie vinden die specifiek is voor het instellen van inrichting voor uw toepassing. Volg vervolgens de stappen voor het configureren van zowel de app als Azure AD om de inrichtings verbinding te maken. Een lijst met app-zelf studies vindt u in een [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="how-to-see-if-provisioning-is-working"></a>Controleren of inrichten werkt 

Zodra de service is geconfigureerd, kan de meest inzicht in de werking van de service vanaf twee locaties worden getrokken:

-   **Inrichtings Logboeken (preview)** : de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) registreren alle bewerkingen die worden uitgevoerd door de inrichtings service, inclusief het opvragen van Azure AD voor toegewezen gebruikers die binnen het bereik van de inrichting vallen. Zoek de doel-app op voor het bestaan van deze gebruikers, waarbij de gebruikers objecten tussen het systeem worden vergeleken. Vervolgens kunt u het gebruikers account in het doel systeem toevoegen, bijwerken of uitschakelen op basis van de vergelijking. U kunt toegang krijgen tot de inrichtings Logboeken in de Azure portal door **Azure Active Directory** &gt; **Enter prise apps** &gt; **Provisioning logs (preview)** te selecteren in de sectie **activiteit** .

-   **Huidige status:** Een samen vatting van de laatste uitvoering van de inrichting voor een bepaalde app kan worden weer gegeven in de sectie **Azure Active Directory &gt; bedrijfs apps &gt; \[ toepassings naam \] &gt; inrichten** onder aan het scherm onder de service-instellingen. De sectie huidige status geeft aan of een inrichtings cyclus is begonnen met het inrichten van gebruikers accounts. U kunt de voortgang van de cyclus bekijken, bekijken hoeveel gebruikers en groepen zijn ingericht en bekijken hoeveel rollen er zijn gemaakt. Als er fouten zijn, kunt u de details vinden in [inrichtings Logboeken (.. /Reports-monitoring/concept-Provisioning-logs.MD? context = Azure/Active-Directory/Manage-apps/context/Manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Algemene probleem gebieden met het inrichten om te overwegen

Hieronder vindt u een lijst met algemene probleem gebieden die u kunt inzoomen als u een idee hebt van waar u moet beginnen.

* [De inrichtings service lijkt niet te worden gestart](#provisioning-service-does-not-appear-to-start)
* Kan de configuratie niet opslaan omdat de app-referenties niet werken
* [Inrichtings logboeken: gebruikers worden overgeslagen en niet ingericht, ook al zijn ze toegewezen](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>De inrichtings service lijkt niet te worden gestart

Als u instelt dat de **inrichtings status** moet worden **ingeschakeld** in de sectie **Azure Active Directory de &gt; module bedrijfs Apps &gt; \[ toepassings naam \] &gt; inrichten** van de Azure Portal. Er worden echter geen andere status gegevens op die pagina weer gegeven nadat de volgende keer opnieuw is geladen. Het is waarschijnlijk dat de service wordt uitgevoerd, maar nog niet een eerste cyclus heeft voltooid. Controleer de hierboven beschreven **inrichtings logboeken** om te bepalen welke bewerkingen de service uitvoert en of er fouten zijn.

>[!NOTE]
>Een eerste cyclus kan 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-adres lijst en het aantal gebruikers binnen het bereik van de inrichting. Volgende synchronisaties na de eerste cyclus worden sneller uitgevoerd, omdat de inrichtings service de water merken opslaat die de status van beide systemen na de eerste cyclus vertegenwoordigen, waardoor de prestaties van volgende synchronisaties worden verbeterd.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Kan de configuratie niet opslaan omdat de app-referenties niet werken

Voor het uitvoeren van de inrichting vereist Azure AD geldige referenties waarmee verbinding kan worden gemaakt met een API voor gebruikers beheer die wordt geleverd door die app. Als deze referenties niet werken of als u niet weet wat ze zijn, raadpleegt u de zelf studie voor het instellen van deze app, zoals eerder beschreven.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Inrichtings logboeken zeggen dat gebruikers worden overgeslagen en niet ingericht, zelfs als ze worden toegewezen

Wanneer een gebruiker wordt weer gegeven als ' overgeslagen ' in de inrichtings logboeken, is het belang rijk om de uitgebreide details in het logboek bericht te lezen om de reden te bepalen. Hieronder vindt u enkele veelvoorkomende redenen en oplossingen:

- **Er is een bereik filter geconfigureerd** **waarmee de gebruiker wordt gefilterd op basis van een kenmerk waarde**. Zie [op kenmerken gebaseerde toepassing inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie.

- **De gebruiker is niet effectief.** Als u dit specifieke fout bericht ziet, is er een probleem met de gebruikers toewijzings record die is opgeslagen in azure AD. U kunt dit probleem oplossen door de toewijzing van de gebruiker (of groep) uit de app ongedaan te maken en het opnieuw toe te wijzen. Zie [een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)voor meer informatie.

- **Een vereist kenmerk ontbreekt of is niet ingevuld voor een gebruiker.** Het is belang rijk om rekening mee te houden bij het instellen van inrichting om de kenmerk toewijzingen en werk stromen te controleren en te configureren die bepalen welke gebruikers-(of groeps) eigenschappen stromen van Azure AD naar de toepassing. Dit omvat het instellen van de ' overeenkomende eigenschap ' die wordt gebruikt om gebruikers/groepen tussen de twee systemen uniek te identificeren en te vergelijken. Zie voor meer informatie over dit belang rijke proces het [aanpassen van kenmerk toewijzingen voor gebruikers inrichting](../app-provisioning/customize-application-attributes.md).

  * **Kenmerk toewijzingen voor groepen:** Het inrichten van de groeps naam en groeps gegevens, naast de leden, als deze worden ondersteund voor sommige toepassingen. U kunt deze functie in-of uitschakelen door de **toewijzing** voor groeps objecten die op het tabblad **inrichten** wordt weer gegeven in of uit te scha kelen. Als inrichtings groepen is ingeschakeld, moet u de kenmerk toewijzingen controleren om ervoor te zorgen dat er een geschikt veld wordt gebruikt voor de ' overeenkomende ID '. Dit kan de weergave naam of e-mail alias zijn), omdat de groep en de bijbehorende leden niet worden ingericht als de overeenkomende eigenschap leeg is of niet is ingevuld voor een groep in azure AD.

## <a name="next-steps"></a>Volgende stappen
[Gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-toepassingen met Azure Active Directory automatiseren](user-provisioning.md)

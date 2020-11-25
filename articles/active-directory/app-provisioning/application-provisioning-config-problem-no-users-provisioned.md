---
title: Gebruikers worden niet ingericht in mijn toepassing
description: Veelvoorkomende problemen oplossen die zich voordoen wanneer u geen gebruikers ziet die worden weer gegeven in een Azure AD Gallery-toepassing die u hebt geconfigureerd voor het inrichten van gebruikers met Azure AD
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 21f16a0b9c8b3b603735520d5dcdbf61601e1480
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994822"
---
# <a name="no-users-are-being-provisioned"></a>Er worden geen gebruikers ingericht 
>[!NOTE]
>Vanaf 04/16/2020 we hebben het gedrag gewijzigd voor gebruikers die zijn toegewezen aan de standaard-Access-rol. Raadpleeg de sectie hieronder voor meer informatie. 
>
Nadat automatische inrichting is geconfigureerd voor een toepassing (waaronder controleren of de referenties van de app die zijn opgegeven voor Azure AD om verbinding te maken met de app geldig zijn), worden gebruikers en/of groepen ingericht voor de app. Inrichting wordt bepaald door de volgende zaken:

-   Welke gebruikers en groepen zijn **toegewezen** aan de toepassing. Houd er rekening mee dat het inrichten van geneste groepen of Microsoft 365 groepen niet wordt ondersteund. Zie [een gebruiker of groep toewijzen aan een bedrijfs-app in azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)voor meer informatie over de toewijzing.
-   Of **kenmerk toewijzingen** zijn ingeschakeld, en geconfigureerd om geldige kenmerken van Azure ad te synchroniseren met de app. Zie voor meer informatie over kenmerk toewijzingen [aanpassen van kenmerk toewijzingen van gebruikers inrichting voor SaaS-toepassingen in azure Active Directory](customize-application-attributes.md).
-   Hiermee wordt aangegeven of er een **bereik filter** aanwezig is waarmee gebruikers worden gefilterd op basis van specifieke kenmerk waarden. Zie voor meer informatie over het bereik van filters [kenmerk toepassing inrichten met bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
  
Als u ziet dat gebruikers niet worden ingericht, raadpleegt u de [inrichtings Logboeken (preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in azure AD. Zoeken naar logboek vermeldingen voor een specifieke gebruiker.

U kunt toegang krijgen tot de inrichtings Logboeken in de Azure portal door **Azure Active Directory** &gt; **Enter prise apps** &gt; **Provisioning logs (preview)** te selecteren in de sectie **activiteit** . U kunt de inrichtings gegevens zoeken op basis van de naam van de gebruiker of de id in het bron systeem of het doel systeem. Zie [inrichtings Logboeken (preview-versie)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)voor meer informatie. 

In de inrichtings logboeken worden alle bewerkingen vastgelegd die door de inrichtings service worden uitgevoerd, waaronder het uitvoeren van query's op Azure AD voor toegewezen gebruikers die binnen het bereik van de inrichting vallen, het opvragen van de doel-app voor het bestaan van deze gebruikers, het vergelijken van de gebruikers objecten tussen het systeem. Vervolgens kunt u het gebruikers account in het doel systeem toevoegen, bijwerken of uitschakelen op basis van de vergelijking.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Algemene probleem gebieden met het inrichten om te overwegen
Hieronder vindt u een lijst met algemene probleem gebieden die u kunt inzoomen als u een idee hebt van waar u moet beginnen.

- [De inrichtings service lijkt niet te worden gestart](#provisioning-service-does-not-appear-to-start)
- [Inrichtings logboeken zeggen dat gebruikers worden overgeslagen en niet ingericht, ook al toegewezen](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>De inrichtings service lijkt niet te worden gestart
Als u instelt dat de **inrichtings status** moet worden **ingeschakeld** in de sectie **Azure Active Directory de &gt; module bedrijfs Apps &gt; \[ toepassings naam \] &gt; inrichten** van de Azure Portal. Er worden echter geen andere status gegevens op die pagina weer gegeven na de volgende keer opnieuw laden. de service wordt waarschijnlijk uitgevoerd, maar is nog niet klaar met het volt ooien van een eerste cyclus. Controleer de hierboven beschreven **inrichtings Logboeken (preview)** om te bepalen welke bewerkingen de service uitvoert en of er fouten zijn.

>[!NOTE]
>Een eerste cyclus kan 20 minuten tot enkele uren duren, afhankelijk van de grootte van de Azure AD-adres lijst en het aantal gebruikers binnen het bereik van de inrichting. Volgende synchronisaties na de eerste cyclus zijn sneller, omdat er door de inrichtings service water merken worden opgeslagen die de status van beide systemen na de eerste cyclus vertegenwoordigen. De eerste cyclus verbetert de prestaties van volgende synchronisaties.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Inrichtings logboeken zeggen dat gebruikers worden overgeslagen en niet ingericht, zelfs als ze worden toegewezen

Wanneer een gebruiker wordt weer gegeven als ' overgeslagen ' in de inrichtings logboeken, is het belang rijk om het tabblad **stappen** van het logboek te bekijken om de reden te bepalen. Hieronder vindt u enkele veelvoorkomende redenen en oplossingen:

- **Er is een bereik filter geconfigureerd** **waarmee de gebruiker wordt gefilterd op basis van een kenmerk waarde**. Zie [bereik filters](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)voor meer informatie over het bereik van filters.
- **De gebruiker is niet effectief.** Als u dit specifieke fout bericht ziet, is er een probleem met de gebruikers toewijzings record die is opgeslagen in azure AD. U kunt dit probleem oplossen door de toewijzing van de gebruiker (of groep) uit de app ongedaan te verwijderen en opnieuw toe te wijzen. Zie [toegang toewijzen aan gebruikers of groepen](../manage-apps/assign-user-or-group-access-portal.md)voor meer informatie over de toewijzing.
- **Een vereist kenmerk ontbreekt of is niet ingevuld voor een gebruiker.** Het is belang rijk om rekening mee te houden bij het instellen van inrichting door de kenmerk toewijzingen en werk stromen te controleren en te configureren die bepalen welke gebruikers-(of groeps) eigenschappen stromen van Azure AD naar de toepassing. Deze configuratie omvat het instellen van de ' overeenkomende eigenschap ' die wordt gebruikt om gebruikers/groepen tussen de twee systemen uniek te identificeren en te vergelijken. Zie voor meer informatie over dit belang rijke proces de [toewijzing van kenmerk toewijzingen van gebruikers aanpassen voor SaaS-toepassingen in azure Active Directory](customize-application-attributes.md).
- **Kenmerk toewijzingen voor groepen:** Het inrichten van de groeps naam en groeps gegevens, naast de leden, als deze worden ondersteund voor sommige toepassingen. U kunt deze functie in-of uitschakelen door de **toewijzing** voor groeps objecten die op het tabblad **inrichten** wordt weer gegeven in of uit te scha kelen. Als inrichtings groepen is ingeschakeld, moet u de kenmerk toewijzingen controleren om ervoor te zorgen dat er een geschikt veld wordt gebruikt voor de ' overeenkomende ID '. De overeenkomende ID kan de weergave naam of e-mail alias zijn. De groep en de bijbehorende leden worden niet ingericht als de overeenkomende eigenschap leeg is of niet is ingevuld voor een groep in azure AD.
## <a name="provisioning-users-assigned-to-the-default-access-role"></a>Gebruikers inrichten die zijn toegewezen aan de standaard-Access-rol
De standaardrol van een toepassing in de galerie wordt de rol ' standaard toegang ' genoemd. Gebruikers die zijn toegewezen aan deze rol, zijn in het verleden niet ingericht en zijn als overgeslagen gemarkeerd in de [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md) vanwege ' niet effectief recht '. 

**Gedrag voor het inrichten van configuraties die zijn gemaakt na 04/16/2020:** Gebruikers die zijn toegewezen aan de rol standaard toegang, worden op dezelfde as als alle andere functies geëvalueerd. Een gebruiker aan wie de standaard toegang is toegewezen, wordt niet overgeslagen als ' niet effectief in aanmerking komt '. 

**Gedrag voor het inrichten van configuraties die zijn gemaakt vóór 04/16/2020:** Het gedrag wordt voor de volgende 3 maanden vandaag nog uitgevoerd. Gebruikers met de rol standaard toegang worden overgeslagen als niet effectief. Na 2020 juli is het gedrag uniform voor alle toepassingen. Het inrichten van gebruikers met de standaard toegangs functie wordt niet overgeslagen omdat het niet effectief recht heeft. Deze wijziging wordt gemaakt door micro soft, zonder actie van de klant vereist. Als u er zeker van wilt zijn dat deze gebruikers blijven worden overgeslagen, kunt u ook na deze wijziging de juiste bereik filters toep assen of de gebruiker uit de toepassing verwijderen om ervoor te zorgen dat ze buiten het bereik vallen.  

Neem voor vragen over deze wijzigingen contact op met provisioningfeedback@microsoft.com
## <a name="next-steps"></a>Volgende stappen

[Azure AD Connect Sync: uitleg over declaratieve inrichting](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
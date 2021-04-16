---
title: Onverwachte fout bij het uitvoeren van toestemming voor een toepassing | Microsoft Docs
description: Bespreekt fouten die kunnen optreden tijdens het toestemmingsproces voor een toepassing en wat u er aan kunt doen
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad216d0062928fc16b0f2226daabb6258d09063c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378122"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Onverwachte fout bij het uitvoeren van toestemming voor een toepassing

In dit artikel worden fouten besproken die kunnen optreden tijdens het toestemmingsproces voor een toepassing. Zie Verificatiescenario's voor Azure AD als u problemen met onverwachte toestemmingsprompts die geen foutberichten bevatten, [wilt oplossen.](../develop/authentication-vs-authorization.md)

Veel toepassingen die zijn geïntegreerd met Azure Active Directory hebben machtigingen nodig voor toegang tot andere resources om te kunnen functioneren. Wanneer deze resources ook zijn geïntegreerd met Azure Active Directory, worden vaak machtigingen voor toegang aangevraagd met behulp van het algemene toestemmingsraamwerk. Er wordt een toestemmingsprompt weergegeven, die doorgaans plaatsvindt wanneer een toepassing voor het eerst wordt gebruikt, maar ook bij een volgend gebruik van de toepassing.

Bepaalde voorwaarden moeten waar zijn om een gebruiker toestemming te geven voor de machtigingen die een toepassing vereist. Als niet aan deze voorwaarden wordt voldaan, kunnen de volgende fouten optreden.

## <a name="requesting-not-authorized-permissions-error"></a>Fout bij het aanvragen van niet-geautoriseerde machtigingen
* **AADSTS90093:** &lt; clientAppDisplayName vraagt een of meer machtigingen &gt; aan die u niet mag verlenen. Neem contact op met een beheerder die namens u toestemming kan geven voor deze toepassing.
* **AADSTS90094:** &lt; clientAppDisplayName &gt; heeft toestemming nodig voor toegang tot resources in uw organisatie die alleen een beheerder kan verlenen. Vraag een beheerder om toestemming te verlenen voor deze app voordat u deze kunt gebruiken.

Deze fout treedt op wanneer een gebruiker die geen globale beheerder is, probeert een toepassing te gebruiken die machtigingen aanvraagt die alleen een beheerder kan verlenen. Deze fout kan worden opgelost door een beheerder die namens de organisatie toegang verleent tot de toepassing.

Deze fout kan ook optreden wanneer een gebruiker geen toestemming kan geven voor een toepassing omdat Microsoft detecteert dat de machtigingsaanvraag riskant is. In dit geval wordt een controlegebeurtenis ook vastgelegd met de categorie 'ApplicationManagement', het activiteitstype 'Toestemming voor toepassing' en de Statusreden van 'Riskante toepassing gedetecteerd'.

Een ander scenario waarin deze fout kan optreden, is wanneer de gebruikerstoewijzing vereist is voor de toepassing, maar er geen beheerderstoewijzing is gegeven. In dit geval moet de beheerder eerst toestemming van de beheerder geven.   

## <a name="policy-prevents-granting-permissions-error"></a>Beleid voorkomt het verlenen van machtigingen fout
* **AADSTS90093:** Een beheerder van tenantDisplayName heeft een beleid ingesteld waarmee wordt voorkomen dat u de naam van de app de machtigingen verleent &lt; &gt; die worden &lt; &gt; gevraagd. Neem contact op met een beheerder van tenantDisplayName, die namens u machtigingen &lt; voor deze app kan &gt; verlenen.

Deze fout treedt op wanneer een globale beheerder de mogelijkheid voor gebruikers om toestemming te geven voor toepassingen uit schakelt, probeert een niet-beheerder een toepassing te gebruiken die toestemming vereist. Deze fout kan worden opgelost door een beheerder die namens de organisatie toegang verleent tot de toepassing.

## <a name="intermittent-problem-error"></a>Onregelmatige probleemfout
* **AADSTS90090:** Het lijkt erop dat tijdens het aanmeldingsproces een onregelmatige probleem is aangetroffen bij het vastleggen van de machtigingen die u hebt verleend &lt; aan clientAppDisplayName. &gt; probeer het later opnieuw.

Deze fout geeft aan dat er af en toe een probleem aan de servicezijde is opgetreden. Dit kan worden opgelost door opnieuw te proberen toestemming te geven voor de toepassing.

## <a name="resource-not-available-error"></a>Fout: Resource niet beschikbaar
* **AADSTS65005:** De &lt; app-clientAppDisplayName heeft machtigingen aangevraagd voor toegang tot &gt; een &lt; resourceresourceAppDisplayName &gt; die niet beschikbaar is. 

Neem contact op met de ontwikkelaar van de toepassing.

##  <a name="resource-not-available-in-tenant-error"></a>Resource niet beschikbaar in tenantfout
* **AADSTS65005:** &lt; clientAppDisplayName vraagt toegang aan tot &gt; een resourceresourceAppDisplayName die niet beschikbaar is in de &lt; tenant van uw &gt; &lt; organisatieDisplayName. &gt; 

Zorg ervoor dat deze resource beschikbaar is of neem contact op met een beheerder &lt; van tenantDisplayName. &gt;

## <a name="permissions-mismatch-error"></a>Fout bij niet-overeenkomende machtigingen
* **AADSTS65005:** De app heeft toestemming gevraagd voor toegang tot &lt; resourceresourceAppDisplayName. &gt; Deze aanvraag is mislukt omdat deze niet overeen komt met de manier waarop de app vooraf is geconfigureerd tijdens de registratie van de app. Neem contact op met de leverancier van de app.**

Deze fouten treden allemaal op wanneer de toepassing waar een gebruiker toestemming voor probeert te geven machtigingen aanvraagt voor toegang tot een resourcetoepassing die niet kan worden gevonden in de directory (tenant) van de organisatie. Deze situatie kan om verschillende redenen optreden:

-   De ontwikkelaar van de clienttoepassing heeft de toepassing onjuist geconfigureerd, waardoor deze toegang tot een ongeldige resource kan aanvragen. In dit geval moet de ontwikkelaar van de toepassing de configuratie van de clienttoepassing bijwerken om dit probleem op te lossen.

-   Een service-principal die de doelresourcetoepassing vertegenwoordigt, bestaat niet in de organisatie of bestond in het verleden, maar is verwijderd. Om dit probleem op te lossen, moet er een service-principal voor de resourcetoepassing worden ingericht in de organisatie, zodat de clienttoepassing hiervoor machtigingen kan aanvragen. De service-principal kan op verschillende manieren worden ingericht, afhankelijk van het type toepassing, waaronder:

    -   Een abonnement verkrijgen voor de resourcetoepassing (gepubliceerde Microsoft-toepassingen)

    -   Toestemming geven voor de resourcetoepassing

    -   De toepassingsmachtigingen verlenen via de Azure Portal

    -   De toepassing toevoegen vanuit de Azure AD-toepassingsgalerie

## <a name="risky-app-error-and-warning"></a>Riskante app-fout en waarschuwing
* **AADSTS900941:** Toestemming van de beheerder is vereist. De app wordt als riskant beschouwd. (AdminConsentRequiredDueToRiskyApp)
* Deze app kan riskant zijn. Als u deze app vertrouwt, vraagt u uw beheerder om u toegang te verlenen.
* **AADSTS900981:** Er is een toestemmingsaanvraag van de beheerder ontvangen voor een riskante app. (AdminConsentRequestRiskyAppWarning)
* Deze app kan riskant zijn. Ga alleen door als u deze app vertrouwt.

Beide berichten worden weergegeven wanneer Microsoft heeft vastgesteld dat de toestemmingsaanvraag riskant kan zijn. Dit kan onder andere gebeuren als [](../develop/publisher-verification-overview.md) een geverifieerde uitgever niet is toegevoegd aan de app-registratie. De eerste foutcode en het eerste bericht worden aan eindgebruikers weergegeven wanneer de werkstroom voor [beheerdersmachtiging](configure-admin-consent-workflow.md) is uitgeschakeld. De tweede code en het tweede bericht worden aan eindgebruikers weergegeven wanneer de werkstroom voor beheerders toestemming is ingeschakeld en voor beheerders. 

Eindgebruikers kunnen geen toestemming verlenen aan apps die als riskant zijn gedetecteerd. Beheerders kunnen dit wel, maar moeten de app zorgvuldig evalueren en voorzichtig zijn. Als de app bij verdere beoordeling verdacht lijkt, kan deze worden gerapporteerd aan Microsoft via het toestemmingsscherm. 

## <a name="next-steps"></a>Volgende stappen 

[Apps, machtigingen en toestemming in Azure Active Directory (v1-eindpunt)](../develop/quickstart-register-app.md)<br>

[Scopes, machtigingen en toestemming in het Azure Active Directory (v2.0-eindpunt)](../develop/v2-permissions-and-consent.md)
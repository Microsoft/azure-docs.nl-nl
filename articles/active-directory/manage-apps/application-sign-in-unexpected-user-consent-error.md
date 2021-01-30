---
title: Er is een onverwachte fout opgetreden bij het uitvoeren van de toestemming voor een toepassing | Microsoft Docs
description: Hierin worden fouten beschreven die zich kunnen voordoen tijdens het proces van het inzenden van een toepassing en wat u hierover kunt doen
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25515c29ae3e5623b447232ed6f935a668a58c3a
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090066"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Er is een onverwachte fout opgetreden bij het uitvoeren van de toestemming voor een toepassing

In dit artikel worden fouten beschreven die zich kunnen voordoen tijdens het proces van het inzenden van een toepassing. Zie [verificatie scenario's voor Azure AD](../develop/authentication-vs-authorization.md)als u problemen met een onverwachte toestemming ondervindt die geen fout berichten bevatten.

Veel toepassingen die met Azure Active Directory worden geïntegreerd, hebben machtigingen nodig om toegang te krijgen tot andere bronnen om te kunnen functioneren. Wanneer deze bronnen ook zijn geïntegreerd met Azure Active Directory, worden machtigingen voor toegang tot deze resources vaak aangevraagd met behulp van het common toestemming raamwerk. Er wordt een toestemming prompt weer gegeven, die doorgaans plaatsvindt wanneer een toepassing voor het eerst wordt gebruikt, maar kan ook worden uitgevoerd op een volgend gebruik van de toepassing.

Bepaalde voor waarden moeten waar zijn voor een gebruiker om toestemming te geven aan de machtigingen die een toepassing nodig heeft. Als niet aan deze voor waarden wordt voldaan, kunnen de volgende fouten optreden.

## <a name="requesting-not-authorized-permissions-error"></a>Fout bij het aanvragen van niet-geautoriseerde machtigingen
* **AADSTS90093:** &lt; clientAppDisplayName &gt; vraagt een of meer machtigingen aan die u niet mag verlenen. Neem contact op met een beheerder die namens u aan deze toepassing kan worden door gegeven.
* **AADSTS90094:** &lt; clientAppDisplayName &gt; heeft toestemming nodig om toegang te krijgen tot bronnen in uw organisatie die alleen door een beheerder kunnen worden verleend. Vraag een beheerder om toestemming te verlenen voor deze app voordat u deze kunt gebruiken.

Deze fout treedt op wanneer een gebruiker die geen globale beheerder is, een toepassing probeert te gebruiken die machtigingen aanvraagt die alleen door een beheerder kunnen worden verleend. Deze fout kan worden opgelost door een beheerder die namens hun organisatie toegang tot de toepassing verleent.

Deze fout kan ook optreden wanneer een gebruiker niet kan worden gestemd op een toepassing omdat micro soft detecteert dat de machtigings aanvraag riskant is. In dit geval wordt een controle gebeurtenis ook vastgelegd met de categorie ' ApplicationManagement ', het activiteitstype ' instemming met de toepassing ' en de status van ' Risk ante toepassing gedetecteerd '.

Een ander scenario waarin deze fout zich kan voordoen, is wanneer de gebruikers toewijzing voor de toepassing is vereist, maar er is geen toestemming van de beheerder opgegeven. In dit geval moet de beheerder eerst toestemming geven voor de beheerder.   

## <a name="policy-prevents-granting-permissions-error"></a>Beleid voor komt fout bij het toekennen van machtigingen
* **AADSTS90093:** Een beheerder van &lt; tenantDisplayName &gt; heeft een beleid ingesteld waarmee wordt voor komen dat u &lt; de naam van de app aan &gt; de aanvraag toevraagt. Neem contact op met een beheerder van &lt; tenantDisplayName &gt; , die namens u machtigingen voor deze app kan verlenen.

Deze fout treedt op wanneer een globale beheerder de mogelijkheid voor gebruikers om toestemming voor toepassingen te geven uitschakelt, een niet-beheerders gebruiker probeert een toepassing te gebruiken die toestemming vereist. Deze fout kan worden opgelost door een beheerder die namens hun organisatie toegang tot de toepassing verleent.

## <a name="intermittent-problem-error"></a>Fout met onregelmatige problemen
* **AADSTS90090:** Het lijkt erop dat het aanmeldings proces een onregelmatige fout heeft aangetroffen bij het vastleggen van de machtigingen die u probeerde toe te kennen aan &lt; clientAppDisplayName &gt; . Probeer het later opnieuw.

Deze fout geeft aan dat er een regel matig probleem is opgetreden in de service zijde. U kunt het probleem oplossen door opnieuw te proberen om de toepassing te accepteren.

## <a name="resource-not-available-error"></a>Fout bericht bron niet beschikbaar
* **AADSTS65005:** De app- &lt; clientAppDisplayName &gt; heeft machtigingen aangevraagd voor toegang tot een resource- &lt; resourceAppDisplayName &gt; die niet beschikbaar is. 

Neem contact op met de ontwikkelaar van de toepassing.

##  <a name="resource-not-available-in-tenant-error"></a>De resource is niet beschikbaar in de Tenant fout
* **AADSTS65005:** &lt; clientAppDisplayName &gt; vraagt toegang tot een resource- &lt; resourceAppDisplayName &gt; die niet beschikbaar is in de tenantDisplayName van uw organisatie &lt; &gt; . 

Zorg ervoor dat deze resource beschikbaar is of neem contact op met een beheerder van &lt; tenantDisplayName &gt; .

## <a name="permissions-mismatch-error"></a>Fout met niet-overeenkomende machtigingen
* **AADSTS65005:** De app heeft toestemming gevraagd om toegang te krijgen tot de resource- &lt; resourceAppDisplayName &gt; . De aanvraag is mislukt omdat deze niet overeenkomt met de manier waarop de app vooraf is geconfigureerd tijdens de app-registratie. Neem contact op met de leverancier van de app. * *

Deze fouten treden allemaal op wanneer de toepassing een gebruiker probeert toestemming te geven om toegang te krijgen tot een resource toepassing die niet kan worden gevonden in de directory van de organisatie (Tenant). Deze situatie kan om verschillende redenen optreden:

-   De ontwikkelaar van de client toepassing heeft hun toepassing onjuist geconfigureerd, waardoor deze toegang tot een ongeldige resource kan aanvragen. In dit geval moet de ontwikkelaar van de toepassing de configuratie van de client toepassing bijwerken om dit probleem op te lossen.

-   Een service-principal die de doel resource toepassing vertegenwoordigt, bestaat niet in de organisatie of bestond al in het verleden, maar is verwijderd. Om dit probleem op te lossen, moet een service-principal voor de resource toepassing worden ingericht in de organisatie, zodat de client toepassing hiervoor machtigingen kan aanvragen. De service-principal kan op verschillende manieren worden ingericht, afhankelijk van het type toepassing, met inbegrip van:

    -   Een abonnement voor de resource toepassing verkrijgen (door micro soft gepubliceerde toepassingen)

    -   De resource toepassing wordt ingestemd

    -   De toepassings machtigingen verlenen via de Azure Portal

    -   De toepassing toevoegen vanuit de Azure AD-toepassings galerie

## <a name="risky-app-error-and-warning"></a>Fout met Risk ante app en waarschuwing
* **AADSTS900941:** Toestemming van de beheerder is vereist. De app wordt beschouwd als riskant. (AdminConsentRequiredDueToRiskyApp)
* Deze app kan riskant zijn. Als u deze app vertrouwt, vraagt u uw beheerder om toegang te verlenen.
* **AADSTS900981:** Er is een aanvraag voor een beheerder ontvangen voor een Risk ante app. (AdminConsentRequestRiskyAppWarning)
* Deze app kan riskant zijn. Ga alleen door als u deze app vertrouwt.

Beide berichten worden weer gegeven wanneer micro soft heeft vastgesteld dat de aanvraag voor toestemming mogelijk riskant is. Onder een aantal andere factoren kan dit gebeuren als een [geverifieerde Uitgever](../develop/publisher-verification-overview.md) niet is toegevoegd aan de app-registratie. De eerste fout code en het volgende bericht worden weer gegeven voor eind gebruikers wanneer de [beheerder de toestemming werk stroom](configure-admin-consent-workflow.md) is uitgeschakeld. De tweede code en het bericht worden weer gegeven aan eind gebruikers wanneer de beheerder de werk stroom voor het beheer van toestemming heeft ingeschakeld en beheerders. 

Eind gebruikers kunnen geen toestemming verlenen voor apps die zijn gedetecteerd als riskant. Beheerders zijn in staat om de app zeer zorgvuldig te evalueren en voorzichtig te gaan. Als de app verdacht lijkt te zijn bij verdere beoordeling, kan deze worden gerapporteerd aan micro soft vanuit het venster voor toestemming. 

## <a name="next-steps"></a>Volgende stappen 

[Apps, machtigingen en toestemming in Azure Active Directory (v1-eind punt)](../develop/quickstart-register-app.md)<br>

[Bereiken, machtigingen en toestemming in de Azure Active Directory (v 2.0-eind punt)](../develop/v2-permissions-and-consent.md)
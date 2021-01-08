---
title: Registratievelden in Azure-portal voor zelf ontwikkelde apps
description: Richt lijnen voor het registreren van een aangepaste ontwikkelde toepassing met Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: 3c540c1b032676e07450eccfea3f9618ddc3ee64
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017066"
---
# <a name="azure-portal-registration-fields-for-custom-developed-apps"></a>Registratievelden in Azure-portal voor zelf ontwikkelde apps

In dit artikel vindt u een korte beschrijving van alle beschik bare velden in het formulier voor toepassings registratie in de [Azure Portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Een nieuwe toepassing registreren

-   Als u een nieuwe toepassing wilt registreren, gaat u naar de <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

-   Klik in het navigatie deel venster links op **Azure Active Directory.**

-   Kies **app-registraties** en klik op **toevoegen**.

-   Hiermee opent u het formulier voor het registreren van de toepassing.

## <a name="fields-in-the-application-registration-form"></a>Velden in het formulier toepassings registratie

| Veld            | Beschrijving                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Naam             | De naam van de toepassing. De waarde moet mini maal vier tekens bevatten.                |
| Ondersteunde accounttypen| Selecteer welke accounts u uw toepassing wilt laten ondersteunen: accounts in deze organisatie-map alleen, accounts in een organisatie Directory of accounts in een organisatorische map en persoonlijke micro soft-accounts.  |
| Omleidings-URI (optioneel) | Selecteer het type app dat u wilt maken, **Web** of **open bare client (mobiele & bureau blad)** en voer vervolgens de omleidings-URI (of antwoord-URL) in voor uw toepassing. Geef voor webtoepassingen de basis-URL van de app op. http://localhost:31544 kan bijvoorbeeld de URL zijn van een web-app die op uw lokale machine wordt uitgevoerd. Gebruikers moeten deze URL gebruiken om zich bij een webclienttoepassing aan te melden. Geef voor openbare clienttoepassingen de URI op die in Azure Active Directory wordt gebruikt om tokenantwoorden te retourneren. Voer een waarde in die specifiek is voor uw toepassing, zoals myapp://auth. Bekijk onze [Quick](./index.yml)starts voor specifieke voor beelden voor webtoepassingen of systeem eigen toepassingen.|

Zodra u de bovenstaande velden hebt ingevuld, wordt de toepassing geregistreerd in het Azure Portal en wordt u omgeleid naar de overzichts pagina van de toepassing. De pagina instellingen in het linkerdeel venster onder **beheren** hebben meer velden waarmee u uw toepassing kunt aanpassen. In de onderstaande tabellen worden alle velden beschreven. U ziet alleen een subset van deze velden, afhankelijk van of u een webtoepassing of een open bare client toepassing hebt gemaakt.

### <a name="overview"></a>Overzicht

| Veld           | Beschrijving        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Toepassings-id  | Wanneer u een toepassing registreert, wijst Azure AD een toepassings-ID toe aan uw toepassing. De toepassings-ID kan worden gebruikt om uw toepassing op unieke wijze te identificeren bij verificatie aanvragen voor Azure AD, en om toegang te krijgen tot resources zoals de Graph API.                                                          |
| App-id-URI      | Dit moet een unieke URI zijn, doorgaans de naam van de **https://- &lt; Tenant naam van het formulier \_ &gt; / &lt; \_ &gt; .** Dit wordt gebruikt tijdens de autorisatie toekennings stroom, als een unieke id voor het opgeven van de bron waarvoor het token moet worden uitgegeven. Het wordt ook de claim ' AUD ' in het verleende toegangs token. |

### <a name="branding"></a>Huisstijl

| Veld           | Beschrijving        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nieuw logo uploaden | U kunt dit gebruiken om een logo te uploaden voor uw toepassing. Het logo moet de indeling. bmp,. jpg of. png hebben en de bestands grootte moet kleiner zijn dan 100 KB. De afmetingen van de afbeelding moeten 215x215 pixels zijn, met de afmetingen van de centrale afbeelding van 94x94 pixels.|
| URL van start pagina   | Dit is de aanmeldings-URL die is opgegeven tijdens de registratie van de toepassing.|

### <a name="authentication"></a>Verificatie

| Veld           | Beschrijving        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Afmeldings-URL      | Dit is de uitzonderings-URL voor eenmalige afmelding. Azure AD verzendt een afmeldings aanvraag naar deze URL wanneer de gebruiker de sessie met Azure AD wist met behulp van een andere geregistreerde toepassing.|
| Ondersteunde accounttypen  | Met deze schakel optie geeft u op of de toepassing kan worden gebruikt door meerdere tenants. Dit betekent meestal dat externe organisaties uw toepassing kunnen gebruiken door deze te registreren in hun Tenant en toegang te verlenen tot de gegevens van de organisatie.|
| Omleidings-URL's      | De omleidings-of antwoord-Url's zijn de eind punten waar Azure AD tokens retourneert die door uw toepassing worden aangevraagd. Voor systeem eigen toepassingen is dit de locatie waar de gebruiker wordt verzonden na een geslaagde autorisatie. Azure AD controleert of de omleidings-URI die uw toepassing levert in de OAuth 2,0-aanvraag overeenkomt met een van de geregistreerde waarden in de portal.|

### <a name="certificates-and-secrets"></a>Certificaten en geheimen

| Veld           | Beschrijving        |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Clientgeheimen            | U kunt client geheimen of-sleutels maken om programmatisch toegang te krijgen tot Web-Api's die zijn beveiligd met Azure AD zonder tussen komst van de gebruiker. Voer op de pagina **Nieuw client geheim** een sleutel beschrijving en de verval datum in en sla het bestand op om de sleutel te genereren. Zorg ervoor dat u het bestand ergens anders opslaat, omdat u het later niet meer kunt openen.             |

## <a name="next-steps"></a>Volgende stappen

[Toepassingen beheren met Azure Active Directory](../manage-apps/what-is-application-management.md)
---
title: bestand opnemen
description: bestand opnemen
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 22411e5a80f555a3ead05d39466a7a175923d9bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104890"
---
* Nadat u het juiste platform hebt geselecteerd in stap 4 van [platform instellingen configureren](../articles/active-directory/develop/quickstart-register-app.md#configure-platform-settings) , configureert u de **omleidings-Uri's** en **toegangs tokens** in het deel venster aan de rechter kant van de gebruikers interface.

    * **Omleidings-uri's** moeten overeenkomen met het adres dat is opgegeven door de verificatie aanvraag:

        * Voor apps die worden gehost in een lokale ontwikkel omgeving selecteert u **open bare client (mobiele & bureau blad)**. Zorg ervoor dat de **open bare client** is ingesteld op **Ja**.
        * Selecteer **Web** voor Single-Page-apps die worden gehost op Azure app service.

    * Bepaal of een **Afmeldings-URL** geschikt is.

    * Schakel de impliciete toekennings stroom in door **toegangs tokens** of **id-tokens** te controleren.

    [![Omleidings-Uri's maken](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klik op **configureren** en vervolgens op **Opslaan**.

* Koppel uw Azure Active Directory-app-Azure Time Series Insights. Selecteer **API-machtigingen**  >  **een machtigings**  >  **-api's toevoegen die mijn organisatie gebruikt**.

    [![Een API koppelen aan uw Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Typ `Azure Time Series Insights` in de zoek balk en selecteer `Azure Time Series Insights` .

* Geef vervolgens de type API-machtiging op die uw app vereist. Standaard worden **gedelegeerde machtigingen** gemarkeerd. Kies een machtigings type en selecteer vervolgens **machtigingen toevoegen**.

    [![Geef het type API-machtiging op dat voor uw app is vereist](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Voeg referenties toe](../articles/active-directory/develop/quickstart-register-app.md#add-credentials) als de toepassing de api's van uw omgeving als zichzelf aanroept. Met referenties kan uw toepassing zichzelf verifiëren, waardoor er geen interactie van een gebruiker tijdens runtime nodig is.
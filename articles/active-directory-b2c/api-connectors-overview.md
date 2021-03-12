---
title: Over API-connectors in Azure AD B2C
description: Gebruik Azure Active Directory (Azure AD) API-connectors om uw registratie gebruikers stromen aan te passen en uit te breiden met behulp van web-Api's.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: d7a3301cb6ec10e75979d0e1fdfad52c7103a2aa
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611518"
---
# <a name="use-api-connectors-to-customize-and-extend-sign-up-user-flows"></a>API-connectors gebruiken om aanmeldingen van gebruikers aan te passen en uit te breiden

> [!IMPORTANT]
> API-connectors voor aanmelden is een open bare preview-functie van Azure AD B2C. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="overview"></a>Overzicht 
Als ontwikkelaar of IT-beheerder kunt u API-connectors gebruiken om uw registratie-gebruikers stromen te integreren met Web Api's om de registratie-ervaring aan te passen en te integreren met externe systemen. Met API-connectors kunt u bijvoorbeeld het volgende doen:

- **Gebruikers invoer gegevens valideren**. Valideer op basis van ongeldige of ongeldige gebruikers gegevens. U kunt bijvoorbeeld door de gebruiker gegeven gegevens valideren op basis van bestaande gegevens in een extern gegevens archief of een lijst met toegestane waarden. Als dit ongeldig is, kunt u een gebruiker vragen om geldige gegevens op te geven of de gebruiker te blok keren om de registratie stroom voort te zetten.
- **Integreren met een aangepaste goedkeurings werk stroom**. Maak verbinding met een aangepast goedkeurings systeem voor het beheren en beperken van het maken van accounts.
- **Gebruikers kenmerken overschrijven**. Een waarde opnieuw Format teren of toewijzen aan een kenmerk dat van de gebruiker wordt verzameld. Als een gebruiker bijvoorbeeld de eerste naam in alle kleine letters of hoofd letters typt, kunt u de naam alleen met de eerste letter in een letter type Format teren. 
- **Verificatie van de identiteit uitvoeren**. Gebruik een service voor identiteits verificatie om een extra beveiligings niveau toe te voegen aan beslissingen voor het maken van een account.
- **Aangepaste bedrijfs logica uitvoeren**. U kunt downstream-gebeurtenissen in uw Cloud systemen activeren om Push meldingen te verzenden, zakelijke data bases bij te werken, machtigingen te beheren, data bases te controleren en andere aangepaste acties uit te voeren.

Een API-connector biedt Azure Active Directory de informatie die nodig is om het API-eind punt aan te roepen door de URL en verificatie van het HTTP-eind punt te definiëren voor de API-aanroep. Zodra u een API-connector hebt geconfigureerd, kunt u deze inschakelen voor een specifieke stap in een gebruikers stroom. Wanneer een gebruiker die stap in de registratie stroom bereikt, wordt de API-connector aangeroepen en resultatenset als een HTTP POST-aanvraag voor uw API, waarbij gebruikers gegevens (' claims ') worden verzonden als sleutel-waardeparen in een JSON-hoofd tekst. De API-reactie kan invloed hebben op de uitvoering van de gebruikers stroom. Met het API-antwoord kan bijvoorbeeld worden geblokkeerd dat een gebruiker zich kan aanmelden, de gebruiker vragen om gegevens opnieuw in te voeren of gebruikers kenmerken te overschrijven en toe te voegen.

## <a name="where-you-can-enable-an-api-connector-in-a-user-flow"></a>Waar u een API-connector in een gebruikers stroom kunt inschakelen

Er zijn twee locaties in een gebruikers stroom waar u een API-connector kunt inschakelen:

- Nadat u zich hebt aangemeld met een id-provider
- Voordat u de gebruiker maakt

> [!IMPORTANT]
> In beide gevallen worden de API-connectors aangeroepen tijdens het **Aanmelden van** de gebruiker, niet bij het aanmelden.

### <a name="after-signing-in-with-an-identity-provider"></a>Nadat u zich hebt aangemeld met een id-provider

Een API-connector in deze stap van het registratie proces wordt onmiddellijk geactiveerd nadat de gebruiker is geverifieerd met een id-provider (zoals Google, Facebook, & Azure AD). Deze stap gaat vooraf aan de ***pagina kenmerk verzameling***. Dit is het formulier dat wordt weer gegeven aan de gebruiker om gebruikers kenmerken te verzamelen. Deze stap wordt niet aangeroepen als een gebruiker wordt geregistreerd met een lokaal account. Hier volgen enkele voor beelden van API-connector scenario's die u in deze stap kunt inschakelen:

- Gebruik het e-mail adres of de federatieve identiteit die de gebruiker heeft gegeven om claims op te zoeken in een bestaand systeem. Retour neer deze claims van het bestaande systeem, vul de pagina kenmerk verzameling vooraf in en maak deze beschikbaar om het token te retour neren.
- Implementeer een lijst met toegestane of geblokkeerde websites op basis van de sociale identiteit.

### <a name="before-creating-the-user"></a>Voordat u de gebruiker maakt

Een API-connector in deze stap van het registratie proces wordt aangeroepen na de pagina kenmerk verzameling, als er een is opgenomen. Deze stap wordt altijd aangeroepen voordat een gebruikers account wordt gemaakt. Hier volgen enkele voor beelden van scenario's die u op dit moment kunt inschakelen tijdens de registratie:

- Gebruikers invoer gegevens valideren en een gebruiker vragen om gegevens opnieuw in te dienen.
- Blok keren dat gebruikers zich registreren op basis van gegevens die door de gebruiker zijn ingevoerd.
- Verificatie van de identiteit uitvoeren.
- Een query uitvoeren op externe systemen voor bestaande gegevens over de gebruiker om deze in het toepassings token te retour neren of op te slaan in azure AD.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [toevoegen van een API-connector aan een gebruikers stroom](add-api-connector.md)
- Ga aan de slag met onze voor [beelden](code-samples.md#api-connectors).
<!-- - Learn how to [add a custom approval system to self-service sign-up](add-approvals.md) -->
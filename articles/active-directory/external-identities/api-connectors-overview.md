---
title: Over API-connectors in self-service-aanmeld stromen-Azure AD
description: Gebruik de API-connectors van Azure Active Directory (Azure AD) om uw selfservice registratie gebruikers stromen aan te passen en uit te breiden met behulp van web-Api's.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a5563ff1f57f6b3684834a2488fc0665ac5eddd
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610039"
---
# <a name="use-api-connectors-to-customize-and-extend-self-service-sign-up"></a>API-connectors gebruiken om de Self-Service-aanmelding aan te passen en uit te breiden 

## <a name="overview"></a>Overzicht 
Als ontwikkelaar of IT-beheerder kunt u API-connectors gebruiken om uw [self-service registratie-gebruikers stromen](self-service-sign-up-overview.md) te integreren met Web api's om de registratie-ervaring aan te passen en te integreren met externe systemen. Met API-connectors kunt u bijvoorbeeld het volgende doen:

- [**Integreren met een aangepaste goedkeurings werk stroom**](self-service-sign-up-add-approvals.md). Maak verbinding met een aangepast goedkeurings systeem voor het beheren en beperken van het maken van accounts.
- [**Verificatie van de identiteit uitvoeren**](code-samples-self-service-sign-up.md#identity-verification). Gebruik een service voor identiteits verificatie om een extra beveiligings niveau toe te voegen aan beslissingen voor het maken van een account.
- **Gebruikers invoer gegevens valideren**. Valideer op basis van ongeldige of ongeldige gebruikers gegevens. U kunt bijvoorbeeld door de gebruiker gegeven gegevens valideren op basis van bestaande gegevens in een extern gegevens archief of een lijst met toegestane waarden. Als dit ongeldig is, kunt u een gebruiker vragen om geldige gegevens op te geven of de gebruiker te blok keren om de registratie stroom voort te zetten.
- **Gebruikers kenmerken overschrijven**. Een waarde opnieuw Format teren of toewijzen aan een kenmerk dat van de gebruiker wordt verzameld. Als een gebruiker bijvoorbeeld de eerste naam in alle kleine letters of hoofd letters typt, kunt u de naam alleen met de eerste letter in een letter type Format teren. 
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
- Meer informatie over het [toevoegen van een API-connector aan een gebruikers stroom](self-service-sign-up-add-api-connector.md)
- Meer informatie over het [toevoegen van een aangepast goedkeurings systeem aan self-service registratie](self-service-sign-up-add-approvals.md)
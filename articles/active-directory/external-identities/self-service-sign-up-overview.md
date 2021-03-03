---
title: Aanmelden via selfservice voor externe identiteiten-Azure AD
description: Meer informatie over het toestaan van externe gebruikers om zich aan te melden voor uw toepassingen zelf door aanmelding via selfservice in te scha kelen. Maak een persoonlijke aanmeldings ervaring door de self-service voor het registreren van gebruikers aan te passen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13023ef93cabcf46924cc2cc76dc2d868c4a1ddd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653559"
---
# <a name="self-service-sign-up"></a>Selfservice registreren

Wanneer u een toepassing deelt met externe gebruikers, weet u mogelijk niet altijd vooraf wie toegang tot de toepassing nodig heeft. Als alternatief voor het rechtstreeks verzenden van uitnodigingen naar personen, kunt u externe gebruikers toestaan zich aan te melden voor specifieke toepassingen zelf door het aanmelden via een self-service in te scha kelen. U kunt een persoonlijke aanmeldings ervaring maken door de self-service voor het registreren van gebruikers aan te passen. U kunt bijvoorbeeld opties bieden om u aan te melden bij Azure AD of Social id-providers en informatie over de gebruiker te verzamelen tijdens het registratie proces.

> [!NOTE]
> U kunt gebruikers stromen koppelen aan apps die zijn gebouwd door uw organisatie. Gebruikers stromen kunnen niet worden gebruikt voor micro soft-apps, zoals share point of teams.

## <a name="user-flow-for-self-service-sign-up"></a>Gebruikers stroom voor Self-service registratie

Met een self-service voor het aanmelden van gebruikers wordt een aanmeldings ervaring gemaakt voor uw externe gebruikers via de toepassing die u wilt delen. De gebruikers stroom kan worden gekoppeld aan een of meer van uw toepassingen. Eerst schakelt u de self-service registratie voor uw Tenant in en gaat u door met de id-providers die u toestaat dat externe gebruikers zich aanmelden voor aanmelding. Vervolgens maakt u de gebruikers stroom voor registratie en past u deze aan en wijst u uw toepassingen hieraan toe.
U kunt instellingen voor de gebruikers stroom configureren om te bepalen hoe de gebruiker zich aanmeldt voor de toepassing:

- Account typen die worden gebruikt voor aanmelding, zoals sociale accounts zoals Facebook of Azure AD-accounts
- Kenmerken die moeten worden verzameld van de gebruiker die zich aanmeldt, zoals de voor naam, de post code of het land of de regio van de locatie

Wanneer een gebruiker zich wil aanmelden bij uw toepassing, of het nu gaat om een web-, mobiel-, desktop-of single-page-toepassing (SPA), initieert de toepassing een autorisatie aanvraag naar het eind punt van de gebruikers stroom. De gebruikers stroom definieert en beheert de gebruikers ervaring. Wanneer de gebruiker de stroom voor het registreren van gebruikers voltooit, genereert Azure AD een token en leidt de gebruiker terug naar uw toepassing. Wanneer de aanmelding is voltooid, wordt een gast account ingericht voor de gebruiker in de Directory. Meerdere toepassingen kunnen dezelfde gebruikers stroom gebruiken.

## <a name="example-of-self-service-sign-up"></a>Voor beeld van een self-service registratie

In het volgende voor beeld ziet u hoe u met de self-service registratie mogelijkheden voor gast gebruikers uw sociale id-providers naar Azure AD brengt.  
Een partner van Woodgrove opent de Woodgrove-app. Ze bepalen dat ze zich willen registreren voor een leveranciers account, zodat ze het account van uw leverancier aanvragen selecteren, waarmee de self-service-registratie stroom wordt gestart.

![Voor beeld van start pagina voor aanmelden via self-service](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

Ze gebruiken het e-mail adres van hun keuze om zich aan te melden.

![Voor beeld van het weer geven van een selectie van Facebook voor aanmelden](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD maakt een relatie met Woodgrove met behulp van het Facebook-account van de partner en maakt een nieuw gast account voor de gebruiker nadat het zich heeft aangemeld.

Woodgrove wil meer weten over de gebruiker, zoals de naam, de bedrijfs naam, de code van de zakelijke registratie, het telefoon nummer.

![Voor beeld van aanmeldings kenmerken van gebruikers](media/self-service-sign-up-overview/example-enter-user-attributes.png)

De gebruiker voert de informatie in, zet de registratie stroom voort en krijgt toegang tot de resources die ze nodig hebben.

![Voor beeld van de gebruiker die is aangemeld](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>Volgende stappen

 Zie [Self-Service-aanmelding toevoegen aan een app](self-service-sign-up-user-flow.md)voor meer informatie.
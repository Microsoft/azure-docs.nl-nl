---
title: Mogelijkheden van Azure Communication Services UI Framework
titleSuffix: An Azure Communication Services conceptual document
description: Meer informatie over UI Framework-mogelijkheden
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b1aab8b38614249d6b502044b5c4c8170f46b3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492214"
---
# <a name="ui-framework-capabilities"></a>Mogelijkheden van UI-Framework

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Met het gebruikers interface-Framework van Azure Communication Services kunt u communicatie ervaringen bouwen met behulp van een set herbruikbare componenten. Deze onderdelen zijn beschikbaar in twee versies: **basis** onderdelen zijn de meest eenvoudige bouw stenen van uw gebruikers interface-ervaring, terwijl combi Naties van deze basis onderdelen **samengestelde** onderdelen worden genoemd.

## <a name="ui-framework-composite-components"></a>Samengestelde onderdelen UI Framework

| Composite               | Description                                               | Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Groep die samen stelling aanroept | Licht gewicht: spraak-en video-uitgaande oproep ervaring voor Azure Communication Services die wordt aangeroepen met behulp van Fluent-ontwerp assets van de gebruikers interface. Ondersteunt groeps aanroepen met de groeps-ID van Azure Communication Services. De samengestelde functie maakt het mogelijk een-op-een-aanroep te gebruiken door te verwijzen naar een Azure Communication Services-identiteit of een telefoon nummer voor PSTN met behulp van een telefoon nummer dat is aangeschaft via Azure.                                    | React |  |  |
| Groeps-chat samen stelling    | Beleving van licht gewicht voor Azure Communication Services met behulp van Fluent-ontwerp assets van de gebruikers interface. Deze ervaring is gericht op het leveren van een eenvoudige chat-client die verbinding kan maken met Azure Communication Services-threads. Hiermee kunnen gebruikers berichten verzenden en ontvangen berichten met type-indica toren en lees bevestigingen bekijken. Er wordt geschaald van 1:1 om chat scenario's te groeperen. Ondersteunt één chat thread.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Basis onderdelen van UI Framework

| Onderdeel             | Beschrijving                                                                                                                                                                                                                                                                        | Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Aanroepende provider    | Kern initialisatie van het onderdeel voor aanroepen. Er is een vereist onderdeel nodig om andere onderdelen boven op de server te initialiseren. Hiermee wordt de kern logica verwerkt voor het initialiseren van de aanroepende client met behulp van toegangs tokens van Azure Communication Services. Ondersteunt groeps deelname. | React | N.v.t.     | N.v.t. |
| Media besturings elementen   | Hiermee kunnen gebruikers de huidige oproep beheren door te scha kelen op dempen, video aan/uit te scha kelen en de oproep te beëindigen.                                                                                                                                                              | React | N.v.t.     | N.v.t. |
| Media galerie   | Alle deel nemers aan de oproep tonen in één galerie. Galerie ondersteunt zowel video ingeschakelde als statische deel nemers. Voor video-ingeschakelde deel nemers wordt video weer gegeven.                                                                                                                | React | N.v.t.     | N.v.t. |
| Microfoon instellingen | Kies de microfoon die moet worden gebruikt voor aanroepen. Dit besturings element kan voor en worden gebruikt tijdens een aanroep om het microfoon apparaat te selecteren.                                                                                                                                               | React | N.v.t.     | N.v.t. |
| Camera-instellingen     | Kies de camera die moet worden gebruikt voor het aanroepen van de video. Dit besturings element kan vóór en worden gebruikt tijdens een aanroep om het video apparaat te selecteren.                                                                                                                                             | React | N.v.t.     | N.v.t. |
| Apparaatinstellingen     | Hiermee worden microfoon-en camera-instellingen gecombineerd tot één onderdeel                                                                                                 | React | N.v.t.     | N.v.t. |
| Chat provider       | Kern onderdeel voor het initialiseren van chat. Er is een vereist onderdeel nodig om andere onderdelen boven op de server te initialiseren. Hiermee wordt de hoofd logica verwerkt voor het initialiseren van de chat-client met een Azure Communication Services-toegangs token en de thread waaraan deze wordt toegevoegd.                                     | React | N.v.t.     | N.v.t. |
| Vak verzenden          | Invoer onderdeel dat gebruikers in staat stelt berichten te verzenden naar de chat-thread. Invoer ondersteunt tekst, hyper links, emojis en andere Unicode-tekens, inclusief andere alfabetten.                                                                                                                         | React | N.v.t.     | N.v.t. |
| Chat-thread           | Thread onderdeel waarmee de gebruiker zowel ontvangen als verzonden berichten met hun afzender gegevens laat zien. De thread ondersteunt type indicatoren en lees bevestigingen. U kunt door deze threads schuiven om de chat geschiedenis te bekijken.
| Deelnemers lijst      | Alle deel nemers van de oproep-of chat-thread weer geven als een lijst.  | React | N.v.t.     | N.v.t. |

## <a name="ui-framework-capabilities"></a>Mogelijkheden van UI-Framework

| Functie                                                             | Groep die samen stelling aanroept | Groeps-chat samen stelling | Basis onderdelen |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Deel nemen aan Vergader teams                                                  |                         |                      |           
| Live-gebeurtenis koppelen aan teams                                               |                         |                      | 
| VoIP-aanroep voor teams gebruiker starten                                       |                         |                      | 
| Deel nemen aan een team dat deelneemt aan chat                                           |                         |                      |            
| Deelname aan Azure Communication Services-oproep met groeps-id                | ✔                      |                      | ✔
| Een VoIP-oproep starten voor een of meer gebruikers van Azure Communication Services |                         |                      |           
| Een Azure Communication Services-chat-thread toevoegen                    |                         | ✔                   | ✔
| Aanroep dempen/dempen opheffen                                                    | ✔                       |                      | ✔
| Video aan/uit bij oproep                                                | ✔                       |                      | ✔
| Scherm delen                                                      | ✔                       |                      | ✔
| Galerie met deel nemers                                                 | ✔                       |                      | ✔
| Microfoon beheer                                               | ✔                       |                      | ✔
| Camera beheer                                                   | ✔                       |                      | ✔
| Contact opnemen met lobby                                                          |                         |                      | ✔
| Chat bericht verzenden                                                   |                         | ✔                   |            
| Chat bericht ontvangen                                                |                         | ✔                   | ✔
| Typen indicatoren                                                   |                         | ✔                   | ✔
| Lees bevestiging                                                        |                         | ✔                   | ✔
| Deelnemers lijst                                                    |                         |                      | ✔


## <a name="customization-support"></a>Aanpassings ondersteuning

| Onderdeel type            | Thema's     | Layout                                                              | Gegevens modellen |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Samengesteld onderdeel       |     N.v.t.    | N.v.t.                                                                 |     N.v.t.     |
| Basis onderdeel            |     N.v.t.    | De indeling van onderdelen kan worden gewijzigd met externe opmaak         |     N.v.t.     |


## <a name="platform-support"></a>Platformondersteuning

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| UI-SDK | Chrome \* , nieuwe rand | Chrome \* , Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*Houd er rekening mee dat de meest recente versie van Chrome naast de vorige twee releases wordt ondersteund.

\*\*Houd er rekening mee dat Safari-versies 13.1 + worden ondersteund. Uitgaande video voor Safari macOS wordt nog niet ondersteund, maar wordt wel ondersteund op iOS. Het delen van een uitgaand scherm wordt alleen ondersteund op Desktop iOS.

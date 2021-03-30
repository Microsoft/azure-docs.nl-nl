---
title: Beperkende factoren-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: De pagina Risico's voor de Microsoft Threat Modeling Tool mogelijke oplossingen te markeren voor de meeste blootgestelde bedreigingen.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68728034"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Microsoft Threat Modeling Tool oplossingen

De Threat Modeling Tool is een kern element van micro soft Security Development Lifecycle (SDL). Hiermee kunnen software architecten potentiële beveiligings problemen vroegtijdig identificeren en verhelpen, wanneer ze betrekkelijk eenvoudig en rendabel zijn om te kunnen oplossen. Als gevolg hiervan worden de totale ontwikkelings kosten aanzienlijk verminderd. Daarnaast hebben we het hulp programma ontworpen met niet-beveiligings experts in het licht, waardoor het maken van bedreigings modellen eenvoudiger is voor alle ontwikkel aars door duidelijke richt lijnen te bieden voor het creëren en analyseren van bedreigings models.

Ga naar de **[Threat Modeling tool](threat-modeling-tool.md)** om vandaag nog aan de slag te gaan.

## <a name="mitigation-categories"></a>Beperkings Categorieën

De Threat Modeling Tool oplossingen worden gecategoriseerd op basis van het beveiligings kader voor webtoepassingen. dit bestaat uit de volgende onderdelen:

| Categorie | Beschrijving |
| -------- | ----------- |
| **[Controle en logboek registratie](threat-modeling-tool-auditing-and-logging.md)** | Wie heeft wat en wanneer? Controle en logboek registratie verwijzen naar de manier waarop uw toepassing gebeurtenissen met betrekking tot beveiliging vastlegt |
| **[Verificatie](threat-modeling-tool-authentication.md)** | Wat is uw rol? Verificatie is het proces waarbij een entiteit de identiteit van een andere entiteit bewijst, meestal via referenties, zoals een gebruikers naam en wacht woord |
| **[Autorisatie](threat-modeling-tool-authorization.md)** | Wat kunt u doen? Autorisatie is de manier waarop uw toepassing toegangs beheer biedt voor resources en bewerkingen |
| **[Communicatie beveiliging](threat-modeling-tool-communication-security.md)** | Wie is het pratend? Communicatie beveiliging zorgt ervoor dat alle communicatie zo veilig mogelijk kan worden uitgevoerd |
| **[Configuratie beheer](threat-modeling-tool-configuration-management.md)** | Wie voert uw toepassing uit als? Met welke data bases kan er verbinding mee worden gemaakt? Hoe wordt uw toepassing beheerd? Hoe worden deze instellingen beveiligd? Configuratie beheer verwijst naar de manier waarop uw toepassing deze operationele problemen verwerkt |
| **[Cryptografie](threat-modeling-tool-cryptography.md)** | Hoe houdt u geheimen (vertrouwelijkheid) bij? Hoe wilt u uw gegevens of bibliotheken (integriteit) controleren? Hoe geeft u seeding voor wille keurige waarden die cryptografisch sterk moeten zijn? Crypto grafie verwijst naar de manier waarop uw toepassing vertrouwelijkheid en integriteit afdwingt |
| **[Uitzonderings beheer](threat-modeling-tool-exception-management.md)** | Wat doet uw toepassing wanneer een methode aanroep in uw toepassing mislukt? Wat moet u weer geven? Gaat u beschrijvende fout informatie retour neren aan eind gebruikers? Geeft u waardevolle uitzonderings informatie terug naar de oproepende functie? Mislukt de toepassing zonder problemen? |
| **[Invoer validatie](threat-modeling-tool-input-validation.md)** | Hoe weet u dat de invoer die door uw toepassing wordt ontvangen geldig is en veilig is? Invoer validatie verwijst naar de manier waarop uw toepassing invoer filtert, verschoont of weigert vóór verdere verwerking. Overweeg het beperken van invoer via ingangs punten en het coderen van uitvoer via afsluit punten. Vertrouwt u gegevens uit bronnen zoals data bases en bestands shares? |
| **[Gevoelige gegevens](threat-modeling-tool-sensitive-data.md)** | Hoe verwerkt uw toepassing gevoelige gegevens? Met gevoelige gegevens wordt verwezen naar de manier waarop uw toepassing gegevens verwerkt die moeten worden beveiligd in het geheugen, via het netwerk of in permanente archieven |
| **[Sessie beheer](threat-modeling-tool-session-management.md)** | Hoe kan uw toepassing gebruikers sessies afhandelen en beveiligen? Een sessie verwijst naar een reeks gerelateerde interacties tussen een gebruiker en uw webtoepassing |

Zo kunt u het volgende identificeren:

* Waar worden de meest voorkomende fouten gemaakt
* Waar zijn de meest actie bare verbeteringen

Als gevolg hiervan kunt u deze categorieën gebruiken om de beveiliging van uw werk te richten en prioriteiten te stellen. Als u weet dat de meest voorkomende beveiligings problemen optreden in de categorieën invoer validatie, authenticatie en autorisatie, hebt u de volgende stappen. Ga voor meer informatie naar **[deze patent koppeling](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Volgende stappen

Ga naar **[Threat Modeling tool dreigingen](threat-modeling-tool-threats.md)** voor meer informatie over de bedreigings categorieën die het hulp programma gebruikt om mogelijke ontwerp bedreigingen te genereren.
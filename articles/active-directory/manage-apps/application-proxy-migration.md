---
title: Upgrade uitvoeren naar Azure AD-toepassingsproxy | Microsoft Docs
description: Kies welke proxy oplossing het meest geschikt is als u een upgrade uitvoert vanuit micro soft Forefront of Unified Access Gateway.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52cb4480e8b7820d656b8ab66f12486c2062dca5
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443488"
---
# <a name="compare-remote-access-solutions"></a>Oplossingen voor externe toegang vergelijken

Azure Active Directory-toepassingsproxy is een van de twee oplossingen voor externe toegang die micro soft biedt. De andere is Web Application proxy, de on-premises versie. Deze twee oplossingen vervangen eerdere producten die door micro soft worden aangeboden: micro soft Forefront Threat Management Gateway (TMG) en Unified Access Gateway (UAG). Gebruik dit artikel om te begrijpen hoe deze vier oplossingen met elkaar vergelijken. Als u nog steeds de afgeschafte oplossingen voor TMG of UAG gebruikt, gebruikt u dit artikel om de migratie naar een van de toepassings proxy te plannen. 


## <a name="feature-comparison"></a>Vergelijking van functies

Gebruik deze tabel om te begrijpen hoe Threat Management Gateway (TMG), Unified Access Gateway (UAG), Web Application proxy (WAP) en Azure AD-toepassingsproxy (AP) met elkaar kunnen worden vergeleken.

| Functie | TMG | UAG | WAP | AP (beschikbaarheid en partitietolerantie) |
| ------- | --- | --- | --- | --- |
| Verificatie via certificaat | Ja | Ja | - | - |
| Browser-apps selectief publiceren | Ja | Ja | Ja | Ja |
| Vooraf-verificatie en eenmalige aanmelding | Ja | Ja | Ja | Ja | 
| Layer 2/3-firewall | Ja | Ja | - | - |
| Proxy mogelijkheden door sturen | Ja | - | - | - |
| VPN-mogelijkheden | Ja | Ja | - | - |
| Uitgebreide protocol ondersteuning | - | Ja | Ja, als er wordt uitgevoerd over HTTP | Ja, als er wordt uitgevoerd over HTTP of via Extern bureaublad-gateway |
| Fungeert als ADFS-proxy server | - | Ja | Ja | - |
| Eén portal voor toegang tot toepassingen | - | Ja | - | Ja |
| Vertaling antwoord tekst koppeling | Ja | Ja | - | Ja | 
| Verificatie met headers | - | Ja | - | Ja, met PingAccess | 
| Beveiliging in de Cloud schalen | - | - | - | Ja | 
| Voorwaardelijke toegang | - | Ja | - | Ja |
| Er zijn geen onderdelen in de zone gedemilitariseerde (DMZ) | - | - | - | Ja |
| Geen binnenkomende verbindingen | - | - | - | Ja |

Voor de meeste scenario's wordt Azure AD-toepassingsproxy aangeraden als de moderne oplossing. Web Application proxy heeft alleen de voor keur in scenario's waarvoor een proxy server voor AD FS is vereist en u kunt geen aangepaste domeinen gebruiken in Azure Active Directory. 

Azure AD-toepassingsproxy biedt unieke voor delen ten opzichte van soort gelijke producten, waaronder:

- Azure AD uitbreiden naar on-premises resources
   - Beveiliging en beveiliging in de Cloud schalen
   - Functies zoals voorwaardelijke toegang en Multi-Factor Authentication zijn gemakkelijk in te scha kelen
- Geen onderdelen in de zone gedemilitariseerde
- Geen binnenkomende verbindingen vereist
- Eén pagina mijn apps waar uw gebruikers naartoe kunnen gaan voor al hun toepassingen, waaronder Microsoft 365, Azure AD Integrated SaaS-apps en uw on-premises web-apps. 


## <a name="next-steps"></a>Volgende stappen

- [Azure AD-toepassingsproxy gebruiken om veilige externe toegang tot on-premises toepassingen te bieden](application-proxy.md)

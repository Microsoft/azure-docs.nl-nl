---
title: Resources voor het migreren van apps naar Azure Active Directory | Microsoft Docs
description: Bronnen die u helpen bij het migreren van de toegang tot en verificatie van toepassingen naar Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ac7e273ebcc4cf541bd94bf7e7fad05802db8d0b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258300"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Resources voor het migreren van toepassingen naar Azure Active Directory

Bronnen die u helpen bij het migreren van de toegang tot en verificatie van toepassingen naar Azure Active Directory (Azure AD).

| Resource  | Beschrijving  |
|:-----------|:-------------|
|[Uw apps migreren naar Azure AD](https://aka.ms/migrateapps/whitepaper) | Dit technisch document bevat de voor delen van migratie en beschrijft het plannen van migratie in vier duidelijk beschreven fasen: detectie, classificatie, migratie en doorlopend beheer. U wordt begeleid bij het bepalen van het proces en het verbreken van het project in gemakkelijk te gebruiken onderdelen. In het hele document vindt u koppelingen naar belang rijke bronnen die u op weg helpen. |
|[Oplossings handleiding: apps migreren van Active Directory Federation Services (AD FS) naar Azure AD](./migrate-adfs-apps-to-azure.md) | In deze hand leiding vindt u een overzicht van de vier fasen van het plannen en uitvoeren van een toepassings migratie project dat wordt beschreven op een hoger niveau in het White Paper over de migratie. In deze hand leiding leert u hoe u deze fasen toepast op het specifieke doel van het verplaatsen van een toepassing vanuit Azure Directory Federated Services (AD FS) naar Azure AD.|
|[Zelf studie voor ontwikkel aars: AD FS naar Azure AD-toepassings migratie Playbook voor ontwikkel aars](https://aka.ms/adfsplaybook) | Met deze set ASP.NET-code voorbeelden en bijbehorende zelf studies kunt u leren hoe u uw toepassingen die zijn geïntegreerd met Active Directory Federation Services (AD FS), veilig en veilig kunt migreren naar Azure Active Directory (Azure AD). Deze zelf studie is gericht op ontwikkel aars die niet alleen meer willen weten over het configureren van apps op zowel AD FS als Azure AD, maar ook op de hoogte zijn en dat de code basis in dit proces moet worden gewijzigd.|
| [Hulp programma: Active Directory Federation Services-gereedheids script voor migratie](https://aka.ms/migrateapps/adfstools) | Dit is een script dat u kunt uitvoeren op uw AD FS-server (on-premises Active Directory Federation Services) om de gereedheid van apps te bepalen voor migratie naar Azure AD.|
| [Implementatie plan: migreren van AD FS naar wacht woord-hash-synchronisatie](https://aka.ms/ADFSTOPHSDPDownload) | Hashes van wacht woorden van gebruikers worden gesynchroniseerd van on-premises Active Directory naar Azure AD. Hierdoor kan Azure AD gebruikers verifiëren zonder interactie met de on-premises Active Directory.| 
| [Implementatie plan: migratie van AD FS naar Pass Through-verificatie](https://aka.ms/ADFSTOPTADPDownload)|Met Azure AD Pass-Through-verificatie kunnen gebruikers zich aanmelden bij zowel lokale als Cloud toepassingen met hetzelfde wacht woord. Deze functie biedt uw gebruikers een betere ervaring omdat ze een minder wacht woord hebben om te onthouden. Het vermindert ook de IT-helpdesk kosten omdat gebruikers minder waarschijnlijk moeten blijven weten hoe ze zich kunnen aanmelden wanneer ze slechts één wacht woord hoeven te onthouden. Als iemand zich aanmeldt bij Azure AD, worden met deze functie de wachtwoorden rechtstreeks gecontroleerd tegen de on-premises Active Directory.|
| [Implementatie plan: eenmalige aanmelding inschakelen voor een SaaS-app met Azure AD](https://aka.ms/SSODPDownload) | Eenmalige aanmelding (SSO) helpt u bij het openen van alle apps en resources die u nodig hebt om zaken te doen, terwijl u zich slechts één keer aanmeldt met één gebruikers account. Nadat een gebruiker zich heeft aangemeld, kan de gebruiker bijvoorbeeld een tweede keer van Microsoft Office, naar Sales Force, verplaatsen zonder verificatie (bijvoorbeeld door een wacht woord in te voeren). 
| [Implementatie plan: apps uitbreiden naar Azure AD met toepassings proxy](https://aka.ms/AppProxyDPDownload)| Het verlenen van toegang van werk nemers en andere apparaten aan on-premises toepassingen heeft traditioneel betrokken virtuele particuliere netwerken (Vpn's) of gedemilitariseerde zones (Dmz's). Dit is niet alleen ingewikkeld en moeilijk te beveiligen, maar het instellen en beheren ervan is duur. Azure AD-toepassingsproxy maakt het gemakkelijker om on-premises toepassingen te openen. |
| [Implementatieplannen](../fundamentals/active-directory-deployment-plans.md) | Vind meer implementatie plannen voor het implementeren van functies zoals multi-factor Authentication, voorwaardelijke toegang, het inrichten van gebruikers, naadloze SSO, selfservice voor wachtwoord herstel en nog veel meer. |
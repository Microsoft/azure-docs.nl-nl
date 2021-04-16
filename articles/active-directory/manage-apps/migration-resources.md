---
title: Resources voor het migreren van apps naar Azure Active Directory | Microsoft Docs
description: Resources voor het migreren van toegang tot toepassingen en verificatie naar Azure Active Directory (Azure AD).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: 2d01c174bbfa522700773b87737b1e3da2de422e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376644"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Resources voor het migreren van toepassingen naar Azure Active Directory

Resources voor het migreren van toegang tot toepassingen en verificatie naar Azure Active Directory (Azure AD).

| Resource  | Beschrijving  |
|:-----------|:-------------|
|[Uw apps migreren naar Azure AD](https://aka.ms/migrateapps/whitepaper) | In dit whitepaper worden de voordelen van migratie beschreven en wordt beschreven hoe u migratie kunt plannen in vier duidelijk omschreven fasen: detectie, classificatie, migratie en doorlopend beheer. U wordt begeleid bij het nadenken over het proces en het opdelen van uw project in eenvoudig te gebruiken onderdelen. In het hele document vindt u koppelingen naar belangrijke resources die u daarbij helpen. |
|[Oplossingshandleiding: Apps migreren van Active Directory Federation Services (AD FS) naar Azure AD](./migrate-adfs-apps-to-azure.md) | Deze oplossingshandleiding begeleidt u door dezelfde vier fasen van het plannen en uitvoeren van een toepassingsmigratieproject dat op een hoger niveau in het whitepaper over migratie wordt beschreven. In deze handleiding leert u hoe u deze fasen kunt toepassen op het specifieke doel van het verplaatsen van een toepassing van Azure Directory Federated Services (AD FS) naar Azure AD.|
|[Zelfstudie voor ontwikkelaars: AD FS azure AD-playbook voor toepassingsmigratie voor ontwikkelaars](https://aka.ms/adfsplaybook) | Met deze reeks ASP.NET codevoorbeelden en de bijbehorende zelfstudies leert u hoe u uw toepassingen die zijn geïntegreerd met Active Directory Federation Services (AD FS) veilig kunt migreren naar Azure Active Directory (Azure AD). Deze zelfstudie is gericht op ontwikkelaars die niet alleen moeten leren hoe ze apps configureren op zowel AD FS als Azure AD, maar ook bewust zijn van en vertrouwen in wijzigingen die hun codebasis in dit proces vereist.|
| [Hulpprogramma: Active Directory Federation Services migratie gereedheidsscript](https://aka.ms/migrateapps/adfstools) | Dit is een script dat u kunt uitvoeren op uw on-premises Active Directory Federation Services-server (AD FS) om te bepalen of apps gereed zijn voor migratie naar Azure AD.|
| [Implementatieplan: Migreren van AD FS naar wachtwoordhashsynchronisatie](https://aka.ms/ADFSTOPHSDPDownload) | Met wachtwoordhashsynchronisatie worden hashes van gebruikerswachtwoorden gesynchroniseerd van on-premises Active Directory naar Azure AD. Hierdoor kan Azure AD gebruikers verifiëren zonder interactie met de on-premises Active Directory.| 
| [Implementatieplan: Migreren van AD FS naar pass-through-verificatie](https://aka.ms/ADFSTOPTADPDownload)|Met pass-through-verificatie van Azure AD kunnen gebruikers zich met hetzelfde wachtwoord aanmelden bij zowel on-premises als cloudtoepassingen. Deze functie biedt uw gebruikers een betere ervaring omdat ze één wachtwoord minder hebben om te onthouden. Het vermindert ook de kosten van de IT-helpdesk omdat gebruikers minder snel vergeten hoe ze zich kunnen aanmelden wanneer ze maar één wachtwoord hoeven te onthouden. Als iemand zich aanmeldt bij Azure AD, worden met deze functie de wachtwoorden rechtstreeks gecontroleerd tegen de on-premises Active Directory.|
| [Implementatieplan: Een aanmelding voor een SaaS-app inschakelen met Azure AD](https://aka.ms/SSODPDownload) | Eenmalige aanmelding (SSO) helpt u toegang te krijgen tot alle apps en resources die u nodig hebt om zaken te doen, terwijl u zich slechts één keer aanmeldt met één gebruikersaccount. Nadat een gebruiker zich bijvoorbeeld heeft aangemeld, kan de gebruiker van Microsoft Office, naar SalesForce, naar Box gaan zonder zich een tweede keer te authenticeren (bijvoorbeeld een wachtwoord te typen). 
| [Implementatieplan: Apps uitbreiden naar Azure AD met toepassingsproxy](https://aka.ms/AppProxyDPDownload)| Bij het verlenen van toegang van laptops van werknemers en andere apparaten aan on-premises toepassingen waren van oudsher VPN's (virtuele particuliere netwerken) of gedemilitariseerde zones (DMZ's) betrokken. Dit is niet alleen ingewikkeld en moeilijk te beveiligen, maar het instellen en beheren ervan is duur. Azure AD toepassingsproxy het gemakkelijker om toegang te krijgen tot on-premises toepassingen. |
| [Implementatieplannen](../fundamentals/active-directory-deployment-plans.md) | Meer implementatieplannen voor het implementeren van functies zoals Multi-Factor Authentication, voorwaardelijke toegang, het inrichten van gebruikers, naadloze eenmalige aanmelding, selfservice voor wachtwoord opnieuw instellen en meer. |
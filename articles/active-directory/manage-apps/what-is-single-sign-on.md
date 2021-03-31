---
title: Wat is eenmalige aanmelding van Azure?
description: Meer informatie over de werking van eenmalige aanmelding (SSO) met Azure Active Directory. Wanneer u SSO gebruikt, hoeven gebruikers geen wachtwoorden te onthouden voor elke toepassing. Wanneer u SSO gebruikt, kunt u tevens het accountbeheer vereenvoudigen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 36025a5a68e4c3d0de1d6b11a13df656c72745d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99260194"
---
# <a name="what-is-single-sign-on-sso"></a>Wat is eenmalige aanmelding?

Eenmalige aanmelding betekent dat een gebruiker zich niet hoeft aan te melden bij elke toepassing die deze gebruikt. De gebruiker meldt zich eenmaal aan en deze referenties worden ook gebruikt voor andere apps.

Als u een eindgebruiker bent, geeft u waarschijnlijk niet veel om SSO-gegevens. U wilt alleen de apps gebruiken waarmee u productief kunt zijn zonder steeds uw wachtwoord te hoeven invoeren. U kunt uw apps vinden op: https://myapps.microsoft.com.
 
Als u een beheerder of IT-professional bent, leest u verder voor meer informatie over eenmalige aanmelding en de implementatie ervan in Azure.

## <a name="single-sign-on-basics"></a>Basisbeginselen voor eenmalige aanmelding
Eenmalige aanmelding biedt een grote stap vooruit in de manier waarop gebruikers zich aanmelden en toepassingen gebruiken. Verificatiesystemen op basis van eenmalige aanmelding worden vaak 'moderne verificatie' genoemd. Moderne verificatie en eenmalige aanmelding vallen in een computingcategorie die identiteits-en toegangsbeheer (IAM) wordt genoemd. Bekijk deze video als u wilt weten waarmee u eenmalige aanmelding mogelijk maakt.

Basisprincipes van verificatie: De basisprincipes | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Eenmalige aanmelding met webtoepassingen
Webtoepassingen zijn zeer populair. Web-apps worden door verschillende bedrijven gehost en beschikbaar gesteld als een service. Enkele populaire voorbeelden van web-apps zijn Microsoft 365, GitHub en Salesforce, en er zijn er nog duizenden andere. Gebruikers hebben toegang tot web-apps via een webbrowser op hun computer. Met eenmalige aanmelding kunnen gebruikers navigeren tussen de verschillende web-apps zonder dat ze zich meerdere keren hoeven aan te melden.

Bekijk deze twee video's om te zien hoe eenmalige aanmelding bij web-apps werkt.

Basisprincipes van verificatie: Webtoepassingen | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Basisprincipes van verificatie: Eenmalige aanmelding voor web-apps | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Cloud-apps versus on-premises gehoste apps
Hoe u eenmalige aanmelding implementeert, is afhankelijk van de locatie waar de app wordt gehost. Het hosten is van belang vanwege de manier waarop netwerkverkeer wordt doorgestuurd om toegang te krijgen tot de app. Als een app wordt gehost en geopend via uw lokale netwerk, een zogeheten on-premises app, is het niet nodig dat gebruikers toegang hebben tot internet om de app te gebruiken. Als de app ergens anders wordt gehost, een zogeheten in de cloud gehoste app, moeten gebruikers toegang hebben tot internet om de app te kunnen gebruiken.

> [!TIP]
> In de cloud gehoste apps worden ook wel SaaS-apps (Software as a Service) genoemd. 

Eenmalige aanmelding voor in de cloud gehoste apps is eenvoudig. U laat de id-provider weten dat dit wordt gebruikt voor de app. En vervolgens configureert u de app zodanig dat de id-provider wordt vertrouwd. Raadpleeg de [quickstartreeks over toepassingsbeheer](add-application-portal.md) om te zien hoe u Azure AD gebruikt als een id-provider voor een app.

> [!TIP]
> De termen 'cloud' en 'internet' worden vaak door elkaar gebruikt. De reden hiervoor heeft te maken met netwerkdiagrammen. Het is gebruikelijk om grote computernetwerken in een diagram aan te duiden met een cloudvorm, omdat het niet haalbaar is om elk onderdeel te tekenen. Internet is het meest bekende netwerk en daarom is het eenvoudig om de termen door elkaar te gebruiken. Elk computernetwerk kan echter een cloud worden genoemd.

U kunt ook eenmalige aanmelding gebruiken voor on-premises gehoste apps. De technologie waarmee eenmalige aanmelding on-premises wordt uitgevoerd, wordt toepassingsproxy genoemd. Zie [Opties voor eenmalige aanmelding](sso-options.md) voor meer informatie hierover.

## <a name="multiple-identity-providers"></a>Meerdere id-providers
Wanneer u eenmalige aanmelding instelt voor gebruik met meerdere id-providers, wordt dit federatie genoemd. Bekijk deze video om te zien hoe federatie werkt.

Basisprincipes van verificatie: Federatie | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Opties voor eenmalige aanmelding](sso-options.md)

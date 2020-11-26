---
title: LDAP-synchronisatie met Azure Active Directory
description: Architectuur richtlijnen voor het bereiken van LDAP-synchronisatie met Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f34e734b315c7c05ce77f5e168a452fc1c1c547
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168675"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>LDAP-synchronisatie met Azure Active Directory

LDAP (Lightweight Directory Access Protocol) is een directory service protocol dat wordt uitgevoerd op de TCP/IP-stack. Het biedt een mechanisme dat wordt gebruikt om verbinding te maken met Internet directory's, te zoeken en te wijzigen. De LDAP-Directory service is gebaseerd op een client-server model en de functie is om toegang tot een bestaande map in te scha kelen. Veel bedrijven zijn afhankelijk van on-premises LDAP-servers voor het opslaan van gebruikers en groepen voor hun cruciale zakelijke apps. 

Azure Active Directory (Azure AD) kan LDAP-synchronisatie vervangen door Azure AD Connect. Met de Azure AD Connect-synchronisatie service worden alle bewerkingen uitgevoerd met betrekking tot het synchroniseren van identiteits gegevens tussen uw on-premises omgevingen en Azure AD. 

## <a name="use-when"></a>Wanneer gebruiken

U moet identiteits gegevens tussen uw on-premises LDAP v3-directory's en Azure AD synchroniseren. 

![architectuur diagram](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: heeft toegang tot een toepassing die afhankelijk is van het gebruik van een LDAP v3-Directory voor het sorteren van gebruikers en wacht woorden.

* **Webbrowser**: het onderdeel waarmee de gebruiker communiceert om toegang te krijgen tot de externe URL van de toepassing

* **Web-app**: toepassing met afhankelijkheden op LDAP v3-directory's.

* **Azure AD**: Azure AD synchroniseert identiteits gegevens (gebruikers, groepen, wacht woorden) van on-premises LDAP-adres lijsten van de organisatie via Azure AD Connect. 

* **Azure AD Connect**: is een hulp programma voor het verbinden van een on-premises identiteits infrastructuur naar Microsoft Azure AD. De wizard en begeleide ervaringen helpen u bij het implementeren en configureren van vereisten en onderdelen die vereist zijn voor de verbinding. 

* **Aangepaste connector**: met een algemene LDAP-connector kunt u de Azure AD Connect-synchronisatie service integreren met een LDAP v3-server. Het is Azure AD Connect.

* **Active Directory**: Active Directory is een directory service die deel uitmaakt van de meeste Windows Server-besturings systemen. Servers met Active Directory Directory Services worden domein controllers genoemd en kunnen alle gebruikers en computers in een Windows-domein verifiëren en autoriseren.

* **LDAP v3-server**: een LDAP-protocol-compatibele directory waarin zakelijke gebruikers en wacht woorden worden opgeslagen die worden gebruikt voor verificatie van Directory Services.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>LDAP-synchronisatie implementeren met Azure AD

* [Hulp middelen voor de integratie van hybride identiteits mappen](../hybrid/plan-hybrid-identity-design-considerations-tools-comparison.md) 

* [Installatie schema voor Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md) 

* [Overzicht en maken van een LDAP-connector](/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > LDAP-connectors zijn een geavanceerde configuratie waarbij kennis is vereist van Forefront Identity Manager en/of Microsoft Identity Manager. Bij gebruik in productie worden vragen over deze configuratie door [Premier Support](https://support.microsoft.com/premier) of Microsoft Partner Network.


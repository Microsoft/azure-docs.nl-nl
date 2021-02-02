---
title: 'Toepassings beheer: Aanbevolen procedures en aanbevelingen | Microsoft Docs'
description: Lees de aanbevolen procedures en aanbevelingen voor het beheren van toepassingen in Azure Active Directory. Meer informatie over het gebruik van automatische inrichting en het publiceren van on-premises apps met toepassings proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23c688d9b2e118ef29303d435bb83ef02ad36105
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259131"
---
# <a name="application-management-best-practices"></a>Best practices voor toepassingsbeheer

Dit artikel bevat aanbevelingen en aanbevolen procedures voor het beheren van toepassingen in Azure Active Directory (Azure AD), het gebruik van automatische inrichting en publiceren van on-premises apps met toepassings proxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Aanbevelingen voor Cloud-apps en eenmalige aanmelding
| Aanbeveling | Opmerkingen |
| --- | --- |
| De Azure AD-toepassings galerie voor apps controleren  | Azure AD heeft een galerie die duizenden vooraf geïntegreerde toepassingen bevat die zijn ingeschakeld met single sign-on (SSO) voor ondernemingen. Zie de [lijst met zelf studies over SaaS-apps](../saas-apps/tutorial-list.md)voor specifieke instructies voor het instellen van apps.  | 
| Federatieve op SAML gebaseerde SSO gebruiken  | Wanneer een toepassing dit ondersteunt, kunt u gebruikmaken van federatieve, op SAML gebaseerde SSO met Azure AD in plaats van op wacht woord gebaseerde SSO en ADFS.  | 
| SHA-256 gebruiken voor certificaat ondertekening  | Azure AD maakt standaard gebruik van het SHA-256-algoritme voor het ondertekenen van het SAML-antwoord. Gebruik SHA-256 tenzij de toepassing SHA-1 vereist (Zie [Opties voor certificaat ondertekening](certificate-signing-options.md) en [aanmeldings probleem van de toepassing](application-sign-in-problem-application-error.md)).  | 
| Gebruikers toewijzing vereisen  | Standaard hebben gebruikers toegang tot uw bedrijfs toepassingen zonder dat ze aan hen zijn toegewezen. Als de toepassing echter rollen beschikbaar stelt of als u wilt dat de toepassing wordt weer gegeven in mijn apps van een gebruiker, moet u de gebruikers toewijzing vereisen.  | 
| Mijn apps naar uw gebruikers implementeren | [Mijn apps](end-user-experiences.md) op `https://myapps.microsoft.com` is een webgebaseerde portal die gebruikers één vermelding biedt voor hun toegewezen Cloud toepassingen. Als er aanvullende mogelijkheden, zoals groeps beheer en self-service voor het opnieuw instellen van wacht woorden, worden toegevoegd, kunnen gebruikers ze vinden in mijn apps. Zie [implementatie van mijn apps plannen](my-apps-deployment-plan.md).
| Groeps toewijzing gebruiken  | Als u uw abonnement hebt opgenomen, wijst u groepen toe aan een toepassing, zodat u doorlopend toegangs beheer kunt delegeren aan de groeps eigenaar.  | 
| Een proces voor het beheren van certificaten instellen | De maximale levens duur van een handtekening certificaat is drie jaar. Als u storingen wilt voor komen of minimaliseren door het verlopen van een certificaat, gebruikt u rollen en e-mail distributie lijsten om ervoor te zorgen dat certificaat gerelateerde wijzigings meldingen nauw keurig worden bewaakt. |

## <a name="provisioning-recommendations"></a>Aanbevelingen inrichten
| Aanbeveling | Opmerkingen |
| --- | --- |
| Zelf studies gebruiken om inrichting in te stellen met Cloud-apps | Bekijk de [lijst met zelf studies voor SaaS-apps](../saas-apps/tutorial-list.md) voor stapsgewijze instructies voor het configureren van inrichting voor de galerie-app die u wilt toevoegen. |
| Inrichtings Logboeken (preview) gebruiken om de status te controleren | De [inrichtings logboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) geven details over alle acties die worden uitgevoerd door de inrichtings service, inclusief de status van afzonderlijke gebruikers. |
| Een distributie groep toewijzen aan de e-mail voor het inrichtings bericht | Wijs een distributie groep toe aan de instelling e-mail meldingen als u de zicht baarheid van kritieke waarschuwingen die door de inrichtings service worden verzonden, wilt verg Roten. |


## <a name="application-proxy-recommendations"></a>Aanbevelingen voor toepassings proxy
| Aanbeveling | Opmerkingen |
| --- | --- |
| Toepassings proxy gebruiken voor externe toegang tot interne bronnen | Toepassings proxy wordt aanbevolen om externe gebruikers toegang te geven tot interne bronnen en de nood zaak voor een VPN-of reverse proxy te vervangen. Het is niet bedoeld voor het openen van bronnen vanuit het bedrijfs netwerk omdat er latentie kan worden toegevoegd.
| Aangepaste domeinen gebruiken | Aangepaste domeinen instellen voor uw toepassingen (Zie [aangepaste domeinen configureren](application-proxy-configure-custom-domain.md)) zodat url's voor gebruikers en tussen toepassingen binnen of buiten uw netwerk werken. U kunt ook uw huis stijl beheren en uw Url's aanpassen.  Wanneer u aangepaste domein namen gebruikt, moet u een openbaar certificaat van een niet-micro soft-vertrouwde certificerings instantie plannen. Azure-toepassing proxy ondersteunt standaard-, ([Joker](application-proxy-wildcard.md)) of San-certificaten. (Zie [toepassings proxy plannen](application-proxy-deployment-plan.md).) |
| Gebruikers synchroniseren voordat u de toepassings proxy implementeert | Voordat u een toepassings proxy implementeert, synchroniseert u gebruikers identiteiten vanuit een on-premises Directory of maakt u ze rechtstreeks in azure AD. Met Identiteitssynchronisatie kan Azure AD vooraf gebruikers verifiëren voordat ze toegang krijgen tot toepassingen die zijn gepubliceerd met app proxy. Het bevat ook de benodigde informatie over de gebruikers-id voor het uitvoeren van eenmalige aanmelding (SSO). (Zie [toepassings proxy plannen](application-proxy-deployment-plan.md).) |
| Volg onze tips voor hoge Beschik baarheid en taak verdeling | Zie [hoge Beschik baarheid en taak verdeling voor uw Application proxy-connectors en-toepassingen](application-proxy-high-availability-load-balancing.md)voor meer informatie over het verkeer tussen gebruikers, Application proxy-connectors en back-end-app-servers en over het verkrijgen van tips voor het optimaliseren van prestaties en taak verdeling. |
| Meerdere connectors gebruiken | Gebruik twee of meer connectors voor toepassings proxy voor meer tolerantie, Beschik baarheid en schaal (Zie [Application proxy-connectors](application-proxy-connectors.md)). Maak connector groepen en zorg ervoor dat elke connector groep ten minste twee Connect oren heeft (drie connectors is optimaal). |
| Connector servers sluiten op toepassings servers en ervoor zorgen dat ze zich in hetzelfde domein bevinden | Als u de prestaties wilt optimaliseren, zoekt u de connector server in de buurt van de toepassings servers (Zie [overwegingen voor netwerk topologie](application-proxy-network-topology.md)). Daarnaast moeten de server-en Web-toepassings servers deel uitmaken van hetzelfde Active Directory domein, of ze moeten vertrouwende domeinen omvatten. Deze configuratie is vereist voor SSO met geïntegreerde Windows-verificatie (IWA) en Kerberos-beperkte delegering (KCD). Als de servers zich in verschillende domeinen bevinden, moet u op resources gebaseerde overdracht gebruiken voor SSO (Zie [KCD voor eenmalige aanmelding met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Automatische updates voor connectors inschakelen | Schakel automatische updates voor uw connectors in voor de nieuwste functies en oplossingen voor problemen. Micro soft biedt direct ondersteuning voor de nieuwste connector versie en één versie voor. (Zie [versie geschiedenis van toepassings proxy release](application-proxy-release-version-history.md).) |
| Uw on-premises proxy overs Laan | Voor eenvoudiger onderhoud moet u de connector configureren om uw on-premises proxy te omzeilen zodat deze rechtstreeks verbinding maakt met de Azure-Services. (Zie [connectors voor toepassings proxy en proxy servers](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Azure AD-toepassingsproxy gebruiken via Web Application proxy | Gebruik Azure AD-toepassingsproxy voor de meeste on-premises scenario's. Web Application proxy heeft alleen de voor keur in scenario's waarvoor een proxy server voor AD FS is vereist en waar u geen aangepaste domeinen in Azure Active Directory kunt gebruiken. (Zie [migratie van toepassings proxy](application-proxy-migration.md).) |
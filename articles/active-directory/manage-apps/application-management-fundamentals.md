---
title: 'Toepassingsbeheer: Aanbevolen procedures en aanbevelingen | Microsoft Docs'
description: Meer informatie over best practices en aanbevelingen voor het beheren van toepassingen in Azure Active Directory. Meer informatie over het gebruik van automatische inrichting en publicatie van on-premises apps met toepassingsproxy.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9b7f312781fd4f14c5e403ad72e5978f4d01487
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379316"
---
# <a name="application-management-best-practices"></a>Best practices voor toepassingsbeheer

Dit artikel bevat aanbevelingen en aanbevolen procedures voor het beheren van toepassingen in Azure Active Directory (Azure AD), met behulp van automatische inrichting en het publiceren van on-premises apps met toepassingsproxy.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Aanbevelingen voor cloud-apps en een een aanmelding
| Aanbeveling | Opmerkingen |
| --- | --- |
| De Azure AD-toepassingsgalerie voor apps controleren  | Azure AD heeft een galerie met duizenden vooraf geïntegreerde toepassingen die zijn ingeschakeld met eenmalige aanmelding (SSO) voor Enterprise. Zie de lijst met zelfstudies voor [SaaS-apps voor richtlijnen voor app-specifieke instellingen.](../saas-apps/tutorial-list.md)  | 
| Federatief op SAML gebaseerde eenmalige aanmelding gebruiken  | Wanneer een toepassing dit ondersteunt, gebruikt u federatief, op SAML gebaseerde eenmalige aanmelding met Azure AD in plaats van eenmalige aanmelding op basis van een wachtwoord en ADFS.  | 
| SHA-256 gebruiken voor het ondertekenen van certificaten  | Azure AD gebruikt standaard het SHA-256-algoritme om het SAML-antwoord te ondertekenen. Gebruik SHA-256 tenzij voor de toepassing [](certificate-signing-options.md) SHA-1 is vereist (zie Opties voor certificaataantekening en Aanmeldingsprobleem [met toepassing](application-sign-in-problem-application-error.md).)  | 
| Gebruikerstoewijzing vereisen  | Standaard hebben gebruikers toegang tot uw bedrijfstoepassingen zonder aan hen te worden toegewezen. Als in de toepassing echter rollen worden weergegeven of als u wilt dat de toepassing wordt weergegeven op de Mijn apps van een gebruiker, moet u de gebruiker toewijzen.  | 
| Uw Mijn apps implementeren | [Mijn apps](end-user-experiences.md) is een webportal die gebruikers één toegangspunt biedt voor hun toegewezen `https://myapps.microsoft.com` cloudtoepassingen. Als er extra mogelijkheden worden toegevoegd, zoals groepsbeheer en selfservice voor wachtwoord opnieuw instellen, kunnen gebruikers deze vinden in Mijn apps. Zie [Implementatie Mijn apps plannen.](my-apps-deployment-plan.md)
| Groepstoewijzing gebruiken  | Als het is opgenomen in uw abonnement, wijst u groepen toe aan een toepassing, zodat u doorlopend toegangsbeheer kunt delegeren aan de groepseigenaar.  | 
| Een proces opzetten voor het beheren van certificaten | De maximale levensduur van een handtekeningcertificaat is drie jaar. Als u uitval wilt voorkomen of minimaliseren omdat een certificaat verloopt, gebruikt u rollen en e-maildistributielijsten om ervoor te zorgen dat aan certificaten gerelateerde wijzigingsmeldingen nauwkeurig worden bewaakt. |

## <a name="provisioning-recommendations"></a>Aanbevelingen voor inrichting
| Aanbeveling | Opmerkingen |
| --- | --- |
| Zelfstudies gebruiken om inrichting met cloud-apps in te stellen | Raadpleeg de [lijst met zelfstudies voor SaaS-apps](../saas-apps/tutorial-list.md) voor stapsgewijs richtlijnen voor het configureren van de inrichting voor de galerie-app die u wilt toevoegen. |
| Inrichtingslogboeken (preview) gebruiken om de status te controleren | De [inrichtingslogboeken](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) geven details over alle acties die door de inrichtingsservice worden uitgevoerd, inclusief de status voor afzonderlijke gebruikers. |
| Een distributiegroep toewijzen aan de e-mail met de inrichtingsmelding | Als u de zichtbaarheid wilt vergroten van kritieke waarschuwingen die door de inrichtingsservice worden verzonden, wijst u een distributiegroep toe aan de instelling E-mailmeldingen. |


## <a name="application-proxy-recommendations"></a>toepassingsproxy aanbevelingen
| Aanbeveling | Opmerkingen |
| --- | --- |
| Gebruik toepassingsproxy voor externe toegang tot interne resources | toepassingsproxy wordt aanbevolen om externe gebruikers toegang te geven tot interne resources, waardoor een VPN of omgekeerde proxy niet meer nodig is. Het is niet bedoeld voor toegang tot resources vanuit het bedrijfsnetwerk, omdat dit latentie kan toevoegen.
| Aangepaste domeinen gebruiken | Stel aangepaste domeinen in voor [](application-proxy-configure-custom-domain.md)uw toepassingen (zie Aangepaste domeinen configureren) zodat URL's voor gebruikers en tussen toepassingen van binnen of buiten uw netwerk werken. U kunt ook uw huisstijl bepalen en uw URL's aanpassen.  Wanneer u aangepaste domeinnamen gebruikt, moet u een openbaar certificaat van een niet-microsoft vertrouwde certificeringsinstantie verkrijgen. Azure-toepassing proxy ondersteunt standaardcertificaten([jokertekens)](application-proxy-wildcard.md)of SAN-certificaten. (Zie [toepassingsproxy planning](application-proxy-deployment-plan.md).) |
| Gebruikers synchroniseren voordat ze toepassingsproxy | Voordat u de toepassingsproxy implementeert, synchroniseert u gebruikersidentiteiten vanuit een on-premises directory of maakt u deze rechtstreeks in Azure AD. Identiteitssynchronisatie kan Azure AD gebruikers vooraf verifiëren voordat ze toegang verlenen tot gepubliceerde app-proxytoepassingen. Het bevat ook de benodigde gebruikers-id-informatie om eenmalige aanmelding (SSO) uit te voeren. (Zie [toepassingsproxy planning](application-proxy-deployment-plan.md).) |
| Volg onze tips voor hoge beschikbaarheid en taakverdeling | Zie Hoge beschikbaarheid en taakverdeling van uw [toepassingsproxy-connectors](application-proxy-high-availability-load-balancing.md)en -toepassingen voor meer informatie over hoe verkeer stroomt tussen gebruikers, toepassingsproxy-connectors en back-end-app-servers en om tips te krijgen voor het optimaliseren van prestaties en taakverdeling. |
| Meerdere connectors gebruiken | Gebruik twee of meer toepassingsproxy-connectors voor meer tolerantie, beschikbaarheid en schaalbaarheid (zie [toepassingsproxy connectors).](application-proxy-connectors.md) Maak connectorgroepen en zorg ervoor dat elke connectorgroep ten minste twee connectors heeft (drie connectors zijn optimaal). |
| Zoek connectorservers dicht bij toepassingsservers en zorg ervoor dat ze zich in hetzelfde domein | Als u de prestaties wilt optimaliseren, zoekt u de connectorserver fysiek dicht bij de [toepassingsservers (zie Overwegingen voor netwerktopologie).](application-proxy-network-topology.md) De connectorserver en webtoepassingenservers moeten ook deel uitmaken van hetzelfde Active Directory-domein, of ze moeten vertrouwende domeinen overspannen. Deze configuratie is vereist voor eenmalige aanmelding met Geïntegreerde Windows-verificatie (IWA) en Kerberos Constrained Delegation (KCD). Als de servers zich in verschillende domeinen, moet u op resources gebaseerde delegatie gebruiken voor eenmalige aanmelding [(zie KCD](application-proxy-configure-single-sign-on-with-kcd.md)voor eenmalige aanmelding met toepassingsproxy ). |
| Automatische updates voor connectors inschakelen | Schakel automatische updates voor uw connectors in voor de nieuwste functies en oplossingen voor fouten. Microsoft biedt directe ondersteuning voor de nieuwste connectorversie en één eerdere versie. (Zie [toepassingsproxy versiegeschiedenis van de release](application-proxy-release-version-history.md).) |
| Uw on-premises proxy omzeilen | Voor eenvoudiger onderhoud configureert u de connector zodanig dat deze uw on-premises proxy omzeilt, zodat deze rechtstreeks verbinding maakt met de Azure-services. (Zie [toepassingsproxy-connectors en proxyservers](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Azure AD-toepassingsproxy via Web toepassingsproxy | Gebruik Azure AD toepassingsproxy voor de meeste on-premises scenario's. Web toepassingsproxy heeft alleen de voorkeur in scenario's waarvoor een proxyserver voor AD FS is vereist en waarbij u geen aangepaste domeinen in Azure Active Directory. (Zie [toepassingsproxy migratie](application-proxy-migration.md).) |
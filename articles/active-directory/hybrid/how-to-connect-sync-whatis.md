---
title: 'Azure AD Connect synchronisatie: synchronisatie begrijpen en aanpassen | Microsoft Docs'
description: Hierin wordt uitgelegd hoe Azure AD Connect synchronisatie werkt en hoe u deze kunt aanpassen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cea26cb119f64679807bc6c5eaadb41b341e5d5a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89662382"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Azure AD Connect synchroniseren: Synchronisatie begrijpen en aanpassen
De Azure Active Directory Connect Synchronization Services (Azure AD Connect Sync) is een hoofd onderdeel van Azure AD Connect. Het zorgt voor alle bewerkingen die betrekking hebben op het synchroniseren van identiteits gegevens tussen uw on-premises omgeving en Azure AD. Azure AD Connect Sync is de opvolger van DirSync, Azure AD Sync en Forefront Identity Manager met de Azure Active Directory connector geconfigureerd.

Dit onderwerp is het start punt voor **Azure AD Connect synchronisatie** (ook wel **synchronisatie-engine** genoemd) en bevat koppelingen naar alle andere onderwerpen die hieraan zijn gerelateerd. Zie [uw on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md)voor koppelingen naar Azure AD Connect.

De synchronisatie service bestaat uit twee onderdelen: het on-premises **Azure AD Connect synchronisatie** onderdeel en de service zijde in azure AD met de naam **Azure AD Connect synchronisatie service**.

## <a name="azure-ad-connect-sync-topics"></a>Onderwerpen over Azure AD Connect synchronisatie
| Onderwerp | Wat er gebeurt en wanneer te lezen |
| --- | --- |
| **Basis beginselen van Azure AD Connect synchronisatie** | |
| [Informatie over de architectuur](concept-azure-ad-connect-sync-architecture.md) |Voor degenen die geen ervaring hebben met de synchronisatie-engine en informatie willen over de architectuur en de gebruikte voor waarden. |
| [Technische concepten](how-to-connect-sync-technical-concepts.md) |Een korte versie van het architectuur onderwerp en geeft een korte beschrijving van de gebruikte termen. |
| [Topologieën voor Azure AD Connect](plan-connect-topologies.md) |Hierin worden de verschillende topologieën en scenario's beschreven die door de synchronisatie-engine worden ondersteund. |
| **Aangepaste configuratie** | |
| [De installatie wizard opnieuw uit te voeren](how-to-connect-installation-wizard.md) |In dit onderwerp wordt uitgelegd welke opties er beschikbaar zijn wanneer u de installatie wizard van Azure AD Connect opnieuw uitvoert. |
| [Inzicht in declaratieve inrichting](concept-azure-ad-connect-sync-declarative-provisioning.md) |Beschrijft het configuratie model met de naam declaratief inrichten. |
| [Inzicht in verklarende inrichtingsexpressies](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Beschrijft de syntaxis voor de expressie taal die wordt gebruikt in declaratieve inrichting. |
| [Inzicht in de standaardconfiguratie](concept-azure-ad-connect-sync-default-configuration.md) |Beschrijft de out-of-Box-regels en de standaard configuratie. Ook wordt beschreven hoe de regels samen werken voor de out-of-Box-scenario's. |
| [Informatie over gebruikers en contact personen](concept-azure-ad-connect-sync-user-and-contacts.md) |Gaat verder met het vorige onderwerp en beschrijft hoe de configuratie voor gebruikers en contact personen samen werkt, met name in een omgeving met meerdere forests. |
| [Hoe kan ik een wijziging aanbrengen in de standaard configuratie](how-to-connect-sync-change-the-configuration.md) |Helpt u bij het maken van een algemene configuratie wijziging voor kenmerk stromen. |
| [Aanbevolen procedures voor het wijzigen van de standaardconfiguratie](how-to-connect-sync-best-practices-changing-default-configuration.md) |Ondersteunings beperkingen en voor het aanbrengen van wijzigingen aan de out-of-box-configuratie. |
| [Filters configureren](how-to-connect-sync-configure-filtering.md) |Hierin worden de verschillende opties beschreven voor het beperken welke objecten worden gesynchroniseerd met Azure AD en stapsgewijze instructies voor het configureren van deze opties. |
| **Functies en scenario's** | |
| [Onopzettelijke verwijderingen voorkomen](how-to-connect-sync-feature-prevent-accidental-deletes.md) |Beschrijft de functie *onopzettelijk verwijderen voor komen* en hoe deze kan worden geconfigureerd. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Hierin wordt de ingebouwde scheduler beschreven, die gegevens importeert, synchroniseert en exporteert. |
| [Hash-synchronisatie voor wacht woord implementeren](how-to-connect-password-hash-synchronization.md) |Hierin wordt beschreven hoe wachtwoord synchronisatie werkt, hoe u kunt implementeren en hoe u deze kunt gebruiken en oplossen. |
| [Apparaat terugschrijven](how-to-connect-device-writeback.md) |Hierin wordt beschreven hoe write-back van apparaten werkt in Azure AD Connect. |
| [Uitbreidingen van de directory](how-to-connect-sync-feature-directory-extensions.md) |Hierin wordt beschreven hoe u het Azure AD-schema uitbreidt met uw eigen aangepaste kenmerken. |
| [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |Hierin wordt beschreven hoe u de Microsoft 365 resources van de gebruiker in dezelfde regio als de gebruiker plaatst. |
| **Synchronisatie service** | |
| [Functies van de Azure AD Connect-synchronisatieservice](how-to-connect-syncservice-features.md) |Hierin wordt de synchronisatie service naast beschreven en wordt uitgelegd hoe u de synchronisatie-instellingen in azure AD wijzigt. |
| [Tolerantie voor dubbel kenmerk](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |Hierin wordt beschreven hoe u **userPrincipalName** en **proxyAddresses** voor dubbele kenmerk waarden kunt inschakelen en gebruiken. |
| **Bewerkingen en gebruikers interface** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Hierin wordt de Synchronization Service Manager-gebruikers interface beschreven, inclusief [bewerkingen](how-to-connect-sync-service-manager-ui-operations.md), [connectors](how-to-connect-sync-service-manager-ui-connectors.md), de functie voor het [omkeren van Query's](how-to-connect-sync-service-manager-ui-mvdesigner.md)en tabbladen met [omgekeerde Zoek opdrachten](how-to-connect-sync-service-manager-ui-mvsearch.md) . |
| [Operationele taken en overwegingen](./how-to-connect-sync-staging-server.md) |Beschrijft operationele problemen, zoals herstel na nood gevallen. |
| **Procedures...** | |
| [Het Azure AD-account opnieuw instellen](how-to-connect-azureadaccount.md) |Het opnieuw instellen van de referenties van het service account dat wordt gebruikt om verbinding te maken vanuit Azure AD Connect synchroniseren met Azure AD. |
| **Meer informatie en verwijzingen** | |
| [Poorten](reference-connect-ports.md) |Een lijst met poorten die u moet openen tussen de synchronisatie-engine en uw on-premises directory's en Azure AD. |
| [Kenmerken gesynchroniseerd naar Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Een lijst met alle kenmerken die worden gesynchroniseerd tussen on-premises AD en Azure AD. |
| [Naslag informatie over functies](reference-connect-sync-functions-reference.md) |Een lijst met alle functies die beschikbaar zijn in declaratieve inrichting. |

## <a name="additional-resources"></a>Aanvullende resources
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
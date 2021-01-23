---
title: Bepaal uw beveiligings postuur voor externe samen werking met Azure Active Directory
description: Voordat u een beveiligings plan voor externe toegang kunt uitvoeren, moet u bepalen wat u probeert te verkrijgen.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37c27e84f15a01a2d8832baae137518685de59a8
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725438"
---
# <a name="determine-your-security-posture-for-external-access"></a>Uw beveiligings postuur voor externe toegang bepalen 

Als u overweegt om externe toegang te bepalen, moet u de vereisten voor beveiliging en samen werking voor uw organisatie in het algemeen beoordelen en binnen elk scenario. Bekijk op het niveau van de organisatie de hoeveelheid besturings elementen die uw IT-team nodig heeft voor een dagelijkse samen werking. Organisaties in gereguleerde industrieën hebben mogelijk meer IT-beheer nodig. Een verdedigings contractant kan bijvoorbeeld verplicht zijn om elke externe gebruiker, hun toegang en de verwijdering van de toegang te identificeren en te documenteren. Deze vereiste kan zich voordoen op alle toegang of op specifieke scenario's of workloads. Aan het andere uiteinde van het spectrum kan een advies bureau in het algemeen toestaan dat eind gebruikers kunnen bepalen met welke externe gebruikers ze moeten samen werken, binnen bepaalde IT-beveiligings rails. 

![HET versus beheer van de eind gebruiker van de samen werking](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> Een nauw keuriger beheer van samen werking kan leiden tot betere IT-budgetten, gereduceerde productiviteit en vertraagde bedrijfs resultaten. Wanneer officiële samenwerkings kanalen als te verlieslatend worden waargenomen, moeten eind gebruikers de IT-mede werkers de beschikking geven om hun taken uit te voeren, door bijvoorbeeld onbeveiligde documenten te e-mailen.

## <a name="think-in-terms-of-scenarios"></a>Denk aan scenario's

In veel gevallen is het mogelijk om partner toegang, ten minste in sommige scenario's, te delegeren tijdens het bieden van beveiligings rails. Met de IT-beveiligings rails kunt u ervoor zorgen dat intellectueel eigendom veilig blijft, terwijl werk nemers kunnen samen werken met partners om werk te doen.

Wanneer u de scenario's in uw organisatie overweegt, moet u de behoefte aan werk nemers versus toegang tot resources tot bronnen beoordelen. Een bank kan nalevings behoeften hebben die de toegang tot bepaalde bronnen, zoals informatie over gebruikers accounts, beperken tot een kleine groep interne mede werkers. Daarentegen kan dezelfde bank gedelegeerde toegang mogelijk maken voor partners die aan een marketing campagne werken.

![continuüm van governance per scenario](media\secure-external-access\1-scenarios.png)

In elk scenario kunt u overwegen 

* de gevoeligheid van de gegevens die risico lopen

* of u wilt beperken wat partners kunnen zien over andere gebruikers

* de kosten van een schending, vergeleken met het gewicht van gecentraliseerde controle en wrijving op eind gebruikers

 U kunt ook beginnen met centraal beheerde controles om te voldoen aan de compliantie doelen en het beheer te delegeren aan eind gebruikers gedurende een bepaalde periode. Alle modellen voor toegangs beheer kunnen naast elkaar worden gebruikt binnen een organisatie. 

Het gebruik van [beheerde referenties voor partners](../external-identities/what-is-b2b.md) biedt uw organisatie een essentieel signaal dat de toegang tot uw resources beëindigt zodra de externe gebruiker de toegang tot de resources van hun eigen bedrijf heeft verloren.

## <a name="goals-of-securing-external-access"></a>Doel stellingen van het beveiligen van externe toegang

De doel stellingen van IT-onderhevige en gedelegeerde toegang verschillen.

**De belangrijkste doel stellingen van de toegang tot IT zijn:**

* Voldoen aan de governance-, regelgevende en compliance-doelen (GRC). 

* Beheer de toegang van partners nauw keurig en wat partners kunnen zien over gebruikers, groepen en andere partners van leden.

**De belangrijkste doel stellingen voor het delegeren van de toegang zijn:**

* Zakelijke eigen aars kunnen bepalen met wie ze samen werken, binnen de IT-beperkingen.

* Zakelijke partners in staat stellen om toegang aan te vragen op basis van regels die zijn gedefinieerd door zakelijke eigen aren.

Afhankelijk van wat u voor uw organisatie en scenario's hebt, moet u: 

* **Toegang tot toepassingen, gegevens en inhoud beheren**. Dit kan worden bereikt via verschillende methoden, afhankelijk van uw versies van [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) en [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans). 

* **Verminder de kwets** baarheid voor aanvallen. [Privileged Identity Management](../privileged-identity-management/pim-configure.md), [preventie van gegevens verlies (DLP)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) en [versleutelings mogelijkheden](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) verminderen de kwets baarheid.

* **Controleer regel matig de activiteiten en het controle logboek om de naleving te bevestigen**. HET kan toegangs beslissingen van zakelijke eigen aren delegeren via rechten beheer terwijl toegangs beoordelingen een manier bieden om regel matig blijvende toegang te bevestigen. Geautomatiseerde gegevens classificatie met gevoeligheids labels helpt bij het automatiseren van de versleuteling van gevoelige inhoud, waardoor de eind gebruikers van werk nemers gemakkelijk kunnen voldoen.

## <a name="next-steps"></a>Volgende stappen 

Raadpleeg de volgende artikelen over het beveiligen van externe toegang tot bronnen. U wordt aangeraden de acties in de vermelde volg orde uit te voeren.

1. [Bepaal uw beveiligings postuur voor externe toegang](1-secure-access-posture.md) (u bent hier.)

2. [Uw huidige status ontdekken](2-secure-access-current-state.md)

3. [Een governance-plan maken](3-secure-access-plan.md)

4. [Groepen gebruiken voor beveiliging](4-secure-access-groups.md)

5. [Overgang naar Azure AD B2B](5-secure-access-b2b.md)

6. [Veilige toegang met het rechten beheer](6-secure-access-entitlement-managment.md)

7. [Veilige toegang met beleid voor voorwaardelijke toegang](7-secure-access-conditional-access.md)

8. [Veilige toegang met gevoeligheids labels](8-secure-access-sensitivity-labels.md)

9. [Beveiligde toegang tot micro soft teams, OneDrive en share point](9-secure-access-teams-sharepoint.md)
 


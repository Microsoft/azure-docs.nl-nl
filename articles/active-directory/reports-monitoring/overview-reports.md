---
title: Wat zijn Azure Active Directory-rapporten? | Microsoft Docs
description: Biedt een algemeen overzicht van Azure Active Directory-rapporten.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2020
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9a51c10a4f390e5627bccf35ab5dc74689e9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91566819"
---
# <a name="what-are-azure-active-directory-reports"></a>Wat zijn Azure Active Directory-rapporten?

Azure Active Directory-rapporten (Azure AD) bieden een uitgebreid overzicht van de activiteit in uw omgeving. Met de gegevens kunt u het volgende doen:

- Vaststellen hoe uw apps en services door uw gebruikers worden gebruikt
- Potentiële risico's detecteren die invloed hebben op de status van uw omgeving
- Problemen oplossen waardoor uw gebruikers hun werk niet kunnen doen  

De rapportagearchitectuur is afhankelijk van twee belangrijke zaken:

- [Beveiligingsrapporten](#security-reports)
- [Activiteitsrapporten](#activity-reports)

![Rapportage](./media/overview-reports/01.png)


## <a name="security-reports"></a>Beveiligingsrapporten

Met de beveiligingsrapporten kunt u de identiteiten van uw organisatie beschermen. Er zijn twee typen beveiligingsrapporten:

- **Gebruikers voor wie wordt aangegeven dat ze risico lopen**: in het rapport [Gebruikers voor wie wordt aangegeven dat ze risico lopen](../identity-protection/overview-identity-protection.md) krijgt u een overzicht van gebruikersaccounts die mogelijk zijn aangetast.

- **Riskante aanmeldingen**: in het beveiligingsrapport [Riskante aanmeldingen](../identity-protection/overview-identity-protection.md) krijgt u een idee van aanmeldingspogingen die mogelijk zijn uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot een beveiligingsrapport?  

Alle edities van Azure AD bieden rapporten over gebruikers voor wie wordt aangegeven dat ze risico lopen en rapporten over riskante aanmeldingen. Het detailniveau van rapporten verschilt wel per editie: 

- In de edities **Azure Active Directory Free en Basic** hebt u toegang tot een lijst die gebruikers bevat voor wie wordt aangegeven dat ze risico lopen, evenals riskante aanmeldingen. 

- De editie **Azure Active Directory Premium 1** bevat een uitgebreider model waarmee u ook bepaalde onderliggende risicodetecties kunt onderzoeken die voor elk rapport zijn gedetecteerd. 

- De editie **Azure Active Directory Premium 2** biedt u de meest gedetailleerde informatie over de onderliggende risicodetecties. Deze editie stelt u ook in staat beveiligingsbeleidsregels te configureren die automatisch op de geconfigureerde risiconiveaus reageren.


## <a name="activity-reports"></a>Activiteitsrapporten

Activiteitenrapporten helpen u het gedrag van gebruikers in uw organisatie te begrijpen. Er zijn in Azure AD twee typen activiteitsrapporten:

- **Audittrails**: het [activiteitenrapport voor audittrails](concept-audit-logs.md) biedt u toegang tot de geschiedenis van elke taak die in uw tenant is uitgevoerd.

- **Aanmeldingen**: met het [activiteitenrapport voor aanmeldingen](concept-sign-ins.md) kunt u bepalen wie de taken heeft uitgevoerd die in het audittrailrapport zijn gerapporteerd.



> [!VIDEO https://www.youtube.com/embed/ACVpH6C_NL8]




### <a name="audit-logs-report"></a>Rapport voor audittrails 

De [audittrailrapporten](concept-audit-logs.md) bieden records van systeemactiviteiten in het kader van naleving. Met deze gegevens kunt u algemene scenario's aanpakken zoals:

- Iemand in mijn tenant heeft toegang gekregen tot een beheerdersgroep. Wie heeft diegene toegang verleend? 

- Ik wil een lijst weergeven met gebruikers die zich in een specifieke app hebben aangemeld sinds ik de app onlangs heb toegevoegd en wil weten of de app het goed doet

- Ik wil weten hoe vaak er in mijn tenant een wachtwoord opnieuw is ingesteld


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot audittrailrapporten?  

Het audittrailrapport is beschikbaar voor functies waarvoor u licenties hebt. Als u een licentie voor een specifieke functie hebt, hebt u ook toegang tot de audittrailgegevens hiervan. U kunt een gedetailleerde functievergelijking van [verschillende typen licenties](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses) bekijken op de pagina [Prijzen voor Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Zie [Functies en mogelijkheden van Azure Active Directory](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad) voor meer informatie.

### <a name="sign-ins-report"></a>Aanmeldingenrapport

Het [aanmeldingenrapport](concept-sign-ins.md) helpt u antwoorden te vinden op vragen zoals:

- Wat is het aanmeldingspatroon van een gebruiker?
- Hoeveel keer hebben gebruikers zich aangemeld gedurende een week?
- Wat is de status van deze aanmeldingen?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot het aanmeldactiviteitenrapport?  

Uw tenant moet beschikken over een Azure AD Premium-licentie om het rapport met aanmeldactiviteiten te kunnen openen.

## <a name="programmatic-access"></a>Toegang op programmeerniveau

De rapportage van Azure AD biedt u naast de gebruikersinterface ook [toegang op programmeerniveau](concept-reporting-api.md) tot de rapportagegegevens via een reeks REST API's. U kunt deze API's vanuit een groot aantal computertalen en hulpprogramma's aanroepen. 

## <a name="next-steps"></a>Volgende stappen

- [Rapport voor riskante aanmeldingen](../identity-protection/overview-identity-protection.md)
- [Rapport voor audittrails](concept-audit-logs.md)
- [Rapport voor aanmeldlogboeken](concept-sign-ins.md)
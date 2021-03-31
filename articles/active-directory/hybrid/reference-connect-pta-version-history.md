---
title: 'Pass-Through-verificatie van Azure AD: release geschiedenis van versie | Microsoft Docs'
description: In dit artikel vindt u een overzicht van alle versies van de Azure AD Pass-Through-verificatie agent
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 854931273ffebd653615a44dbfa9ce1b846655c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91333423"
---
# <a name="azure-ad-pass-through-authentication-agent-version-release-history"></a>Azure AD Pass-Through-verificatie agent: release geschiedenis van versie 
 
De agents die on-premises zijn geïnstalleerd en die Pass Through-verificatie inschakelen, worden regel matig bijgewerkt om nieuwe mogelijkheden te bieden. In dit artikel worden de versies en functies vermeld die worden toegevoegd wanneer er nieuwe functionaliteit wordt geïntroduceerd. Pass Through-verificatie agenten worden automatisch bijgewerkt wanneer er een nieuwe versie wordt uitgebracht. 

Hier vindt u verwante onderwerpen: 

- [Aanmelding van gebruikers met Azure AD Pass-Through-verificatie](how-to-connect-pta.md) 
- [Installatie van Azure AD Pass-Through-verificatie agent](how-to-connect-pta-quick-start.md) 

## <a name="1517420"></a>1.5.1742.0
### <a name="release-status"></a>Release status: 
04/09/2020: uitgebracht voor downloaden

### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen

- Er is ondersteuning toegevoegd voor het richten op Cloud omgevingen tijdens de installatie. De bundel kan worden vastgemaakt aan een bepaalde cloud omgeving.



## <a name="1510070"></a>1.5.1007.0 
### <a name="release-status"></a>Status van de release 
1/22/2019: uitgebracht voor downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- Er is ondersteuning toegevoegd voor Service Bus betrouw bare kanalen om een extra laag met verbindings tolerantie voor uitgaande verbindingen toe te voegen 
- TLS 1,2 afdwingen tijdens de registratie van de agent 

## <a name="156430"></a>1.5.643.0 
### <a name="release-status"></a>Status van de release 
4/10/2018: uitgebracht voor downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- Ondersteuning voor WebSocket-verbinding 
- TLS 1,2 instellen als het standaard protocol voor de agent 
 
## <a name="154050"></a>1.5.405.0 
### <a name="release-status"></a>Status van de release 
1/31/2018: uitgebracht voor downloaden  
### <a name="fixed-issues"></a>Opgeloste problemen 
- Er is een fout opgelost waardoor sommige geheugen lekken in de agent zijn veroorzaakt. 
- De Azure Service Bus versie is bijgewerkt, die een fout oplossing bevat voor problemen met de time-out van de connector. 
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- Er is ondersteuning toegevoegd voor verbindingen op basis van websockets tussen de agent en Azure AD-Services om de verbindings tolerantie te verbeteren

## <a name="154020"></a>1.5.402.0 
### <a name="release-status"></a>Status van de release 
11/25/2017: uitgebracht voor downloaden  
### <a name="fixed-issues"></a>Opgeloste problemen 
- Opgeloste fouten met betrekking tot de DNS-cache voor standaard proxy scenario's 
 
## <a name="153890"></a>1.5.389.0 
### <a name="release-status"></a>Status van de release 
10/17/2017: uitgebracht voor downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- De functionaliteit van de DNS-cache voor uitgaande verbindingen is toegevoegd om tolerantie van DNS-fouten toe te voegen 
 
## <a name="152610"></a>1.5.261.0 
### <a name="release-status"></a>Status van de release 
08/31/2017: uitgebracht voor downloaden  
### <a name="new-features-and-improvements"></a>Nieuwe functies en verbeteringen 
- GA-versie van de Pass-Through-verificatie agent van Azure AD 

## <a name="next-steps"></a>Volgende stappen

- [Gebruikersaanmelding met Pass Through-verificatie in Azure Active Directory](how-to-connect-pta.md)

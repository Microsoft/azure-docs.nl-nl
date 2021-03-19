---
title: MFA vereisen van niet-vertrouwde netwerken-Azure Active Directory
description: Meer informatie over het configureren van een beleid voor voorwaardelijke toegang in Azure Active Directory (Azure AD) voor toegang tot pogingen van niet-vertrouwde netwerken.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c020a9be7683bf045dbcc747dad3cb45058dd7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077673"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>Procedure: MFA vereisen voor toegang vanaf niet-vertrouwde netwerken met voorwaardelijke toegang   

Met Azure Active Directory (Azure AD) kunt u eenmalige aanmelding voor apparaten, apps en services vanaf elke locatie. Uw gebruikers hebben niet alleen toegang tot uw Cloud-apps vanuit het netwerk van uw organisatie, maar ook vanaf een niet-vertrouwde Internet locatie. Een veelvoorkomende best practice voor toegang vanuit niet-vertrouwde netwerken is multi-factor Authentication (MFA) vereist.

In dit artikel vindt u de informatie die u nodig hebt om een beleid voor voorwaardelijke toegang te configureren waarvoor MFA is vereist voor toegang vanuit niet-vertrouwde netwerken. 

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de [basis concepten](overview.md) van voorwaardelijke toegang. 

## <a name="scenario-description"></a>Scenariobeschrijving

Om het evenwicht tussen beveiliging en productiviteit mogelijk te maken, kan het voldoende zijn om alleen een wacht woord voor aanmeldingen vanuit het netwerk van uw organisatie in te stellen. Voor toegang vanaf een niet-vertrouwde netwerk locatie is er echter een verhoogd risico dat aanmeldingen niet worden uitgevoerd door legitieme gebruikers. Om dit probleem op te lossen, kunt u de toegang tot niet-vertrouwde netwerken blok keren. U kunt ook multi-factor Authentication (MFA) vereisen om extra zekerheid te krijgen dat er een poging is gedaan door de rechtmatige eigenaar van het account. 

Met voorwaardelijke toegang van Azure AD kunt u deze vereiste aanpakken met één beleid dat toegang verleent: 

- Naar geselecteerde Cloud-apps
- Voor geselecteerde gebruikers en groepen  
- Multi-factor Authentication vereisen 
- Wanneer de toegang afkomstig is: 
   - Een locatie die niet wordt vertrouwd

## <a name="implementation"></a>Implementatie

De uitdaging van dit scenario is het vertalen *van de toegang vanaf een niet-vertrouwde netwerk locatie* naar een voorwaardelijke toegangs voorwaarde. In een beleid voor voorwaardelijke toegang kunt u de [locatie voorwaarde](location-condition.md) configureren voor het oplossen van scenario's die betrekking hebben op netwerk locaties. Met de voor waarde voor locaties kunt u benoemde locaties selecteren. Dit zijn logische groeperingen van IP-adresbereiken, landen en regio's.  

Normaal gesp roken bezit uw organisatie een of meer adresbereiken, bijvoorbeeld 199.30.16.0-199.30.16.15.
U kunt een benoemde locatie als volgt configureren:

- Dit bereik opgeven (199.30.16.0/28) 
- Een beschrijvende naam toewijzen, zoals **bedrijfs netwerk** 

In plaats van te bepalen welke locaties niet worden vertrouwd, kunt u het volgende doen:

- Een wille keurige locatie toevoegen 

   :::image type="content" source="./media/untrusted-networks/02.png" alt-text="Scherm opname van het deel venster met een D-locatie van Azure, waarvoor configureren is ingesteld op Ja, het tabblad includes en de optie alle locaties geselecteerd en gemarkeerd." border="false":::

- Alle vertrouwde locaties uitsluiten 

   :::image type="content" source="./media/untrusted-networks/01.png" alt-text="Scherm opname van het deel venster van Azure A D-locaties, met configureren ingesteld op Ja, het tabblad uitsluiten zichtbaar en de optie alle vertrouwde locaties geselecteerd." border="false":::

## <a name="policy-deployment"></a>Implementatie van beleid

Met de aanpak die in dit artikel wordt beschreven, kunt u nu een beleid voor voorwaardelijke toegang configureren voor niet-vertrouwde locaties. Om ervoor te zorgen dat uw beleid werkt zoals verwacht, is het aanbevolen best practice het te testen voordat u het in productie neemt. In het ideale geval gebruikt u een test Tenant om te controleren of uw nieuwe beleid werkt zoals bedoeld.

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is voorwaardelijke toegang in azure Active Directory?](./overview.md) voor meer informatie over voorwaardelijke toegang.

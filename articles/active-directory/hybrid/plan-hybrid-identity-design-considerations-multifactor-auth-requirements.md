---
title: Hybride identiteits ontwerp-vereisten voor multi-factor Authentication Azure | Microsoft Docs
description: Met voorwaardelijk toegangs beheer verifieert Azure AD de specifieke voor waarden die u kiest bij het verifiëren van de gebruiker en voordat toegang tot de toepassing wordt toegestaan.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d8ddf372e234bab242e4b28ba53dce7dd68cc89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90976050"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Multi-factor Authentication-vereisten voor uw hybride identiteits oplossing bepalen
In deze wereld van mobiliteit, met gebruikers die toegang hebben tot gegevens en toepassingen in de Cloud en vanaf elk apparaat, is het beveiligen van deze gegevens het grootste geworden.  Elke dag is een nieuwe kop over een inbreuk op de beveiliging.  Hoewel er geen garantie is tegen dergelijke inbreuken, biedt multi-factor Authentication een extra beveiligingslaag om deze inbreuken te voor komen.
Begin met het evalueren van de vereisten van organisaties voor multi-factor Authentication. Dat wil zeggen dat de organisatie probeert te beveiligen.  Deze evaluatie is belang rijk voor het definiëren van de technische vereisten voor het instellen en inschakelen van de organisaties gebruikers voor multi-factor Authentication.

Zorg ervoor dat u het volgende beantwoordt:

* Probeert uw bedrijf micro soft-apps te beveiligen? 
* Hoe worden deze apps gepubliceerd?
* Biedt uw bedrijf externe toegang zodat werk nemers toegang kunnen krijgen tot on-premises apps?

Zo ja, welk type externe toegang? U moet ook evalueren waar de gebruikers die toegang tot deze toepassingen hebben, zich bevinden. Deze evaluatie is een andere belang rijke stap om de juiste multi-factor Authentication-strategie te definiëren. Zorg ervoor dat u de volgende vragen beantwoordt:

* Waar bevinden de gebruikers zich?
* Kunnen ze overal worden gevonden?
* Wil uw bedrijf beperkingen instellen op basis van de locatie van de gebruiker?

Wanneer u deze vereisten begrijpt, is het belang rijk dat u ook de vereisten van de gebruiker voor multi-factor Authentication evalueert. Deze evaluatie is belang rijk omdat hiermee de vereisten voor het implementeren van multi-factor Authentication worden gedefinieerd. Zorg ervoor dat u de volgende vragen beantwoordt:

* Zijn de gebruikers bekend met multi-factor Authentication?
* Moeten sommige toepassingen worden gebruikt om aanvullende verificatie te bieden?  
  * Zo ja, altijd, wanneer ze afkomstig zijn uit externe netwerken of toegang krijgen tot specifieke toepassingen, of onder andere omstandigheden?
* Moeten de gebruikers training hebben over het instellen en implementeren van multi-factor Authentication?
* Wat zijn de belangrijkste scenario's waarin uw bedrijf multi-factor Authentication voor hun gebruikers wil inschakelen?

Nadat u de voor gaande vragen hebt beantwoord, kunt u nagaan of er lokaal multi-factor Authentication is geïmplementeerd. Deze evaluatie is belang rijk voor het definiëren van de technische vereisten voor het instellen en inschakelen van de organisaties gebruikers voor multi-factor Authentication. Zorg ervoor dat u de volgende vragen beantwoordt:

* Moet uw bedrijf bevoorrechte accounts beveiligen met MFA?
* Moet uw bedrijf MFA inschakelen voor bepaalde toepassingen voor nalevings redenen?
* Moet uw bedrijf MFA inschakelen voor alle in aanmerking komende gebruikers van deze toepassing of alleen voor beheerders?
* Moet MFA altijd zijn ingeschakeld of alleen als de gebruikers worden geregistreerd buiten uw bedrijfs netwerk?

## <a name="next-steps"></a>Volgende stappen
[Een strategie voor het implementeren van een hybride identiteit definiëren](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Zie ook
[Overzicht van ontwerp overwegingen](plan-hybrid-identity-design-considerations-overview.md)


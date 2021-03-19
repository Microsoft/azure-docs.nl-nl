---
title: "Oplossingen voor de fout ' u kunt hier geen toegang krijgen ': Azure AD"
description: Zoek mogelijke oplossingen voor de reden waarom u het fout bericht ' u kunt geen van de hier geen toegang krijgen ' krijgt.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: df3941c895ce67862eb53b8e96bc7a6d53c1ed02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799414"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Mogelijke oplossingen voor het fout bericht ' u kunt geen van deze hier ophalen '

Bij het openen van de interne web-apps of-services van uw organisatie wordt mogelijk een fout bericht weer gegeven waarin wordt vermeld dat **u hier niet kunt komen**. Dit bericht betekent dat uw organisatie een beleid heeft ingesteld dat voor komt dat uw apparaat toegang heeft tot de resources van uw organisatie. Hoewel het mogelijk is om contact op te nemen met de Help Desk om dit probleem op te lossen, kunt u het beste het eerst proberen.

## <a name="make-sure-youre-using-a-supported-browser"></a>Zorg ervoor dat u een ondersteunde browser gebruikt
Als u het **volgende** bericht krijgt dat u de sites van uw organisatie probeert te openen vanuit een niet-ondersteunde browser, controleert u de browser die u gebruikt.

![Fout bericht met betrekking tot browser ondersteuning](media/user-help-device-remediation/browser-version.png)

Om dit probleem op te lossen, moet u een ondersteunde browser installeren en uitvoeren, op basis van uw besturings systeem. Als u met Windows 10 werkt, bevatten de ondersteunde browsers micro soft Edge, Internet Explorer en Google Chrome. Als u een ander besturings systeem gebruikt, kunt u de volledige lijst met [ondersteunde browsers](../conditional-access/concept-conditional-access-conditions.md#supported-browsers)controleren.

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Zorg ervoor dat u een ondersteund besturings systeem gebruikt
Zorg ervoor dat u een ondersteunde versie van het besturings systeem gebruikt, met inbegrip van:

- **Windows-client.** Windows 7 of hoger.

- **Windows-Server.** Windows Server 2008 R2 of hoger.

- **macOS.** macOS X of hoger

- **Android en iOS.** Nieuwste versie van Android-en iOS Mobile-besturings systemen

U kunt dit probleem oplossen door een ondersteund besturings systeem te installeren en uit te voeren.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Controleer of het apparaat is gekoppeld aan uw netwerk
Als u het volgende bericht krijgt dat het apparaat **niet** aan de eisen van het toegangs beleid van uw organisatie voldoet, moet u ervoor zorgen dat uw apparaat is verbonden met het netwerk van uw organisatie.

![Fout bericht met betrekking tot de vraag of u zich in het netwerk bevindt](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Controleren of het apparaat is gekoppeld aan uw netwerk
1. Meld u aan bij Windows met uw werk-of school account. Bijvoorbeeld alain@contoso.com.

2. Maak verbinding met het netwerk van uw organisatie via een virtueel particulier netwerk (VPN) of DirectAccess.

3. Nadat u verbinding hebt gemaakt, drukt u op de **Windows-logo toets + L** om uw apparaat te vergren delen.

4. Ontgrendel uw apparaat met behulp van uw werk-of school account en probeer opnieuw toegang te krijgen tot de problematische app of service.

    Als u het fout bericht **u kunt niet meer ophalen** ziet, selecteert u de koppeling **meer details** en neemt u contact op met de Help Desk voor meer informatie.

### <a name="to-join-your-device-to-your-network"></a>Uw apparaat toevoegen aan uw netwerk
Als uw apparaat niet is toegevoegd aan het netwerk van uw organisatie, kunt u een van de volgende twee dingen doen:

- **Word lid van uw werk apparaat.** Voeg uw werkend Windows 10-apparaat toe aan het netwerk van uw organisatie, zodat u mogelijk beperkte bronnen kunt openen. Zie [uw werk apparaat lid maken van het netwerk van uw organisatie](user-help-join-device-on-network.md)voor meer informatie en stapsgewijze instructies.

- **Registreer uw persoonlijke apparaat voor werk.** Registreer uw persoonlijke apparaat, meestal een telefoon of Tablet, op het netwerk van uw organisatie. Nadat het apparaat is geregistreerd, kan het toegang krijgen tot de beperkte bronnen van uw organisatie. Zie [uw persoonlijke apparaat registreren op het netwerk van uw organisatie](user-help-register-device-on-network.md)voor meer informatie en stapsgewijze instructies.

## <a name="next-steps"></a>Volgende stappen
- [Wat is de MyApps-Portal?](./my-apps-portal-end-user-access.md)

- [Aanmelden met uw telefoon, niet met uw wachtwoord](user-help-auth-app-sign-in.md)
---
title: Bekijk en zoek uw recente aanmeldings activiteit op de pagina Mijn aanmeldingen-Azure Active Directory | Microsoft Docs
description: Meer informatie over het weer geven en doorzoeken van uw recente aanmeldings activiteit vanaf de pagina Mijn aanmeldingen van de portal mijn account.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: rhicock
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.openlocfilehash: 1062c8dfe416af2d39063bf71f39d52e151e28d3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096069"
---
# <a name="view-and-search-your-recent-sign-in-activity-from-the-my-sign-ins-page"></a>Uw recente aanmeldings activiteit bekijken en doorzoeken vanaf de pagina Mijn aanmeldingen

U kunt al uw recente aanmeldings activiteiten voor werk-of school accounts bekijken op de pagina **mijn aanmeldingen** van de portal **Mijn account** . Door uw aanmeldings geschiedenis te bekijken, kunt u controleren op ongebruikelijke activiteiten door u te helpen bij het volgende:

- Als iemand probeert uw wacht woord te raden.
- Als een aanvaller zich heeft aangemeld bij uw account en vanaf welke locatie.
- Welke apps de aanvaller probeerde te openen.

>[!Note]
> Als er een fout optreedt tijdens het aanmelden met een persoonlijke Microsoft-account, kunt u zich nog steeds aanmelden met de domein naam voor uw organisatie (zoals contoso.com) of de **Tenant-id** van uw organisatie van uw beheerder in een van de volgende url's:
>
>   - https://myaccount.microsoft.com?tenantId=*your_domain_name*
>   - https://myaccount.microsoft.com?tenant=*your_tenant_ID*

## <a name="view-your-recent-sign-in-activity"></a>Uw recente aanmeldingsactiviteiten bekijken

1. Meld u aan bij uw werk-of school account en ga vervolgens naar de https://myaccount.microsoft.com/ pagina.

2. Selecteer **mijn aanmeldingen** in het navigatie deel venster links of selecteer de koppeling **recente activiteit controleren** van het blok **mijn aanmeldingen** .

    ![Pagina Mijn account, met gemarkeerde recente koppelingen met activiteiten](media/my-account-portal/my-account-portal-sign-ins.png)

3. Vouw en Controleer elk van de aanmeldings items en zorg ervoor dat u ze herkent. Als u een aanmeldings item vindt dat niet bekend is, wijzigt u uw wacht woord om het account te beveiligen voor het geval dat het is aangetast.

    ![Pagina recente activiteiten met uitgebreide aanmeldings Details](media/my-account-portal-sign-ins-page/recent-activity.png)

### <a name="if-you-see-a-successful-sign-in"></a>Als een geslaagde aanmelding wordt weer geven

Soms ziet u bij het controleren van uw eigen normale aanmeldings activiteit mogelijk een geslaagde aanmelding bij een onbekende locatie, browser of besturings systeem. Onbekende aanmeldingen kunnen betekenen dat een aanvaller toegang heeft verkregen tot uw account. Als u een activiteit ziet die u niet hebt geautoriseerd, raden we u aan om uw wacht woord onmiddellijk te wijzigen en vervolgens naar [beveiligings gegevens](https://mysignins.microsoft.com/security-info) te gaan om uw beveiligings instellingen bij te werken.

Voordat u opgeeft dat er iets onjuist is, controleert u of er geen onechte positieve is (waarbij het item wel vraagbaar is). We bepalen bijvoorbeeld uw geschatte locatie en kaart op basis van uw IP-adres. Mobiele netwerken zijn met name moeilijk te lokaliseren, omdat ze het verkeer soms via locaties op afstand routeren. Zelfs als u zich aanmeldt met uw mobiele apparaat in de staat Washington, kan de locatie de aanmelding weer geven die afkomstig is van Californië. We raden u ten zeerste aan om de details te controleren behalve alleen de locatie. Zorg ervoor dat het besturings systeem, de browser en de app allemaal zinvol zijn.

### <a name="if-you-see-an-unsuccessful-sign-in"></a>Als er een mislukte aanmelding wordt weer geven

Als u een mislukte aanmelding ziet, kan dit betekenen dat u uw referenties hebt getypt. Het kan ook betekenen dat een aanvaller heeft geprobeerd uw wacht woord te raden. U hoeft uw wacht woord niet te wijzigen, maar we raden u aan om u te registreren voor Azure AD Multi-Factor Authentication (MFA). Met multi-factor Authentication, zelfs als de hacker uw wacht woord verdient, is het niet voldoende om toegang te krijgen tot het account.

![Mislukte aanmeldings tegel](media/my-account-portal-sign-ins-page/unsuccessful.png)

Als er een niet-geslaagde aanmelding wordt weer geven met een opmerking onder **sessie activiteit** met de tekst, `Additional verification failed, invalid code` betekent dit dat uw primaire authenticatie referenties zijn geslaagd, maar multi-factor Authentication is mislukt. Dit kan betekenen dat een aanvaller uw wacht woord correct heeft geraden, maar dat de Challenge voor multi-factor Authentication niet kan worden door gegeven. We raden u aan uw wacht woord nog steeds te wijzigen, want de aanvaller kan dat al hebben gedaan, en ga naar de pagina met [beveiligings gegevens](https://mysignins.microsoft.com/security-info) om uw beveiligings instellingen bij te werken.

## <a name="search-for-specific-sign-in-activity"></a>Zoeken naar specifieke aanmeldings activiteit

U kunt uw recente aanmeldings activiteit doorzoeken op een van de beschik bare gegevens. U kunt bijvoorbeeld zoeken naar uw recente aanmeldings activiteit per besturings systeem, locatie, app, enzovoort.

1. Typ op de pagina **recente activiteit controleren** de informatie waarnaar u wilt zoeken in de **Zoek** balk. Typ bijvoorbeeld `Unsuccessful` om te zoeken naar alle mislukte aanmeld activiteiten die zijn verzameld door de app mijn account.

2. Selecteer de knop **zoeken** om te beginnen met zoeken.

    ![Pagina recente activiteiten, met de gemarkeerde zoek balk, zoek knop en resultaten](media/my-account-portal-sign-ins-page/sign-in-search.png)

### <a name="confirm-unusual-activity"></a>Ongebruikelijke activiteiten bevestigen

Aanmeldingen die als ongebruikelijke activiteiten zijn gemarkeerd, kunnen worden bevestigd in de tegel voor die activiteit op de pagina **mijn aanmeldingen** .

![Ongebruikelijke aanmeldings tegel om te bevestigen dat u de aanmelding hebt of niet hebt geprobeerd](media/my-account-portal-sign-ins-page/this-wasnt-me.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u uw recente aanmeldings activiteit hebt bekeken, kunt u het volgende doen:

- Je [beveiligings gegevens](./security-info-setup-signin.md)weer geven of beheren.

- Uw verbonden [apparaten](my-account-portal-devices-page.md)weer geven of beheren.

- Uw [organisaties](my-account-portal-organizations-page.md)weer geven of beheren.

- Bekijk hoe uw organisatie [gebruikmaakt van uw privacy-gerelateerde gegevens](my-account-portal-privacy-page.md).

- Wijzig de [Portalinstellingen van mijn account](my-account-portal-settings.md)
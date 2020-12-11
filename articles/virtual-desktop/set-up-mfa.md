---
title: Azure multi-factor Authentication instellen voor het virtuele bureau blad van Windows-Azure
description: Azure multi-factor Authentication instellen voor verbeterde beveiliging in Windows virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 00aba5d169a05eab25dcc63ca813955e71d09598
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092377"
---
# <a name="enable-azure-multifactor-authentication-for-windows-virtual-desktop"></a>Azure multi-factor Authentication inschakelen voor virtueel bureau blad van Windows

>[!IMPORTANT]
> Als u deze pagina vanuit de documentatie over virtueel bureau blad (Classic) van Windows bezoekt, keert u [terug naar de Windows-documentatie voor virtueel bureau blad (klassiek)](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) wanneer u klaar bent.

De Windows-client voor Windows Virtual Desktop is een uitstekende optie voor het integreren van virtuele Windows-Bureau bladen met uw lokale computer. Wanneer u echter uw Windows virtueel-bureaublad account in de Windows-client configureert, zijn er bepaalde metingen die u moet uitvoeren om uzelf en uw gebruikers veilig te houden.

Wanneer u zich voor het eerst aanmeldt, vraagt de client om uw gebruikers naam, wacht woord en Azure multi-factor Authentication. Daarna herinnert de client de volgende keer dat u zich aanmeldt uw token van uw Azure Active Directory (AD) Enter prise-toepassing. Wanneer u **mij onthouden** selecteert op de vraag om referenties voor de sessiehost, kunnen uw gebruikers zich aanmelden nadat de client opnieuw is opgestart zonder dat ze hun referenties opnieuw moeten invoeren.

Bij het onthouden van referenties is het handig om ook implementaties op bedrijfs scenario's of persoonlijke apparaten minder veilig te maken. Om uw gebruikers te beschermen, kunt u ervoor zorgen dat de client vaker vraagt om de Azure multi-factor Authentication-referenties. In dit artikel wordt uitgelegd hoe u het beleid voor voorwaardelijke toegang configureert voor virtuele Windows-Bureau bladen om deze instelling in te scha kelen.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende informatie nodig om aan de slag te gaan:

- Wijs gebruikers toe aan een licentie die Azure Active Directory Premium P1 of P2 bevat.
- Een Azure Active Directory groep waaraan uw gebruikers zijn toegewezen als groeps leden.
- Schakel Azure multi-factor Authentication in voor al uw gebruikers. Zie [verificatie in twee stappen vereisen voor een gebruiker](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)voor meer informatie over hoe u dit doet.

> [!NOTE]
> De volgende instelling is ook van toepassing op de [Windows virtueel bureau blad-webclient](https://rdweb.wvd.microsoft.com/arm/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

U kunt als volgt een beleid voor voorwaardelijke toegang maken waarvoor multi-factor Authentication is vereist wanneer u verbinding maakt met een virtueel bureau blad van Windows:

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
2. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
3. Selecteer **Nieuw beleid**.
4. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
5. Onder **Toewijzingen** selecteert u **Gebruikers en groepen**.
6. Onder **insluiten** selecteert u **gebruikers en groepen**  >  **gebruikers en groepen** selecteren > kiest u de groep die u hebt gemaakt in de fase [vereisten](#prerequisites) .
7. Selecteer **Gereed**.
8. Onder **Cloud-apps of acties**  >  , selecteert u **apps selecteren**.
9. Selecteer een van de volgende apps op basis van de versie van het virtuele Windows-bureau blad dat u gebruikt.
   
   - Als u Windows virtueel bureau blad (klassiek) gebruikt, kiest u deze apps:
       
       - **Virtueel bureau blad van Windows** (app-id 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Windows-client voor virtueel bureau blad** (app-id fa4345a4-a730-4230-84a8-7d9651b86739), waarmee u beleid kunt instellen op de webclient
       
        Daarna gaat u verder met stap 11.

   - Als u Windows virtueel bureau blad gebruikt, kiest u deze app in plaats daarvan:
       
       -  **Virtueel bureau blad van Windows** (app-id 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        Daarna gaat u naar stap 10.

   >[!IMPORTANT]
   > Selecteer de app met de naam Windows Virtual Desktop Azure Resource Manager provider (50e95039-B200-4007-bc97-8d5790743a63) niet. Deze app wordt alleen gebruikt voor het ophalen van de gebruikers feed en hoeft geen multi-factor Authentication te hebben.
   > 
   > Als u Windows virtueel bureau blad (klassiek) gebruikt, kunt u dit oplossen door de App-ID 9cdead84-a844-4324-93f2-b2e6bb768d07 toe te voegen aan het beleid als het beleid voor voorwaardelijke toegang alle toegang blokkeert en alleen virtuele Windows-bureau blad-app-Id's uitsluit. Door deze app-ID niet toe te voegen, wordt de feed-detectie van Windows virtueel bureau blad-bronnen (klassiek) geblokkeerd.

10. Ga naar **voor waarden**  >  **client-apps** en selecteer waar u het beleid wilt Toep assen:
    
    - Selecteer **browser** als u wilt dat het beleid wordt toegepast op de webclient.
    - Selecteer **mobiele apps en desktop-clients** als u het beleid wilt Toep assen op andere clients.
    - Schakel beide selectie vakjes in als u het beleid wilt Toep assen op alle clients.
   
    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van de pagina client-apps. De gebruiker heeft het selectie vakje Mobile apps en desktop-clients ingeschakeld.](media/select-apply.png)

11. Wanneer u uw app hebt geselecteerd, kiest u **selecteren** en selecteert u **gereed**.

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname van de pagina Cloud-apps of-acties. De Windows Virtual Desktop-en Windows Virtual Desktop Client-apps worden rood gemarkeerd.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >Als u de App-ID wilt vinden van de app die u wilt selecteren, gaat u naar **bedrijfs toepassingen** en selecteert u **micro soft-toepassingen** in de vervolg keuzelijst toepassings type.

12. Onder **toegangs beheer**  >  **toekennen** selecteert u **toegang verlenen**, **multi-factor Authentication vereisen** en **selecteert** u vervolgens.
13. Onder **toegangs beheer**  >  **sessie** selecteert u **aanmeldings frequentie**, stelt u de waarde in op de gewenste tijd tussen prompts en selecteert u **selecteren**. Als u de waarde bijvoorbeeld instelt op **1** en de eenheid op **uren**, is multi-factor Authentication vereist als een verbinding wordt gestart op een uur na de laatste.
14. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
15. Selecteer **maken** om uw beleid in te scha kelen.

>[!NOTE]
>Wanneer u de webclient gebruikt om u via uw browser aan te melden bij het virtuele bureau blad van Windows, wordt in het logboek de client-App-ID weer geven als a85cf173-4192-42f8-81fa-777a763e6e2c (Windows-client voor virtueel bureau blad). Dit komt doordat de client-app intern is gekoppeld aan de server app-ID waarvoor het beleid voor voorwaardelijke toegang is ingesteld. 

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Meer informatie over de aanmeldings frequentie van gebruikers](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)

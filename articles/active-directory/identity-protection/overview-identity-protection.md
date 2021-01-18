---
title: Wat is Azure Active Directory Identity Protection?
description: Risico's detecteren, oplossen, onderzoeken en analyseren met Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperf-fy21q1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e274d35fde6a3d55c05bcb5a9f22e75a37aa3c6
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955396"
---
# <a name="what-is-identity-protection"></a>Wat is Identity Protection?

Identity Protection is een hulpprogramma waarmee organisaties drie belangrijke taken kunnen uitvoeren:

- Het detecteren en oplossen van risico's op basis van de identiteit automatiseren.
- Risico's onderzoeken met behulp van gegevens in de portal.
- Gegevens van risicodetectie exporteren naar hulpprogramma's van derden voor verdere analyse.

Identity Protection maakt gebruik van de informatie die Microsoft heeft verkregen dankzij zijn positie in organisaties met Azure AD, in de consumentenwereld met Microsoft-accounts en in gaming met Xbox om uw gebruikers te beschermen. Microsoft analyseert 6.500.000.000.000 signalen per dag om bedreigingen te identificeren en klanten ertegen te beveiligen.

De signalen die worden gegenereerd door en worden doorgevoerd naar Identity Protection, kunnen verder worden doorgevoerd naar hulpprogramma's zoals voorwaardelijke toegang om toegangsbeslissingen te maken, of worden teruggevoerd naar een hulpprogramma voor beveiligingsgegevens en gebeurtenisbeheer (SIEM) voor verder onderzoek op basis van het beleid van uw organisatie.

## <a name="why-is-automation-important"></a>Waarom is automatisering belangrijk?

In zijn [blogpost van oktober 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) legde Alex Weinert, die het Identity Security and Protection van Microsoft leidt, uit waarom automatisering zo belangrijk is voor het verwerken van grote volumes gebeurtenissen:

> Elke dag bieden onze machine learning- en heuristische systemen risicoscores voor 18.000.000.000 aanmeldingspogingen voor meer dan 800.000.000 afzonderlijke accounts, waarvan 300.000.000 waarschijnlijk worden uitgevoerd door aanvallers (bijvoorbeeld criminele organisaties, hackers).
>
> Vorig jaar op Ignite sprak ik over de drie meest voorkomende aanvallen op onze identiteitssystemen. Dit zijn recente cijfers voor deze aanvallen
>   
>   - **Herhaalde schendingen**: 4,6 miljard aanvallen gedetecteerd in mei 2018
>   - **Wachtwoordspray**: 350.000 in april 2018
>   - **Phishing**: het is moeilijk om een precies aantal te geven, maar we hebben 23 miljoen risicogebeurtenissen gedetecteerd in maart 2018, waarvan er veel met phishing te maken hebben

## <a name="risk-detection-and-remediation"></a>Risicodetectie en -herstel

Identity Protection identificeert risico's in de volgende classificaties:

| Risicodetectietype | Beschrijving |
| --- | --- |
| Anoniem IP-adres | Aanmelding vanaf een anoniem IP-adres (bijvoorbeeld een Tor-browser, anonymizer-VPN's). |
| Ongewoon traject | Aanmelding vanaf een ongewone locatie op basis van recente aanmeldingen van de gebruiker. |
| Aan malware gekoppeld IP-adres | Aanmelding via een aan malware gekoppeld IP-adres. |
| Onbekende aanmeldingseigenschappen | Aanmelding met eigenschappen die niet recent zijn waargenomen voor de gebruiker. |
| Gelekte aanmeldingsgegevens | Geeft aan dat de geldige referenties van de gebruiker zijn gelekt. |
| Wachtwoordspray | Geeft aan dat meerdere gebruikersnamen worden aangevallen met behulp van veelvoorkomende wachtwoorden op een manier die lijkt op een brute-forceaanval. |
| Azure AD-bedreigingsinformatie | De interne en externe bedreigingsinformatiebronnen van Microsoft hebben een bekend aanvalspatroon geïdentificeerd. |
| Nieuw land | Deze detectie wordt gedetecteerd door [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country). |
| Activiteit vanaf anoniem IP-adres | Deze detectie wordt gedetecteerd door [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses). |
| Verdachte doorstuuractiviteit voor Postvak IN | Deze detectie wordt gedetecteerd door [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding). |

Meer informatie over deze risico's en hoe/wanneer deze worden vastgesteld, vindt u in het artikel [What is risk](concept-identity-protection-risks.md) (Wat is een risico?).

De risicosignalen kunnen herstelpogingen activeren, zoals gebruikers vragen om Azure AD Multi-Factor Authentication uit te voeren, gebruikers vragen hun wachtwoord opnieuw in te stellen met self-service voor wachtwoordherstel of door een account te blokkeren totdat een beheerder actie onderneemt.

## <a name="risk-investigation"></a>Risico-onderzoek

Beheerders kunnen detecties bekijken en zo nodig handmatig actie ondernemen. Er zijn drie belangrijke rapporten die door beheerders worden gebruikt voor onderzoeken in Identity Protection:

- Riskante gebruikers
- Riskante aanmeldingen
- Risicodetectie

Meer informatie vindt u in het artikel [How To: Investigate risk](howto-identity-protection-investigate-risk.md) (Procedure: risico onderzoeken).

### <a name="risk-levels"></a>Risiconiveaus

Met Identity Protection wordt het risico gecategoriseerd in drie niveaus: laag, gemiddeld en hoog. 

Microsoft biedt geen specifieke details over de manier waarop het risico wordt berekend, maar hoe hoger het niveau is, hoe betrouwbaarder het is dat de gebruiker of het aanmelden is gecompromitteerd. Een voorbeeld: één geval van onbekende aanmeldingseigenschappen voor een gebruiker kan minder bedreigend zijn dan gelekte referenties voor een andere gebruiker.

## <a name="exporting-risk-data"></a>Risicogegevens exporteren

Gegevens van Identity Protection kunnen worden geëxporteerd naar andere hulpprogramma's voor archivering en verder onderzoek en samenhang. Met de op Microsoft Graph gebaseerde API's kunnen organisaties deze gegevens verzamelen voor verdere verwerking in een hulpprogramma zoals hun SIEM. Informatie over het openen van de Identity Protection-API vindt u in het artikel [Get started with Azure Active Directory Identity Protection and Microsoft Graph](howto-identity-protection-graph-api.md) (Aan de slag met Azure Active Directory Identity Protection en Microsoft Graph)

Informatie over het integreren van Identity Protection-gegevens met Azure Sentinel vindt u in het artikel [Connect data from Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md) (Gegevens uit Azure AD Identity Protection koppelen).

## <a name="permissions"></a>Machtigingen

Voor toegang tot Identity Protection moeten gebruikers een Beveiligingslezer, Beveiligingsoperator, Beveiligingsbeheerder, Globale lezer of Globale beheerder zijn.

| Rol | Wel | Niet |
| --- | --- | --- |
| Globale beheerder | Volledige toegang tot Identity Protection |   |
| Beveiligingsbeheerder | Volledige toegang tot Identity Protection | Wachtwoord opnieuw instellen voor een gebruiker |
| Beveiligingsoperator | Alle rapporten en de overzichts-blade van Identity Protection bekijken <br><br> Gebruikersrisico's negeren, veilige aanmelding bevestigen, inbreuk bevestigen | Beleid configureren of wijzigen <br><br> Wachtwoord opnieuw instellen voor een gebruiker <br><br> Waarschuwingen configureren |
| Beveiligingslezer | Alle rapporten en de overzichts-blade van Identity Protection bekijken | Beleid configureren of wijzigen <br><br> Wachtwoord opnieuw instellen voor een gebruiker <br><br> Waarschuwingen configureren <br><br> Feedback geven op detecties |

De rol van beveiligingsoperator heeft momenteel geen toegang tot het rapport Riskante aanmeldingen.

Beheerders met voorwaardelijke toegang kunnen ook beleidsregels maken met aanmeldrisico’s als een voorwaarde. Meer informatie vindt u in het artikel [Voorwaardelijke toegang: Conditions](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) (Voorwaardelijke toegang: voorwaarden).

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Mogelijkheid | Details  | Azure AD Free/Microsoft 365-apps | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| Risicobeleid | Beleid voor gebruikersrisico's (via Identity Protection)  | Nee | Nee |Ja | 
| Risicobeleid | Beleid voor aanmeldingsrisico's (via Identity Protection of voorwaardelijke toegang)  | Nee |  Nee |Ja |
| Beveiligingsrapporten | Overzicht |  Nee | Nee |Ja |
| Beveiligingsrapporten | Riskante gebruikers  | Beperkte informatie. Alleen gebruikers met een gemiddeld en hoog risico worden weergegeven. Geen detailslade of risicogeschiedenis. | Beperkte informatie. Alleen gebruikers met een gemiddeld en hoog risico worden weergegeven. Geen detailslade of risicogeschiedenis. | Volledige toegang|
| Beveiligingsrapporten | Riskante aanmeldingen  | Beperkte informatie. Er wordt geen risicodetail of risiconiveau weergegeven. | Beperkte informatie. Er wordt geen risicodetail of risiconiveau weergegeven. | Volledige toegang|
| Beveiligingsrapporten | Risicodetectie   | Nee | Beperkte informatie. Geen detailslade.| Volledige toegang|
| Meldingen | Waarschuwingen bij gebruikers die risico lopen  | Nee | Nee |Ja |
| Meldingen | Wekelijkse samenvatting| Nee | Nee | Ja | 
| | MFA-registratiebeleid | Nee | Nee | Ja |

Meer informatie over deze uitgebreide berichten vindt u in het artikel [How To: Investigate risk](howto-identity-protection-investigate-risk.md#navigating-the-reports) (Procedure: risico onderzoeken).

## <a name="next-steps"></a>Volgende stappen

- [Beveiligingsoverzicht](concept-identity-protection-security-overview.md)

- [What is risk](concept-identity-protection-risks.md) (Wat is een risico?)

- [Policies available to mitigate risks](concept-identity-protection-policies.md) (Beschikbare beleidsregels om risico's te beperken)

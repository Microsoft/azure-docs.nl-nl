---
title: Apparaat-id en bureaubladvirtualisatie - Azure Active Directory
description: Meer informatie over hoe VDI- en Azure AD-apparaatidentiteiten samen kunnen worden gebruikt
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1d78094effe6919587f24c2262612e4fab347d
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575374"
---
# <a name="device-identity-and-desktop-virtualization"></a>Apparaat-id en bureaubladvirtualisatie

Beheerders implementeren doorgaans VDI-platformen (Virtual Desktop Infrastructure) die als host voor Windows-besturingssystemen in hun organisatie worden gebruikt. Beheerders implementeren VDI op:

- Stroomlijn het beheer.
- Kosten verlagen door consolidatie en centralisatie van resources.
- Eindgebruikers mobiliteit bieden en de vrijheid hebben om altijd en overal toegang te krijgen tot virtuele bureaubladen op elk apparaat.

Er zijn twee primaire typen virtuele bureaubladen:

- Permanent
- Niet-permanent

Permanente versies gebruiken een unieke bureaubladafbeelding voor elke gebruiker of een groep gebruikers. Deze unieke bureaubladen kunnen worden aangepast en opgeslagen voor toekomstig gebruik. 

Niet-permanente versies maken gebruik van een verzameling bureaubladen die gebruikers waar nodig kunnen openen. Deze niet-permanente bureaubladen worden teruggezet naar de oorspronkelijke staat. In het geval van Windows huidige<sup>1</sup> gebeurt dit wanneer een virtuele machine een proces voor afsluiten/opnieuw opstarten/opnieuw opstarten/opnieuw opstarten van het besturingssysteem doorstaat. In het geval van Down level<sup>2</sup> van Windows gebeurt dit wanneer een gebruiker zich af meldt.

Er is een toename in niet-permanente VDI-implementaties, omdat extern werk de nieuwe norm blijft. Omdat klanten niet-permanente VDI implementeren, is het belangrijk om ervoor te zorgen dat u het apparaatverloop beheert dat kan worden veroorzaakt door een frequente apparaatregistratie zonder een juiste strategie te hebben voor het beheer van de levenscyclus van apparaten.

> [!IMPORTANT]
> Als u het apparaatverloop niet beheert, kan dit leiden tot een hogere druk op het gebruik van uw tenantquotum en een mogelijk risico op onderbreking van de service als u een tenantquotum hebt. Volg de onderstaande richtlijnen bij het implementeren van niet-permanente VDI-omgevingen om deze situatie te voorkomen.

In dit artikel worden de richtlijnen van Microsoft voor beheerders over ondersteuning voor apparaat-id's en VDI beschreven. Zie het artikel Wat is een apparaat-id? voor meer informatie [over apparaat-id's.](overview.md)

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Voordat u apparaat-id's configureert in Azure AD voor uw VDI-omgeving, moet u zich vertrouwd maken met de ondersteunde scenario's. In de onderstaande tabel ziet u welke inrichtingsscenario's worden ondersteund. Inrichten in deze context impliceert dat een beheerder apparaatidentiteiten op schaal kan configureren zonder tussenkomst van de eindgebruiker.

| Type apparaat-id | Id-infrastructuur | Windows-apparaten | Versie van het VDI-platform | Ondersteund |
| --- | --- | --- | --- | --- |
| Hybride Azure AD-deelname | Federatief<sup>3</sup> | Windows current en Windows down-level | Permanent | Yes |
|   |   | Huidige Windows-versie | Niet-permanent | Ja<sup>5</sup> |
|   |   | Downlevel Windows | Niet-permanent | Ja<sup>6</sup> |
|   | Beheerd<sup>4</sup> | Windows current en Windows down-level | Permanent | Yes |
|   |   | Huidige Windows-versie | Niet-permanent | No |
|   |   | Downlevel Windows | Niet-permanent | Ja<sup>6</sup> |
| Azure AD-deelname | Federatief | Huidige Windows-versie | Permanent | No |
|   |   |   | Niet-permanent | No |
|   | Beheerd | Huidige Windows-versie | Permanent | No |
|   |   |   | Niet-permanent | No |
| Azure AD-geregistreerd | Federatief/beheerd | Windows current/Windows down-level | Permanent/niet-permanent | Niet van toepassing |

<sup>1</sup> **Huidige Windows-apparaten** Windows 10, Windows Server 2016 v1803 of hoger en Windows Server 2019.
<sup>2</sup> **Down level Windows-apparaten** vertegenwoordigen Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2. Zie Ondersteuning voor Windows 7 eindigt voor ondersteuningsinformatie [over Windows 7.](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support) Zie prepare for Windows Server 2008 end of support (Voorbereiden voor Windows Server 2008 einde van ondersteuning) voor ondersteuningsinformatie [over Windows Server 2008 R2.](https://www.microsoft.com/cloud-platform/windows-server-2008)

<sup>3</sup> Een **federatief** identiteitsinfrastructuuromgeving vertegenwoordigt een omgeving met een id-provider zoals AD FS of andere IDP van derden.

<sup>4</sup>  Een infrastructuuromgeving voor beheerde identiteiten vertegenwoordigt een omgeving met Azure AD als de identiteitsprovider die is geïmplementeerd met [wachtwoord-hashsynchronisatie (PHS)](../hybrid/whatis-phs.md) of [pass-through-verificatie (PTA)](../hybrid/how-to-connect-pta.md) met naadloze een [eengemaakte aanmelding.](../hybrid/how-to-connect-sso.md)

<sup>5 Voor</sup> **niet-persistentieondersteuning** voor Windows is aanvullende overweging vereist, zoals hieronder wordt beschreven in de sectie met richtlijnen. Voor dit scenario Windows 10 versie 1803, Windows Server 2019 of Windows Server (Semi-Annual-kanaal) vanaf versie 1803 vereist

<sup>6 Voor</sup> **niet-persistentieondersteuning voor Down-Level windows** is aanvullende overweging vereist, zoals hieronder wordt beschreven in de sectie met richtlijnen.


## <a name="microsofts-guidance"></a>Richtlijnen van Microsoft

Beheerders moeten verwijzen naar de volgende artikelen, op basis van hun identiteitsinfrastructuur, voor meer informatie over het configureren van hybride Azure AD-join.

- [Hybride Azure Active Directory configureren voor federatief omgeving](hybrid-azuread-join-federated-domains.md)
- [Hybride Azure Active Directory configureren voor beheerde omgeving](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Niet-permanente VDI

Bij het implementeren van niet-permanente VDI raadt Microsoft IT-beheerders aan de onderstaande richtlijnen te implementeren. Als u dit niet doet, heeft uw directory veel verouderde hybride Azure AD-apparaten die zijn geregistreerd vanaf uw niet-permanente VDI-platform, wat leidt tot een verhoogde druk op uw tenantquotum en het risico op serviceonderbreking als gevolg van een te hoge tenantquotum.

- Als u vertrouwt op de Hulpprogramma voor systeemvoorbereiding (sysprep.exe) en als u een pre-Windows 10 1809-installatie installatie-installatie gebruikt, moet u ervoor zorgen dat de installatie niet afkomstig is van een apparaat dat al is geregistreerd bij Azure AD als hybride Azure AD-lid.
- Als u vertrouwt op een momentopname van een virtuele machine (VM) om extra VM's te maken, moet u ervoor zorgen dat de momentopname niet afkomstig is van een virtuele machine die al is geregistreerd bij Azure AD als hybride Azure AD-join.
- Maak en gebruik een voorvoegsel voor de weergavenaam (bijvoorbeeld NPVDI-) van de computer die aangeeft dat het bureaublad niet-persistent is op basis van VDI.
- Voor Down-level Windows:
   - Implementeren **autoworkplacejoin /leave** opdracht als onderdeel van het script voor het afschrijven. Deze opdracht moet worden geactiveerd in de context van de gebruiker en moet worden uitgevoerd voordat de gebruiker volledig is afgemeld en er nog steeds netwerkverbinding is.
- Voor Windows actueel in een federatief omgeving (bijvoorbeeld AD FS):
   - Implementeert **dsregcmd /join** als onderdeel van de VM-opstartvolgorde/-volgorde en voordat de gebruiker zich meldt.
   - **Voer** dsregcmd /leave niet uit als onderdeel van het proces voor het afsluiten/opnieuw opstarten van de VM.
- Definieer en implementeert het [proces voor het beheren van verouderde apparaten.](manage-stale-devices.md)
   - Zodra u een strategie hebt om uw niet-permanente hybride Azure AD-apparaten te identificeren (bijvoorbeeld met het voorvoegsel van de weergavenaam van de computer), moet u agressiever zijn bij het ops schonen van deze apparaten om ervoor te zorgen dat uw directory niet wordt gebruikt met veel verouderde apparaten.
   - Voor niet-permanente VDI-implementaties op het huidige en down-niveau van Windows moet u apparaten verwijderen met **een ApproximateLastLogonTimestamp** die ouder is dan 15 dagen.

> [!NOTE]
> Als u niet-permanente VDI gebruikt en u de status apparaatdeel nemen wilt voorkomen, moet u ervoor zorgen dat de volgende registersleutel is ingesteld:  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Zorg ervoor dat u Windows 10 versie 1803 of hoger.  
>
> Roaming van alle gegevens onder het pad `%localappdata%` wordt niet ondersteund. Als u ervoor kiest om inhoud te verplaatsen naar , moet u ervoor zorgen dat de inhoud van de volgende mappen en registersleutels het apparaat nooit onder een `%localappdata%` voorwaarde laat.  Bijvoorbeeld: hulpprogramma's voor profielmigratie moeten de volgende mappen en sleutels overslaan:
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
>


### <a name="persistent-vdi"></a>Permanente VDI

Bij het implementeren van permanente VDI raadt Microsoft IT-beheerders aan de onderstaande richtlijnen te implementeren. Als u dit niet doet, leidt dit tot implementatie- en verificatieproblemen. 

- Als u vertrouwt op de Hulpprogramma voor systeemvoorbereiding (sysprep.exe) en als u een pre-Windows 10 1809-installatieafbeelding gebruikt voor installatie, moet u ervoor zorgen dat de installatie van de installatie niet afkomstig is van een apparaat dat al is geregistreerd bij Azure AD als hybride Azure AD-lid.
- Als u een momentopname van een virtuele machine (VM) gebruikt om extra VM's te maken, moet u ervoor zorgen dat de momentopname niet afkomstig is van een virtuele machine die al is geregistreerd bij Azure AD als Hybride Azure AD-join.

Bovendien raden we u aan om een proces te implementeren voor het [beheren van verouderde apparaten.](manage-stale-devices.md) Dit zorgt ervoor dat uw directory niet wordt verbruikt met veel verouderde apparaten als u uw VM's periodiek opnieuw instart.
 
## <a name="next-steps"></a>Volgende stappen

[Hybride Azure Active Directory voor federatief omgeving configureren](hybrid-azuread-join-federated-domains.md)

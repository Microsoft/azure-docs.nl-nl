---
title: Apparaat-id en bureau blad-virtualisatie-Azure Active Directory
description: Meer informatie over hoe VDI en Azure AD-apparaat-id's samen kunnen worden gebruikt
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
ms.openlocfilehash: cfea22c10d98adf3b8c89491c248bf7a934ba1ed
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798881"
---
# <a name="device-identity-and-desktop-virtualization"></a>Apparaat-id en desktop-virtualisatie

Beheerders implementeren ook VDI-platforms (Virtual Desktop Infrastructure) die als host fungeren voor Windows-besturings systemen in hun organisaties. Beheerders implementeren VDI voor:

- Stroom lijn beheer.
- Verminder de kosten door consolidatie en gecentraliseering van resources.
- Lever eind gebruikers mobiliteit en de vrijheid om op elk gewenst moment toegang te krijgen tot virtuele Bureau bladen, vanaf elke locatie, op elk apparaat.

Er zijn twee primaire typen virtuele Bureau bladen:

- Permanent
- Niet-persistent

Permanente versies gebruiken een unieke bureaublad installatie kopie voor elke gebruiker of een groep gebruikers. Deze unieke Bureau bladen kunnen worden aangepast en opgeslagen voor toekomstig gebruik. 

Voor niet-permanente versies wordt gebruikgemaakt van een verzameling bureau bladen die gebruikers op een naar behoefte gebaseerde toegang hebben. Deze niet-permanente Bureau bladen worden teruggezet naar de oorspronkelijke staat, in het geval van Windows Current<sup>1</sup> dit gebeurt wanneer een virtuele machine wordt uitgevoerd door een proces voor afsluiten/opnieuw opstarten/opnieuw instellen van het besturings systeem en in het geval van Windows down level<sup>2</sup> dit gebeurt wanneer een gebruiker zich afmeldt.

Er is een toename in het gebruik van niet-permanente VDI-implementaties omdat het externe werk blijft de nieuwe norm. Als klanten niet-permanente VDI implementeren, is het belang rijk om ervoor te zorgen dat u het verloop van het apparaat beheert dat kan worden veroorzaakt door frequente apparaatregistratie, zonder dat dit een goede strategie heeft voor het beheer van de levens cyclus van apparaten.

> [!IMPORTANT]
> Als u het apparaat verloop niet beheert, kan dit leiden tot een toename van het gebruik van uw Tenant quota en een potentieel risico op onderbrekingen van de service, als het Tenant quotum is overschreden. Volg de hieronder beschreven instructies bij het implementeren van niet-permanente VDI-omgevingen om deze situatie te voor komen.

In dit artikel worden de richt lijnen van micro soft behandeld voor beheerders met ondersteuning voor de apparaat-id en VDI. Zie het artikel [Wat is een apparaat-id](overview.md)? voor meer informatie over de identiteit van het apparaat.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Voordat u apparaat-id's configureert in azure AD voor uw VDI-omgeving, moet u vertrouwd raken met de ondersteunde scenario's. In de onderstaande tabel ziet u welke inrichtings scenario's worden ondersteund. Het inrichten in deze context impliceert dat een beheerder apparaat-id's op schaal kan configureren zonder dat hiervoor interactie van de eind gebruiker is vereist.

| Type apparaat-id | Id-infrastructuur | Windows-apparaten | VDI-platform versie | Ondersteund |
| --- | --- | --- | --- | --- |
| Hybride Azure AD-deelname | Federatief<sup>3</sup> | Windows huidige en Windows down level | Permanent | Ja |
|   |   | Windows actueel | Niet-persistent | Ja<sup>5</sup> |
|   |   | Downlevel Windows | Niet-persistent | Ja<sup>6</sup> |
|   | Beheerd<sup>4</sup> | Windows huidige en Windows down level | Permanent | Ja |
|   |   | Windows actueel | Niet-persistent | Nee |
|   |   | Downlevel Windows | Niet-persistent | Ja<sup>6</sup> |
| Azure AD-deelname | Federatief | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |
|   | Beheerd | Windows actueel | Permanent | Nee |
|   |   |   | Niet-persistent | Nee |
| Azure AD-geregistreerd | Federatief/beheerd | Windows-huidige/Windows-down level | Persistent/niet-persistent | Niet van toepassing |

<sup>1</sup> **Windows-huidige** apparaten vertegenwoordigen Windows 10, Windows Server 2016 V1803 of hoger en Windows Server 2019.
<sup>2</sup> **Windows-down level-** apparaten vertegenwoordigen windows 7, Windows 8,1, Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2. Zie [ondersteuning voor Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)voor meer informatie over ondersteuning voor Windows 7. Zie voor ondersteunings informatie over Windows Server 2008 R2 voor [bereiding op Windows server 2008 end of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> een **federatieve** infra structuur voor identiteiten is een omgeving met een id-provider, zoals AD FS of een andere IDP van derden.

<sup>4</sup> een **beheerde** identiteits infrastructuur is een omgeving met Azure AD als de ID-provider die is geïmplementeerd met een [PHS (Password Hash Sync)](../hybrid/whatis-phs.md) of [Pass Through-verificatie (PTA)](../hybrid/how-to-connect-pta.md) met [naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md).

<sup>5</sup> de **niet-persistente ondersteuning voor Windows Current** vereist extra aandacht als hieronder beschreven in het gedeelte met instructies. Voor dit scenario is Windows 10 1803, Windows Server 2019 of Windows Server (semi-Annual-kanaal) vereist voor het starten van versie 1803

<sup>6</sup> **niet-persistente ondersteuning voor Windows-lager niveau** vereist extra aandacht als hieronder beschreven in het gedeelte met instructies.


## <a name="microsofts-guidance"></a>Richt lijn van micro soft

Beheerders moeten verwijzen naar de volgende artikelen, op basis van hun identiteits infrastructuur, voor meer informatie over het configureren van hybride Azure AD-deelname.

- [Hybride Azure Active Directory-koppeling configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)
- [Hybride Azure Active Directory-deelname configureren voor een beheerde omgeving](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Niet-permanente VDI

Bij het implementeren van niet-persistente VDI raadt micro soft aan dat IT-beheerders de onderstaande instructies implementeren. Als u dit niet doet, zal uw directory veel verouderde hybride Azure AD-apparaten hebben die zijn geregistreerd op basis van uw niet-permanente VDI-platform, wat resulteert in een verhoogde druk op het quotum van uw Tenant en het risico van een onderbreking van de service vanwege het overschrijden van de Tenant quota.

- Als u afhankelijk bent van het hulp programma voor systeem voorbereiding (sysprep.exe) en als u een installatie kopie van vóór Windows 10 1809 gebruikt, moet u ervoor zorgen dat de installatie kopie niet afkomstig is van een apparaat dat al is geregistreerd bij Azure AD als hybride lid van Azure AD.
- Als u een moment opname van een virtuele machine (VM) vertrouwt om extra Vm's te maken, moet u ervoor zorgen dat de moment opname niet afkomstig is van een VM die al is geregistreerd bij Azure AD als hybride Azure AD-deelname.
- Maak en gebruik een voor voegsel voor de weergave naam (bijvoorbeeld NPVDI-) van de computer die het bureau blad als niet-permanente VDI-basis aanduidt.
- Voor Windows down level:
   - Implementeer **autoworkplacejoin/Leave** opdracht als onderdeel van het afmeldings script. Deze opdracht moet worden geactiveerd in de context van de gebruiker en moet worden uitgevoerd voordat de gebruiker volledig is afgemeld en terwijl er nog verbinding is met het netwerk.
- Voor Windows actueel in een federatieve omgeving (bijvoorbeeld AD FS):
   - Implementeer **dsregcmd/join** als onderdeel van de VM-opstart volgorde.
   - Voer dsregcmd/Leave **niet** uit als onderdeel van het proces voor het afsluiten of opnieuw opstarten van de VM.
- Definieer en implementeer het proces voor het [beheren van verouderde apparaten](manage-stale-devices.md).
   - Zodra u een strategie hebt voor het identificeren van uw niet-permanente hybride Azure AD-apparaten (zoals het voor voegsel computer weergave naam), moet u de opschoning van deze apparaten agressief maken om ervoor te zorgen dat uw Directory niet wordt gebruikt voor het gebruik van veel verouderde apparaten.
   - Voor niet-permanente VDI-implementaties op Windows huidige en lagere niveaus moet u apparaten met **ApproximateLastLogonTimestamp** ouder dan 15 dagen verwijderen.

### <a name="persistent-vdi"></a>Permanente VDI

Bij het implementeren van permanente VDI raadt micro soft aan dat IT-beheerders de onderstaande instructies implementeren. Als u dit niet doet, worden de implementatie-en verificatie problemen veroorzaakt. 

- Als u afhankelijk bent van het hulp programma voor systeem voorbereiding (sysprep.exe) en als u een installatie kopie van vóór Windows 10 1809 gebruikt, moet u ervoor zorgen dat de installatie kopie niet afkomstig is van een apparaat dat al is geregistreerd bij Azure AD als hybride lid van Azure AD.
- Als u een moment opname van een virtuele machine (VM) vertrouwt om extra Vm's te maken, moet u ervoor zorgen dat de moment opname niet afkomstig is van een VM die al is geregistreerd bij Azure AD als hybride Azure AD-deelname.

Daarnaast raden wij u aan om het proces voor het [beheren van verouderde apparaten](manage-stale-devices.md)te implementeren. Dit zorgt ervoor dat uw Directory niet wordt gebruikt met veel verouderde apparaten als u uw Vm's regel matig opnieuw instelt.
 
## <a name="next-steps"></a>Volgende stappen

[Hybride Azure Active Directory-koppeling configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)

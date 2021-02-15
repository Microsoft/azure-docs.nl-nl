---
title: Inleiding tot Active Directory-service accounts | Azure Active Directory
description: Een inleiding tot de typen service accounts in Active Directory en hoe u deze kunt beveiligen.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de24975dadf27293f305611c6ba07522e8aa90
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417183"
---
# <a name="introduction-to-active-directory-service-accounts"></a>Inleiding tot Active Directory-service accounts

Een service heeft een primaire beveiligings identiteit waarmee de toegangs rechten voor lokale en netwerk bronnen worden bepaald. De beveiligings context voor een micro soft Win32-service wordt bepaald door het service account dat wordt gebruikt om de service te starten. Een service account wordt gebruikt voor het volgende:
* een service identificeren en verifiëren
* Er is een service gestart
* toegang tot of uitvoeren van code of toepassing
* een proces starten. 

## <a name="types-of-on-premises-service-accounts"></a>Typen on-premises service accounts

Op basis van uw use-case kunt u een beheerde service account (MSA), een computer account of een gebruikers account gebruiken om een service uit te voeren. Services moeten worden getest om te bevestigen dat ze een beheerd service account kunnen gebruiken. Als dat mogelijk is, moet u er een gebruiken.

### <a name="group-msa-accounts"></a>MSA-accounts groeperen

Gebruik waar mogelijk [groeps-beheerde service accounts](service-accounts-group-managed.md) (gmsa's) voor services die worden uitgevoerd in uw on-premises omgeving. Gmsa's bieden één identiteits oplossing voor een service die wordt uitgevoerd op een server farm of achter een netwerk load balancer. Ze kunnen ook worden gebruikt voor een service die wordt uitgevoerd op één server. [Gmsa's hebben specifieke vereisten waaraan moet worden voldaan](https://docs.microsoft.com/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)

### <a name="standalone-msa-accounts"></a>Zelfstandige MSA-accounts

Als u een gMSA niet kunt gebruiken, gebruikt u een [zelfstandige beheerde service accounts](service-accounts-standalone-managed.md)(sMSA). sMSAs vereist ten minste Windows Server 2008R2. In tegens telling tot Gmsa's voert sMSAs alleen uit op één server. Ze kunnen worden gebruikt voor meerdere services op die server.

### <a name="computer-account"></a>Computer account

Als u geen MSA kunt gebruiken, moet u onderzoeken met behulp van een [computer account](service-accounts-computer.md). Het account LocalSystem is een vooraf gedefinieerd lokaal account met uitgebreide bevoegdheden op de lokale computer en fungeert als de computer identiteit op het netwerk.   
Services die worden uitgevoerd als een LocalSystem-account hebben toegang tot netwerk bronnen met behulp van de referenties van het computer account in de indeling <domain_name>\<computer_name> .

NT AUTHORITY\SYSTEM is de vooraf gedefinieerde naam voor de LocalSystem-account. Het kan worden gebruikt om een service te starten en de beveiligings context voor die service op te geven.

> [!NOTE]
> Wanneer een computer account wordt gebruikt, weet u niet welke service op de computer dat account gebruikt en kan daarom niet controleren welke service wijzigingen aanbrengt. 

### <a name="user-account"></a>Gebruikersaccount

Als u geen MSA kunt gebruiken, moet u onderzoeken met behulp van een [gebruikers account](service-accounts-user-on-premises.md). Gebruikers accounts kunnen een domein gebruikers account of een lokale gebruikers account zijn.

Met een domein gebruikers account kan de service optimaal profiteren van de beveiligings functies van Windows en micro soft Active Directory Domain Services van de service. De-service krijgt de lokale en netwerk toegang die aan het account wordt verleend. Het bevat ook de machtigingen van de groepen waarvan het account lid is. Domein service accounts ondersteunen wederzijdse Kerberos-authenticatie.

Een lokale gebruikers account (naam indeling: ".\UserName") bestaat alleen in de SAM-data base van de hostcomputer. Er is geen gebruikers object in Active Directory Domain Services. Een lokaal account kan niet worden geverifieerd door het domein. Een service die wordt uitgevoerd in de beveiligings context van een lokaal gebruikers account heeft dus geen toegang tot netwerk bronnen (behalve als een anonieme gebruiker). Services die worden uitgevoerd in de lokale gebruikers context, bieden geen ondersteuning voor wederzijdse Kerberos-authenticatie waarbij de service wordt geverifieerd door de clients. Om die reden zijn lokale gebruikers accounts doorgaans niet geschikt voor Directory-Services.

> [!IMPORTANT]
> Service accounts mogen geen lid zijn van een geprivilegieerde groep, omdat machtigingen voor groepslid maatschappen een beveiligings risico vormen. Elke service moet een eigen service account hebben voor controle-en beveiligings doeleinden.

## <a name="choose-the-right-type-of-service-account"></a>Het juiste type service account kiezen


| Criteria| gMSA| sMSA| Computer account| Gebruikersaccount |
| - | - | - | - | - |
| App wordt uitgevoerd op één server| Ja| Ja. Gebruik, indien mogelijk, een gMSA| Ja. Gebruik, indien mogelijk, een MSA| Ja. Gebruik MSA als dat mogelijk is. |
| App wordt uitgevoerd op meerdere servers| Ja| Nee| Nee. Het account is gekoppeld aan de server| Ja. Gebruik MSA als dat mogelijk is. |
| App wordt uitgevoerd achter load balancers| Ja| Nee| Nee| Ja. Alleen gebruiken als u geen gMSA kunt gebruiken |
| App wordt uitgevoerd op Windows Server 2008 R2| Nee| Ja| Ja. Gebruik MSA als dat mogelijk is.| Ja. Gebruik MSA als dat mogelijk is. |
| Wordt uitgevoerd op Windows Server 2012| Ja| Ja. GMSA indien mogelijk gebruiken| Ja. Indien mogelijk MSA gebruiken| Ja. Gebruik MSA als dat mogelijk is. |
| Vereiste om service account te beperken tot één server| Nee| Ja| Ja. SMSA indien mogelijk gebruiken| Nee. |


 

### <a name="use-server-logs-and-powershell-to-investigate"></a>Server logboeken en Power shell gebruiken om te onderzoeken

U kunt Server Logboeken gebruiken om te bepalen welke servers en op hoeveel servers een toepassing wordt uitgevoerd.

U kunt de volgende Power shell-opdracht uitvoeren om een lijst op te halen van de Windows Server-versie voor alle servers in uw netwerk. 

```PowerShell

Get-ADComputer -Filter 'operatingsystem -like "*server*" -and enabled -eq "true"' `

-Properties Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

sort-Object -Property Operatingsystem |

Select-Object -Property Name,Operatingsystem,OperatingSystemVersion,IPv4Address |

Out-GridView

```

## <a name="find-on-premises-service-accounts"></a>On-premises service accounts zoeken

We raden u aan om een voor voegsel zoals ' SVC ' toe te voegen. Alle accounts die worden gebruikt als service accounts. Deze naamgevings Conventie maakt het gemakkelijker om ze te vinden en te beheren. Denk ook na over het gebruik van een beschrijvings kenmerk voor het service account en de eigenaar van het service account. Dit kan een team alias of eigenaar van een beveiligings team zijn.

Het vinden van on-premises service accounts is essentieel om de beveiliging te garanderen. En het kan lastig zijn voor niet-MSA-accounts. U wordt aangeraden alle accounts te controleren die toegang hebben tot uw belang rijke on-premises resources en te bepalen welke computer-of gebruikers accounts kunnen fungeren als service accounts. U kunt ook de volgende methoden gebruiken om accounts te vinden.

* De artikelen voor elk type account bevatten gedetailleerde stappen voor het vinden van het account type. Zie de sectie volgende stappen van dit artikel voor koppelingen naar deze artikelen.

## <a name="document-service-accounts"></a>Service accounts documenteren

Wanneer u de service accounts in uw on-premises omgeving hebt gevonden, documenteert u de volgende informatie over elk account. 

* De eigenaar. De persoon die verantwoordelijk is voor het onderhouden van het account.

* Het doel. De toepassing die het account vertegenwoordigt of een ander doel. 

* Machtigings bereik. Welke machtigingen heeft dit en hoe moet het? Wat gebeurt er als de groepen lid zijn van de groep?

* Risico profiel. Wat is het risico voor uw bedrijf als dit account is aangetast? Als u een hoog risico hebt, gebruikt u MSA.

* Verwachte levens duur en periodieke Attestation. Hoe lang verwacht u dat dit account Live is? Hoe vaak moet de eigenaar de beoordeling controleren en moet er rekening mee worden verklaard?

* Wachtwoord beveiliging. Voor gebruikers-en lokale computer accounts waar het wacht woord wordt opgeslagen. Zorg ervoor dat wacht woorden veilig blijven en document die toegang heeft. Overweeg het gebruik van [privileged Identity Management](../privileged-identity-management/pim-configure.md) om opgeslagen wacht woorden te beveiligen. 

  

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen over het beveiligen van service accounts

* [Inleiding tot on-premises service accounts](service-accounts-on-premises.md)

* [Door groep beheerde service accounts beveiligen](service-accounts-group-managed.md)

* [Zelfstandige beheerde service accounts beveiligen](service-accounts-standalone-managed.md)

* [Computer accounts beveiligen](service-accounts-computer.md)

* [Gebruikers accounts beveiligen](service-accounts-user-on-premises.md)

* [On-premises service accounts beheren](service-accounts-govern-on-premises.md)

 
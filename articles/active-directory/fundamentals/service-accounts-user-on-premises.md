---
title: Service accounts op basis van gebruikers beveiligen | Azure Active Directory
description: Een hand leiding voor het beveiligen van on-premises gebruikers accounts.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417176"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Service accounts op basis van gebruikers beveiligen in Active Directory

On-premises gebruikers accounts zijn de traditionele benadering voor het beveiligen van services die worden uitgevoerd in Windows. Gebruik deze accounts als laatste redmiddel wanneer globale beheerde service accounts (Gmsa's) en zelfstandige beheerde service accounts (sMSAs) niet door uw service worden ondersteund. Zie overzicht van on-premises service accounts voor meer informatie over het selecteren van het beste type account dat moet worden gebruikt. Onderzoek ook of u uw service kunt verplaatsen om een Azure-service account te gebruiken, zoals een beheerde identiteit of een service principe. 

On-premises gebruikers accounts kunnen worden gemaakt om een beveiligings context voor services te bieden en machtigingen te verlenen die nodig zijn voor de services om toegang te krijgen tot lokale en netwerk bronnen. Ze moeten hand matig wachtwoord beheer hebben zoals elk ander Active Directory (AD)-gebruikers account. Service-en domein beheerders moeten een sterk wacht woord beheer proces gebruiken om deze accounts te beveiligen.

Wanneer u een gebruikers account als een service account gebruikt, gebruikt u dit alleen voor één service. Noem het zodanig dat het een service account is en voor welke service. 

## <a name="benefits-and-challenges"></a>Voor delen en uitdagingen

Voordelen

On-premises gebruikers accounts zijn het meest veelzijdige account type voor gebruik met Services. Gebruikers accounts die worden gebruikt als service accounts, kunnen worden beheerd door alle beleids regels die gelden voor normale gebruikers accounts. U kunt deze alleen gebruiken als u geen MSA kunt gebruiken. Beoordeel ook of een computer account een betere optie is. 

Problemen met on-premises gebruikers accounts

De volgende uitdagingen zijn gekoppeld aan het gebruik van on-premises gebruikers accounts.

| Uitdagingen| Oplossingen |
| - | - |
| Wachtwoord beheer is een hand matig proces dat kan leiden tot zwakkere beveiliging en downtime van services.| Zorg ervoor dat de wachtwoord complexiteit en het wijzigen van het wacht woord worden bepaald door een robuust proces dat regel matige updates met een sterk wacht woord waarborgt. <br> Het wijzigen van het wachtwoord wijziging met een wachtwoord update voor de service, wat resulteert in downtime van de service. |
| Het identificeren van on-premises gebruikers accounts die fungeren als service accounts kunnen lastig zijn.| Documenteer en onderhoud records van service accounts die in uw omgeving zijn geïmplementeerd. <br> Volg de account naam en de resources waaraan ze toegang hebben toegewezen. <br> Overweeg om een voor voegsel van svc_ toe te voegen aan alle gebruikers accounts die worden gebruikt als service accounts. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>On-premises gebruikers accounts zoeken die worden gebruikt als service accounts

On-premises gebruikers accounts zijn net als elk ander AD-gebruikers account. Daarom kan het lastig zijn om deze accounts te vinden omdat er geen enkel kenmerk van een gebruikers account is dat dit als een service account identificeert. 

U wordt aangeraden een eenvoudig herken bare naam Conventie te maken voor elk gebruikers account dat wordt gebruikt als een service account.

Voeg bijvoorbeeld ' service-' als voor voegsel toe en noem de service: ' service-HRDataConnector '.

U kunt een aantal van de onderstaande indica toren gebruiken om deze service accounts te vinden, maar dit kan echter niet alle accounts vinden.

* Accounts die worden vertrouwd voor delegering.

* Accounts met service principal-namen.

* Accounts waarvan het wacht woord is ingesteld op nooit verlopen.

U kunt de volgende Power shell-opdrachten uitvoeren om de on-premises gebruikers accounts te vinden die voor services zijn gemaakt.

### <a name="find-accounts-trusted-for-delegation"></a>Accounts zoeken die worden vertrouwd voor delegering

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Accounts met namen van service Principles zoeken

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Accounts zoeken waarvoor wacht woorden zijn ingesteld op nooit verlopen

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


U kunt ook de toegang tot gevoelige resources controleren en audit logboeken archiveren in een SIEM-systeem (Security Information and Event Management). Met systemen als Azure Log Analytics of Azure Sentinel kunt u zoeken naar en analyseren en service accounts maken.

## <a name="assess-security-of-on-premises-user-accounts"></a>Beveiliging van on-premises gebruikers accounts beoordelen

Evalueer de beveiliging van uw on-premises gebruikers accounts die worden gebruikt als service accounts aan de hand van de volgende criteria:

* Wat is het beleid voor wachtwoord beheer?

* Is het account lid van een geprivilegieerde groep?

* Heeft het account lees-en schrijf toegang tot belang rijke bronnen?

### <a name="mitigate-potential-security-issues"></a>Mogelijke beveiligings problemen oplossen

De volgende tabel bevat mogelijke beveiligings problemen en bijbehorende oplossingen voor on-premises gebruikers accounts.

| Beveiligingsproblemen| Oplossingen |
| - | - |
| Wachtwoordbeheer|* Zorg ervoor dat de wachtwoord complexiteit en het wijzigen van het wacht woord van een robuust proces zijn dat regel matige updates met sterke wachtwoord vereisten waarborgt. <br> * Wijzig de wachtwoord wijziging met een wachtwoord update om de uitval tijd van de service te minimaliseren. |
| Account is lid van geprivilegieerde groepen.| Groepslid maatschappen controleren. Verwijder het account uit geprivilegieerde groepen. Verleen het account alleen de rechten en machtigingen die nodig zijn om de service uit te voeren (Neem contact op met de service leverancier). U kunt zich bijvoorbeeld lokaal aanmelden weigeren of interactieve aanmelding afwijzen. |
| Account heeft lees-/schrijftoegang tot gevoelige bronnen.| Toegang tot gevoelige bronnen controleren. Archiveer audit logboeken naar een SIEM (Azure Log Analytics of Azure Sentinel) voor analyse. Herstel resource machtigingen als er een ongewenst toegangs niveau wordt gedetecteerd. |


## <a name="move-to-more-secure-account-types"></a>Verplaatsen naar meer beveiligde account typen

Micro soft raadt klanten niet aan om on-premises gebruikers accounts te gebruiken als service accounts. Voor alle services die gebruikmaken van dit type account, moet u beoordelen of dit in plaats daarvan kan worden geconfigureerd voor gebruik van een gMSA of een sMSA.

U kunt ook evalueren of de service zelf kan worden verplaatst naar Azure zodat er meer beveiligde service-account typen kunnen worden gebruikt. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen over het beveiligen van service accounts

* [Inleiding tot on-premises service accounts](service-accounts-on-premises.md)

* [Door groep beheerde service accounts beveiligen](service-accounts-group-managed.md)

* [Zelfstandige beheerde service accounts beveiligen](service-accounts-standalone-managed.md)

* [Computer accounts beveiligen](service-accounts-computer.md)

* [Gebruikers accounts beveiligen](service-accounts-user-on-premises.md)

* [On-premises service accounts beheren](service-accounts-govern-on-premises.md)

 

---
title: Computer accounts beveiligen | Azure Active Directory
description: Een hand leiding voor het beveiligen van on-premises computer accounts.
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
ms.openlocfilehash: 22faba20cb12ae755f19fe43c295d98f9b364cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417195"
---
# <a name="securing-computer-accounts"></a>Computer accounts beveiligen

Het computer account of het account LocalSystem is een ingebouwd, zeer bevoegd account met toegang tot vrijwel alle resources op de lokale computer. Dit account is niet gekoppeld aan een aangemeld gebruikers account. Services die worden uitgevoerd als LocalSystem hebben toegang tot netwerk bronnen door de referenties van de computer te presen teren aan externe servers. De referenties worden in de vorm <domain_name>\<computer_name> $ weer gegeven. De vooraf gedefinieerde naam van een computer account is NT AUTHORITY\SYSTEM. Het kan worden gebruikt om een service te starten en beveiligings context voor die service op te geven.

![[Afbeelding 4] (.\media\securing-service-accounts\secure-computer-accounts-image-1.png)](.\media\securing-service-accounts\secure-computer-accounts-image-1.png)

## <a name="benefits-of-using-the-computer-account"></a>Voor delen van het gebruik van het computer account

Het computer account biedt de volgende voor delen.

* **Onbeperkte lokale toegang**: het computer account biedt volledige toegang tot de lokale bronnen van de machine.

* **Automatisch wachtwoord beheer**: het computer account verwijdert de nood zaak voor het hand matig wijzigen van wacht woorden. Dit account is in plaats daarvan lid van Active Directory en het account wachtwoord wordt automatisch gewijzigd. Ook hoeft u de Service Principal Name voor de service niet te registreren.

* **Beperkte toegangs rechten** op de computer: de standaard Access Control lijst (ACL) in Active Directory Domain Services staat minimale toegang toe voor computer accounts. Als deze service is gekraakt, heeft deze alleen beperkte toegang tot resources in uw netwerk.

## <a name="assess-security-posture-of-computer-accounts"></a>De beveiligings postuur van computer accounts beoordelen

Mogelijke uitdagingen en bijbehorende oplossingen bij het gebruik van computer accounts. 

| Problemen| Oplossingen |
| - | - |
| Computer accounts zijn onderhevig aan het verwijderen en recreatie wanneer de computer het domein verlaat en opnieuw verbindt.| Valideer de nood zaak om een computer toe te voegen aan een AD-groep en controleer welk computer account is toegevoegd aan een groep met behulp van de voorbeeld scripts op deze pagina.| 
| Als u een computer account aan een groep toevoegt, krijgen alle services die als LocalSystem worden uitgevoerd op die computer toegangs rechten van de groep.| De groepslid maatschappen van uw computer account selectief zijn. Vermijd het maken van computer accounts leden van een domein beheerders groep, omdat de bijbehorende service volledige toegang tot Active Directory Domain Services heeft. |
| Onjuiste netwerk standaard waarden voor LocalSystem| Ga er niet van uit dat het computer account de standaard beperkte toegang tot netwerk bronnen heeft. In plaats daarvan moet u de groepslid maatschappen voor dit account zorgvuldig controleren. |
| Onbekende services die worden uitgevoerd als LocalSystem| Zorg ervoor dat alle services die onder het account LocalSystem worden uitgevoerd, micro soft-Services of vertrouwde services van derden zijn. |


## <a name="find-services-running-under-the-computer-account"></a>Services zoeken die worden uitgevoerd onder het computer account

Gebruik de volgende Power shell-cmdlet om services te vinden die worden uitgevoerd onder de context LocalSystem

```powershell

Get-WmiObject win32_service | select Name, StartName | Where-Object {($_.StartName -eq "LocalSystem")}
```

**Computers accounts zoeken die lid zijn van een specifieke groep**

Gebruik de volgende Power shell-cmdlet om computer accounts te vinden die lid zijn van een specifieke groep.

```powershell

```Get-ADComputer -Filter {Name -Like "*"} -Properties MemberOf | Where-Object {[STRING]$_.MemberOf -like "Your_Group_Name_here*"} | Select Name, MemberOf
```

**Computers accounts zoeken die lid zijn van geprivilegieerde groepen**

Gebruik de volgende Power shell-cmdlet om computer accounts te vinden die lid zijn van identiteits beheerders groepen (domein Administrators, ondernemings Administrators, beheerders)

```powershell
Get-ADGroupMember -Identity Administrators -Recursive | Where objectClass -eq "computer"
```
## <a name="move-from-computer-accounts"></a>Van computer accounts verplaatsen

> [!IMPORTANT]
> Computer accounts zijn accounts met zeer privileges en moeten alleen worden gebruikt wanneer uw service onbeperkte toegang tot lokale bronnen op de computer vereist en u geen beheerde service account (MSA) kunt gebruiken.

* Neem contact op met de eigenaar van de service als de service kan worden uitgevoerd met behulp van MSA en gebruik een door een groep beheerd service account (gMSA) of een zelfstandig beheerd service account (sMSA) als uw service dit ondersteunt.

* Gebruik een domein gebruikers account met alleen de bevoegdheden die nodig zijn om uw service uit te voeren.

## <a name="next-steps"></a>Volgende stappen 

Raadpleeg de volgende artikelen over het beveiligen van service accounts

* [Inleiding tot on-premises service accounts](service-accounts-on-premises.md)

* [Door groep beheerde service accounts beveiligen](service-accounts-group-managed.md)

* [Zelfstandige beheerde service accounts beveiligen](service-accounts-standalone-managed.md)

* [Computer accounts beveiligen](service-accounts-computer.md)

* [Gebruikers accounts beveiligen](service-accounts-user-on-premises.md)

* [On-premises service accounts beheren](service-accounts-govern-on-premises.md)

 

 

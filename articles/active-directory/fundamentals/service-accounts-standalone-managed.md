---
title: Zelfstandige beheerde service accounts beveiligen | Azure Active Directory
description: Een hand leiding voor het beveiligen van zelfstandige beheerde service accounts.
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
ms.openlocfilehash: e2b3079407774c3d36fe5515b39e964018f9087e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548849"
---
# <a name="securing-standalone-managed-service-accounts"></a>Zelfstandige beheerde service accounts beveiligen

Zelfstandige beheerde service accounts (sMSAs) zijn beheerde domein accounts die worden gebruikt voor het beveiligen van een of meer services die worden uitgevoerd op een server. Ze kunnen niet opnieuw worden gebruikt op meerdere servers. sMSAs bieden automatische wachtwoord beheer, vereenvoudigd Service Principal Name (SPN) en de mogelijkheid om beheer te delegeren aan andere beheerders. 

In Active Directory zijn sMSAs gekoppeld aan een specifieke server waarop een service wordt uitgevoerd. Deze accounts kunt u vinden in de module Active Directory gebruikers en computers van de micro soft Management Console.

![Een scherm opname van de module Active Directory gebruikers en computers met de organisatie-eenheid managed service accounts.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Beheerde service accounts zijn geïntroduceerd in Windows Server 2008R2 Active Directory schema en vereisen een mini maal niveau van het besturings systeem Windows Server 2008R2. 

## <a name="benefits-of-using-smsas"></a>Voor delen van het gebruik van sMSAs

sMSAs bieden betere beveiliging dan gebruikers accounts die worden gebruikt als service accounts, terwijl de administratieve overhead wordt verminderd door:

* Sterke wacht woorden instellen. sMSAs gebruik 240 byte wille keurig gegenereerd complexe wacht woorden. De complexiteit en lengte van sMSA-wacht woorden minimaliseert de kans dat een service wordt aangetast door beveiligings-of woordenboek aanvallen.

* Wacht woorden regel matig. Het sMSA-wacht woord wordt in Windows elke 30 dagen automatisch gewijzigd. Service-en domein beheerders hoeven geen wachtwoord wijzigingen te plannen of de bijbehorende downtime te beheren.

* SPN-beheer vereenvoudigen. Service-principal-namen worden automatisch bijgewerkt als het domein functionaliteits niveau (DFL) Windows Server 2008 R2 is. Zo wordt de Service Principal Name automatisch bijgewerkt in de volgende scenario's:

   * De naam van het computer account van de hostcomputer is gewijzigd. 

   * De DNS-naam van de hostcomputer wordt gewijzigd.

   * Bij het toevoegen of verwijderen van een extra SAM-account-of DNS-hostname-para meters met behulp van [Power shell](/powershell/module/addsadministration/set-adserviceaccount)

## <a name="when-to-use-smsas"></a>Wanneer u sMSAs gebruikt

sMSAs kunnen beheer-en beveiligings taken vereenvoudigen. Gebruik sMSAs wanneer u een of meer services hebt geïmplementeerd op één server en u geen gMSA kunt gebruiken. 

> [!NOTE] 
> Hoewel u sMSAs kunt gebruiken voor meer dan één service, is het raadzaam dat elke service een eigen identiteit voor controle doeleinden heeft. 

Als de maker van de software u niet kan vertellen of een MSA kan worden gebruikt, moet u de toepassing testen. Als u dit wilt doen, maakt u een test omgeving en zorgt u ervoor dat deze toegang heeft tot alle vereiste resources. Zie [een sMSA maken en installeren](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) voor stapsgewijze instructies.

### <a name="assess-security-posture-of-smsas"></a>Beveiligings postuur van sMSAs beoordelen

sMSAs zijn inherent veiliger dan standaard gebruikers accounts, waarvoor voortdurend wachtwoord beheer is vereist. Het is echter belang rijk om sMSAs te beschouwen als onderdeel van hun algemene beveiligings postuur.

In de volgende tabel ziet u hoe u mogelijke beveiligings problemen kunt oplossen die zijn verbonden met sMSAs.

| Beveiligingsproblemen| Oplossingen |
| - | - |
| sMSA is lid van geprivilegieerde groepen|Verwijder de sMSA van verhoogde geprivilegieerde groepen (zoals domein Administrators). <br> Gebruik het minst geprivilegieerde model en verleen de sMSA alleen de rechten en machtigingen die nodig zijn om de service (s) uit te voeren. <br> Als u niet zeker bent van de vereiste machtigingen, raadpleegt u de maker van de service. |
| sMSA heeft lees-/schrijftoegang tot gevoelige bronnen.|Toegang tot gevoelige bronnen controleren. Archiveer audit logboeken naar een SIEM (Azure Log Analytics of Azure Sentinel) voor analyse. <br> Herstel resource machtigingen als er een ongewenst toegangs niveau wordt gedetecteerd. |
| Standaard is de sMSA-wachtwoord rollover frequentie 30 dagen| Groeps beleid kan worden gebruikt voor het afstemmen van de duur, afhankelijk van de beveiligings vereisten van de onderneming. <br> * U kunt de verloop tijd van het wacht woord instellen met behulp van het volgende pad. <br>Computer gaisatie Options\Domain lid: maximale leeftijd van wacht woord voor computer account |



### <a name="challenges-with-smsas"></a>Uitdagingen met sMSAs

De uitdagingen met betrekking tot sMSAs zijn als volgt:

| Uitdagingen| Oplossingen |
| - | - |
| Ze kunnen op één server worden gebruikt.| Gebruik Gmsa's als u het account wilt gebruiken op meerdere servers. |
| Ze kunnen niet worden gebruikt in verschillende domeinen.| Gebruik Gmsa's als u het account wilt gebruiken in domeinen. |
| Niet alle toepassingen bieden ondersteuning voor sMSAs.| Gebruik, indien mogelijk, Gmsa's. Als u geen standaard gebruikers account of computer account gebruikt zoals aanbevolen door de maker van de toepassing. |


## <a name="find-smsas"></a>SMSAs zoeken

Voer op een domein controller DSA. msc uit en vouw de container managed service accounts uit om alle sMSAs weer te geven. 

Met de volgende Power shell-opdracht worden alle sMSAs en Gmsa's in het domein Active Directory geretourneerd. 

`Get-ADServiceAccount -Filter *`

De volgende opdracht retourneert alleen sMSAs in het domein Active Directory.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>SMSAs beheren

U kunt de volgende Active Directory Power shell-cmdlets gebruiken voor het beheren van sMSAs:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>Naar sMSAs

Als een toepassings service sMSA ondersteunt, maar niet Gmsa's, en momenteel een gebruikers account of computer account gebruikt voor de beveiligings context, [maakt en installeert u een sMSA](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) op de-server. 

Verplaats resources in het ideale geval naar Azure en gebruik Azure beheerde identiteiten of service-principals.

 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen over het beveiligen van service accounts

* [Inleiding tot on-premises service accounts](service-accounts-on-premises.md)

* [Door groep beheerde service accounts beveiligen](service-accounts-group-managed.md)

* [Zelfstandige beheerde service accounts beveiligen](service-accounts-standalone-managed.md)

* [Computer accounts beveiligen](service-accounts-computer.md)

* [Gebruikers accounts beveiligen](service-accounts-user-on-premises.md)

* [On-premises service accounts beheren](service-accounts-govern-on-premises.md)


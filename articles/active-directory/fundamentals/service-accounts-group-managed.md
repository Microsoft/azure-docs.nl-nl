---
title: Door groepen beheerde service accounts beveiligen | Azure Active Directory
description: Een hand leiding voor het beveiligen van computer accounts voor beheerde service accounts voor groepen.
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
ms.openlocfilehash: bd4c1adddbf4b13f8e299bd656443c9aaab1d55b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644824"
---
# <a name="securing-group-managed-service-accounts"></a>Door groepen beheerde service accounts beveiligen

Door een groep beheerde service accounts (Gmsa's) zijn beheerde domein accounts die worden gebruikt voor het beveiligen van services. Gmsa's kan worden uitgevoerd op één server of in een server farm, zoals systemen achter een netwerk Load Balancer (NLB) of een Internet Information Services IIS-server. Wanneer u uw services hebt geconfigureerd voor het gebruik van een gMSA-Principal, wordt wachtwoord beheer voor dat account door Windows afgehandeld.

## <a name="benefits-of-using-gmsas"></a>Voor delen van het gebruik van Gmsa's

Gmsa's bieden één identiteits oplossing met betere beveiliging en vermindert de administratieve overhead door:

* **Sterke wacht woorden instellen**. Gmsa's gebruik 240 byte wille keurig gegenereerd complexe wacht woorden. De complexiteit en lengte van gMSA-wacht woorden minimaliseert de kans dat een service wordt aangetast door beveiligings-of woordenboek aanvallen.

* **Wacht woorden regel matig**. Gmsa's Shift-wacht woord beheer naar Windows, waarmee het wacht woord elke 30 dagen wordt gewijzigd. Service-en domein beheerders hoeven niet langer wachtwoord wijzigingen te plannen of service-uitvallen te beheren om service accounts te beveiligen. 

* **Ondersteuning voor de implementatie naar server farms**. De mogelijkheid om Gmsa's te implementeren op meerdere servers biedt ondersteuning voor oplossingen met gelijke taak verdeling waarbij meerdere hosts dezelfde service uitvoeren. 

* Een **vereenvoudigd SPN-beheer (Server Principal Name) ondersteunen**. U kunt SPN instellen met behulp van Power shell op het moment dat het account wordt gemaakt. Daarnaast kunnen services die automatische SPN-registraties ondersteunen, dit doen tegen de gMSA, mits de gMSA-machtigingen correct zijn ingesteld. 

## <a name="when-to-use-gmsas"></a>Wanneer u Gmsa's gebruikt

Gebruik Gmsa's als het voorkeurs account voor on-premises Services, tenzij een service, zoals failover clustering, dit niet ondersteunt.

> [!IMPORTANT]
> U moet uw service testen met Gmsa's voordat u de implementatie in productie neemt. U doet dit door een test omgeving in te stellen en ervoor te zorgen dat de toepassing de gMSA kan gebruiken en toegang heeft tot de bronnen die nodig zijn voor toegang. Zie [ondersteuning voor door groepen beheerde service accounts](/system-center/scom/support-group-managed-service-accounts?view=sc-om-2019)voor meer informatie.


Als een service het gebruik van Gmsa's niet ondersteunt, kunt u het beste een zelfstandig beheerd service account (sMSA) gebruiken. sMSAs bieden dezelfde functionaliteit als een gMSA, maar zijn alleen bedoeld voor implementatie op één server.

Als u geen gebruik kunt maken van een gMSA of sMSA wordt ondersteund door uw service, moet de service worden geconfigureerd om te worden uitgevoerd als een standaard gebruikers account. Service-en domein beheerders moeten een sterk wacht woord voor wachtwoord beheer observeren om het account veilig te houden.

## <a name="assess-the-security-posture-of-gmsas"></a>De beveiligings postuur van Gmsa's evalueren

Gmsa's zijn inherent veiliger dan standaard gebruikers accounts, waarvoor voortdurend wachtwoord beheer is vereist. Het is echter belang rijk om de Gmsa's-reik wijdte van de toegang te overwegen terwijl u de algemene beveiligings postuur bekijkt.

De volgende tabel bevat mogelijke beveiligings problemen en oplossingen voor het gebruik van Gmsa's.

| Beveiligingsproblemen| Oplossingen |
| - | - |
| gMSA is lid van geprivilegieerde groepen. | Controleer uw groepslid maatschappen. Als u dit wilt doen, kunt u een Power shell-script maken om alle groepslid maatschappen op te sommen en vervolgens een resulterende CSV-bestand filteren op de namen van uw gMSA-bestanden. <br>Verwijder de gMSA uit de geprivilegieerde groepen.<br> Ken gMSA alleen de rechten en machtigingen toe die nodig zijn om de service uit te voeren (Raadpleeg de leverancier van uw service). 
| gMSA heeft lees-/schrijftoegang tot gevoelige bronnen. | Toegang tot gevoelige bronnen controleren. Archiveer audit logboeken naar een SIEM, bijvoorbeeld Azure Log Analytics of Azure Sentinel, voor analyse. Verwijder onnodige bron machtigingen als er een ongewenst toegangs niveau wordt gedetecteerd. |


## <a name="find-gmsas"></a>Gmsa's zoeken

Uw organisatie heeft mogelijk al Gmsa's gemaakt. Voer de volgende Power shell-cmdlet uit om deze accounts op te halen:

Gmsa's moet zich in de organisatie-eenheid (OE) van de beheerde service accounts bevinden om effectief te kunnen werken.

  
![Scherm opname van de organisatie-eenheid van het beheerde service account.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Zie de volgende opdrachten om service Msa's te vinden die hier niet mogelijk zijn.

**Alle service accounts, waaronder Gmsa's en sMSAs, zoeken:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount”}
```

## <a name="manage-gmsas"></a>Gmsa's beheren

U kunt de volgende Active Directory Power shell-cmdlets gebruiken voor het beheren van Gmsa's:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Vanaf Windows Server 2012 werken de cmdlets *-ADServiceAccount standaard met Gmsa's. Zie aan de slag [**met door groep beheerde service accounts**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)voor meer informatie over het gebruik van de bovenstaande cmdlets.

## <a name="move-to-a-gmsa"></a>Naar een gMSA verplaatsen
Gmsa's zijn het veiligste type service account voor on-premises behoeften. Als u naar een andere kunt gaan, moet u. U kunt ook uw Services verplaatsen naar Azure en uw service accounts naar Azure Active Directory.

1.  Zorg ervoor dat de [basis sleutel KDS in het forest is geïmplementeerd](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key). Dit is een eenmalige bewerking.

2. [Maak een nieuwe gMSA](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Installeer de nieuwe gMSA op elke host waarop de service wordt uitgevoerd.
   > [!NOTE] 
   > Zie aan de slag [met door groep beheerde service accounts](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)) voor meer informatie over het maken en installeren van gMSA op een host voordat u de service configureert voor het gebruik van gMSA.

 
4. Wijzig uw service-identiteit in gMSA en geef een leeg wacht woord op.

5. Controleer of uw service werkt onder de nieuwe gMSA-identiteit.

6. Verwijder de oude service account-id.

 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen over het beveiligen van service accounts

* [Inleiding tot on-premises service accounts](service-accounts-on-premises.md)

* [Door groep beheerde service accounts beveiligen](service-accounts-group-managed.md)

* [Zelfstandige beheerde service accounts beveiligen](service-accounts-standalone-managed.md)

* [Computer accounts beveiligen](service-accounts-computer.md)

* [Gebruikers accounts beveiligen](service-accounts-user-on-premises.md)

* [On-premises service accounts beheren](service-accounts-govern-on-premises.md)
---
title: Beleid beveiligen en gebruiken
description: Meer informatie over beveiliging en beleid voor virtuele machines in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 83d291ae72e79ec3600015fac07ee4ab80fe5df4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843550"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Beveiligen en beleidsregels gebruiken op virtuele machines in Azure

Het is belang rijk om uw virtuele machine (VM) te beveiligen voor de toepassingen die u uitvoert. Het beveiligen van uw Vm's kan bestaan uit een of meer Azure-Services en-functies die beveiligde toegang bieden tot uw Vm's en beveiligde opslag van uw gegevens. In dit artikel vindt u informatie waarmee u uw virtuele machines en toepassingen kunt beveiligen.

## <a name="antimalware"></a>Antimalware

Het moderne risico landschap voor Cloud omgevingen is dynamisch, waardoor de druk wordt verhoogd om effectief beveiliging te garanderen om te voldoen aan de nalevings-en beveiligings vereisten. [Micro soft antimalware voor Azure](../security/fundamentals/antimalware.md) is een gratis real-time beschermings functie waarmee u virussen, spyware en andere schadelijke software kunt herkennen en verwijderen. Waarschuwingen kunnen worden geconfigureerd om u te waarschuwen wanneer bekende of ongewenste software zichzelf probeert te installeren of uit te voeren op uw virtuele machine. Het wordt niet ondersteund op virtuele machines met Linux of Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) helpt u bij het voor komen, detecteren en reageren op bedreigingen voor uw virtuele machines. Security Center biedt geïntegreerde beveiligings bewaking en beleids beheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders niet kunnen worden opgemerkt en werkt met een uitgebreid ecosysteem van beveiligings oplossingen.

De just-in-time-toegang van Security Center kan worden toegepast op uw VM-implementatie om inkomend verkeer naar uw Azure-Vm's te vergren delen, waardoor de bloot stelling aan aanvallen wordt verkleind en zo snel mogelijk verbinding met Vm's wordt gemaakt. Wanneer just-in-time is ingeschakeld en een gebruiker toegang tot een virtuele machine vraagt, controleert Security Center welke machtigingen de gebruiker heeft voor de virtuele machine. Als ze over de juiste machtigingen beschikken, wordt de aanvraag goedgekeurd en Security Center wordt automatisch de netwerk beveiligings groepen (Nsg's) geconfigureerd om binnenkomend verkeer voor de geselecteerde poorten gedurende een beperkte periode toe te staan. Nadat de tijd is verstreken, wordt de Nsg's door Security Center teruggezet naar de vorige status. 

## <a name="encryption"></a>Versleuteling

Er worden twee versleutelings methoden aangeboden voor Managed disks. Versleuteling op het niveau van het besturings systeem, dat Azure Disk Encryption is en versleuteling op platform niveau, dat versleuteling aan de server zijde is.

### <a name="server-side-encryption"></a>Versleuteling aan de serverzijde

Met Azure Managed disks worden uw gegevens standaard automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Versleuteling aan de server zijde beveiligt uw gegevens en helpt u te voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie. Gegevens in azure Managed disks worden transparant versleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok versleuteling die beschikbaar is en is compatibel met FIPS 140-2.

Versleuteling heeft geen invloed op de prestaties van Managed disks. Er zijn geen extra kosten voor het versleutelen van de versleuteling.

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf of u kunt versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, kunt u een door de *klant beheerde sleutel* opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle gegevens in Managed disks. 

Raadpleeg de artikelen voor [Windows](windows/disk-encryption.md) of [Linux](linux/disk-encryption.md)voor meer informatie over versleuteling aan de server zijde.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Virtuele schijven in azure kunnen worden versleuteld voor verbeterde beveiliging en naleving van de [Windows-VM](windows/disk-encryption-overview.md) en de [Linux-VM](linux/disk-encryption-overview.md) . Virtuele schijven op Windows-Vm's worden op rest versleuteld met behulp van BitLocker. Virtuele schijven op Linux Vm's worden op rest versleuteld met behulp van DM-cryptografie. 

Er worden geen kosten in rekening gebracht voor het versleutelen van virtuele schijven in Azure. Cryptografische sleutels worden opgeslagen in Azure Key Vault met behulp van softwarebeveiliging, of u kunt uw sleutels in Hardware Security Modules (HMS's), die zijn gecertificeerd voor FIPS 140-2 level 2-standaarden, importeren of genereren. Deze cryptografische sleutels worden gebruikt voor het versleutelen en ontsleutelen van virtuele schijven die zijn gekoppeld aan uw VM. U behoudt de controle over deze cryptografische sleutels en kunt het gebruik controleren. Een Azure Active Directory Service-Principal biedt een beveiligd mechanisme voor het uitgeven van deze cryptografische sleutels als Vm's worden in-en uitgeschakeld.

## <a name="key-vault-and-ssh-keys"></a>Key Vault-en SSH-sleutels

Geheimen en certificaten kunnen worden gemodelleerd als resources en worden verschaft door [Key Vault](../key-vault/general/basic-concepts.md). U kunt Azure PowerShell gebruiken om sleutel kluizen te maken voor [Windows-vm's](windows/key-vault-setup.md) en de Azure CLI voor [Linux-vm's](linux/key-vault-setup.md). U kunt ook sleutels maken voor versleuteling.

Toegangs beleid voor sleutel kluis verleent machtigingen voor sleutels, geheimen en certificaten afzonderlijk. U kunt bijvoorbeeld een gebruiker toegang geven tot sleutels, maar geen machtigingen voor geheimen geven. Machtigingen voor toegang tot sleutels, geheimen of certificaten bevinden zich echter op het niveau van de Key Vault. Met andere woorden, het [toegangs beleid voor de sleutel kluis](../key-vault/general/secure-your-key-vault.md) biedt geen ondersteuning voor machtigingen op object niveau.

Wanneer u verbinding maakt met Vm's, moet u gebruikmaken van open bare-sleutel cryptografie voor een veiligere manier om u aan te melden. Dit proces omvat een open bare en persoonlijke sleutel uitwisseling met behulp van de opdracht Secure Shell (SSH) voor de verificatie van uzelf in plaats van een gebruikers naam en wacht woord. Wacht woorden zijn kwetsbaar voor aanvallen met een zwakke kracht, met name op Internet gerichte Vm's zoals webservers. Met een SSH-sleutel paar (Secure Shell) kunt u een [virtuele Linux-machine](linux/mac-create-ssh-keys.md) maken die gebruikmaakt van SSH-sleutels voor verificatie, waardoor het niet nodig is om zich aan te melden. U kunt ook SSH-sleutels gebruiken om verbinding te maken vanaf een [Windows-VM](linux/ssh-from-windows.md) naar een virtuele Linux-machine.

## <a name="managed-identities-for-azure-resources"></a>Beheerde identiteiten voor Azure-resources

Een veelvoorkomende uitdaging bij het bouwen van cloud-apps is het beheren van de referenties in uw code voor verificatie bij cloudservices. Het is belangrijk dat de referenties veilig worden bewaard. In het ideale geval worden de referenties nooit weergegeven op werkstations van ontwikkelaars en niet ingecheckt in broncodebeheer. Azure Key Vault biedt een manier voor het veilig opslaan van referenties, geheimen en andere sleutels, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen. 

Dit probleem wordt opgelost met de functie Beheerde identiteiten voor Azure-resources in Azure Active Directory (Azure AD). De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in de code.  Uw code die op een virtuele machine wordt uitgevoerd, kan een token aanvragen van twee eind punten die alleen toegankelijk zijn vanuit de virtuele machine. Raadpleeg de pagina [Managed Identities voor Azure resources](../active-directory/managed-identities-azure-resources/overview.md) Overview voor meer gedetailleerde informatie over deze service.   

## <a name="policies"></a>Beleidsregels

[Azure-beleid](../governance/policy/overview.md) kan worden gebruikt voor het definiëren van het gewenste gedrag voor de virtuele [Windows-machines](./windows/policy.md) en [Linux-vm's](./linux/policy.md)van uw organisatie. Door gebruik te maken van beleids regels kan een organisatie verschillende conventies en regels voor de hele onderneming afdwingen. Afdwinging van het gewenste gedrag kan het risico verkleinen en bijdragen aan het succes van de organisatie.

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Met [op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure](../role-based-access-control/overview.md)kunt u taken in uw team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers op uw virtuele machine die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen voor de virtuele machine te geven, kunt u alleen bepaalde acties toestaan. U kunt toegangs beheer voor de virtuele machine configureren in het [Azure Portal](../role-based-access-control/role-assignments-portal.md), met behulp van de [Azure cli](/cli/azure/role)of[Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Volgende stappen
- Door loop de stappen voor het bewaken van de beveiliging van virtuele machines met behulp van Azure Security Center voor [Linux](../security/fundamentals/overview.md) of [Windows](windows/tutorial-azure-security.md).
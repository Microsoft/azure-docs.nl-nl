---
title: Beleidsregels beveiligen en gebruiken
description: Meer informatie over beveiliging en beleid voor virtuele machines in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: cynthn
ms.topic: conceptual
ms.openlocfilehash: 840045da33938d4c1cd725fd5a99bf1b8014f6b1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748462"
---
# <a name="secure-and-use-policies-on-virtual-machines-in-azure"></a>Beveiligen en beleidsregels gebruiken op virtuele machines in Azure

Het is belangrijk om uw virtuele machine (VM) veilig te houden voor de toepassingen die u gebruikt. Het beveiligen van uw VM's kan een of meer Azure-services en -functies omvatten die beveiligde toegang tot uw VM's en beveiligde opslag van uw gegevens omvatten. Dit artikel bevat informatie waarmee u uw VM en toepassingen veilig kunt houden.

## <a name="antimalware"></a>Antimalware

Het moderne bedreigingslandschap voor cloudomgevingen is dynamisch, waardoor de druk toeneemt om effectieve beveiliging te handhaven om te voldoen aan de nalevings- en beveiligingsvereisten. [Microsoft Antimalware voor Azure](../security/fundamentals/antimalware.md) is een gratis realtime-beveiligingsmogelijkheid waarmee virussen, spyware en andere schadelijke software kunnen worden identificeren en verwijderd. Waarschuwingen kunnen worden geconfigureerd om u op de hoogte te stellen wanneer bekende schadelijke of ongewenste software zichzelf probeert te installeren of uit te voeren op uw VM. Het wordt niet ondersteund op VM's met Linux of Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../security-center/security-center-introduction.md) helpt u bedreigingen voor uw VM's te voorkomen, te detecteren en hierop te reageren. Security Center biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders ongemerkt zouden blijven en werkt met een breed ecosysteem van beveiligingsoplossingen.

Security Center Just-In-Time-toegang kan worden toegepast op uw VM-implementatie om het inkomende verkeer naar uw Azure-VM's te vergrendelen, waardoor de blootstelling aan aanvallen wordt verkleind en tegelijkertijd eenvoudig verbinding kan worden gemaakt met VM's wanneer dat nodig is. Wanneer Just-In-Time is ingeschakeld en een gebruiker toegang tot een VM aanvraagt, controleert Security Center welke machtigingen de gebruiker heeft voor de VM. Als ze de juiste machtigingen hebben, wordt de aanvraag goedgekeurd en configureert Security Center automatisch de netwerkbeveiligingsgroepen (NSG's) om binnenkomende verkeer naar de geselecteerde poorten voor een beperkte tijd toe te staan. Nadat de tijd is verstreken, Security Center de NSG's terugzetten naar hun vorige staat. 

## <a name="encryption"></a>Versleuteling

Er worden twee versleutelingsmethoden aangeboden voor beheerde schijven. Versleuteling op het niveau van het besturingssysteem, Azure Disk Encryption, en versleuteling op platformniveau. Dit is versleuteling aan de serverzijde.

### <a name="server-side-encryption"></a>Versleuteling aan de serverzijde

Azure Managed Disks versleutelt uw gegevens standaard automatisch wanneer ze in de cloud worden opgeslagen. Versleuteling aan de serverzijde beschermt uw gegevens en helpt u te voldoen aan de beveiligings- en nalevingsverplichtingen van uw organisatie. Gegevens in Azure Managed Disks worden transparant versleuteld met [](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)behulp van 256-bits AES-versleuteling, een van de sterkste blokcodes die beschikbaar zijn en voldoen aan FIPS 140-2.

Versleuteling heeft geen invloed op de prestaties van beheerde schijven. Er zijn geen extra kosten voor de versleuteling.

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf of u kunt versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met  uw eigen sleutels te beheren, kunt u een door de klant beheerde sleutel opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle gegevens in beheerde schijven. 

Raadpleeg de artikelen voor [Windows](./disk-encryption.md) of Linux voor meer informatie over versleuteling aan de [serverzijde.](./disk-encryption.md)

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Voor verbeterde beveiliging [en naleving van virtuele Windows-machines](windows/disk-encryption-overview.md) en [Linux-VM's](linux/disk-encryption-overview.md) kunnen virtuele schijven in Azure worden versleuteld. Virtuele schijven op Windows-VM's worden in rust versleuteld met BitLocker. Virtuele schijven op Linux-VM's worden in rust versleuteld met behulp van dm-crypt. 

Er worden geen kosten in rekening brengen voor het versleutelen van virtuele schijven in Azure. Cryptografische sleutels worden opgeslagen in Azure Key Vault met behulp van softwarebeveiliging, of u kunt uw sleutels in Hardware Security Modules (HMS's), die zijn gecertificeerd voor FIPS 140-2 level 2-standaarden, importeren of genereren. Deze cryptografische sleutels worden gebruikt voor het versleutelen en ontsleutelen van virtuele schijven die zijn gekoppeld aan uw VM. U behoudt de controle over deze cryptografische sleutels en kunt het gebruik controleren. Een Azure Active Directory service-principal biedt een veilig mechanisme voor het uitgeven van deze cryptografische sleutels wanneer VM's worden in- en uitgeschakeld.

## <a name="key-vault-and-ssh-keys"></a>Key Vault en SSH-sleutels

Geheimen en certificaten kunnen worden gemodelleerd als resources en worden geleverd door [Key Vault.](../key-vault/general/basic-concepts.md) U kunt deze Azure PowerShell om sleutelkluizen te maken voor [Windows-VM's](windows/key-vault-setup.md) en de Azure CLI voor [Linux-VM's.](linux/key-vault-setup.md) U kunt ook sleutels voor versleuteling maken.

Toegangsbeleid voor Key Vault verleent afzonderlijk machtigingen aan sleutels, geheimen en certificaten. U kunt bijvoorbeeld een gebruiker toegang geven tot sleutels, maar geen machtigingen voor geheimen geven. Machtigingen voor toegang tot sleutels, geheimen of certificaten bevinden zich echter op het niveau van de Key Vault. Met andere woorden, [toegangsbeleid voor key vault](../key-vault/general/security-overview.md) biedt geen ondersteuning voor machtigingen op objectniveau.

Wanneer u verbinding maakt met VM's, moet u cryptografie met openbare sleutels gebruiken om een veiligere manier te bieden om u bij deze VM's aan te melden. Dit proces omvat een uitwisseling van openbare en persoonlijke sleutels met behulp van de SSH-opdracht (Secure Shell) om uzelf te verifiëren in plaats van een gebruikersnaam en wachtwoord. Wachtwoorden zijn kwetsbaar voor brute-force aanvallen, met name op internet gerichte VM's zoals webservers. Met een SSH-sleutelpaar (Secure Shell) kunt u een [linux-VM](linux/mac-create-ssh-keys.md) maken die gebruikmaakt van SSH-sleutels voor verificatie, waardoor aanmelding met wachtwoorden niet meer nodig is. U kunt ook SSH-sleutels gebruiken om vanaf een [Windows-VM verbinding](linux/ssh-from-windows.md) te maken met een Linux-VM.

## <a name="managed-identities-for-azure-resources"></a>Beheerde identiteiten voor Azure-resources

Een veelvoorkomende uitdaging bij het bouwen van cloud-apps is het beheren van de referenties in uw code voor verificatie bij cloudservices. Het is belangrijk dat de referenties veilig worden bewaard. In het ideale geval worden de referenties nooit weergegeven op werkstations van ontwikkelaars en niet ingecheckt in broncodebeheer. Azure Key Vault biedt een manier voor het veilig opslaan van referenties, geheimen en andere sleutels, maar uw code moet worden geverifieerd bij Key Vault om ze op te halen. 

Dit probleem wordt opgelost met de functie Beheerde identiteiten voor Azure-resources in Azure Active Directory (Azure AD). De functie biedt Azure-services met een automatisch beheerde identiteit in Azure AD. U kunt de identiteit gebruiken voor verificatie bij alle services die ondersteuning bieden voor Azure AD-verificatie, inclusief Key Vault, zonder referenties in de code.  Uw code die wordt uitgevoerd op een VM kan een token aanvragen bij twee eindpunten die alleen toegankelijk zijn vanuit de VM. Bekijk de overzichtspagina beheerde identiteiten voor [Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) voor meer gedetailleerde informatie over deze service.   

## <a name="policies"></a>Beleidsregels

[Azure-beleid](../governance/policy/overview.md) kan worden gebruikt om het gewenste gedrag te definiëren voor de [Windows-VM's](./windows/policy.md) en [Linux-VM's van uw organisatie.](./linux/policy.md) Met behulp van beleidsregels kan een organisatie verschillende conventies en regels afdwingen in de hele onderneming. Het afdwingen van het gewenste gedrag kan helpen risico's te beperken en tegelijkertijd bij te dragen aan het succes van de organisatie.

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Met op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../role-based-access-control/overview.md)kunt u taken scheiden binnen uw team en alleen de hoeveelheid toegang verlenen aan gebruikers op uw VM die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen te verlenen op de VM, kunt u alleen bepaalde acties toestaan. U kunt toegangsbeheer voor de VM configureren in [de Azure Portal](../role-based-access-control/role-assignments-portal.md), met behulp van [de Azure CLI](/cli/azure/role)of[Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Volgende stappen
- Doorloop de stappen voor het bewaken van de beveiliging van virtuele machines met behulp Azure Security Center [voor Linux](../security/fundamentals/overview.md) of [Windows.](/previous-versions/azure/virtual-machines/tutorial-azure-security)
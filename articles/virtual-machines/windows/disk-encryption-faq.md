---
title: Veelgestelde vragen-Azure Disk Encryption voor Windows-Vm's
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Microsoft Azure schijf versleuteling voor Windows IaaS-Vm's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6371a6bd0c4b8151c97a892ec70789429b4bda75
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562194"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Veelgestelde vragen over Azure Disk Encryption voor virtuele Windows-machines

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Disk Encryption voor Windows-Vm's. Zie [Azure Disk Encryption Overview](disk-encryption-overview.md)voor meer informatie over deze service.

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Wat is Azure Disk Encryption voor virtuele Windows-machines?

Azure Disk Encryption voor Windows-Vm's maakt gebruik van de BitLocker-functie van Windows voor het versleutelen van de volledige schijf versleuteling van de besturingssysteem schijf en de gegevens schijven. Daarnaast biedt het een versleuteling van de tijdelijke schijf wanneer de [para meter VolumeType is](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  De inhouds stromen zijn versleuteld van de virtuele machine naar de back-end van de opslag. Het leveren van end-to-end-versleuteling met een door de klant beheerde sleutel.
 
Zie [ondersteunde vm's en besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Waar wordt Azure Disk Encryption in algemene Beschik baarheid?

Azure Disk Encryption is algemene Beschik baarheid in alle open bare Azure-regio's.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Welke gebruikers ervaringen zijn er beschikbaar met Azure Disk Encryption?

Azure Disk Encryption GA ondersteunt Azure Resource Manager sjablonen, Azure PowerShell en Azure CLI. De verschillende gebruikers ervaring bieden u flexibiliteit. Er zijn drie verschillende opties voor het inschakelen van schijf versleuteling voor uw virtuele machines. Zie [Azure Disk Encryption scenario's voor Windows](disk-encryption-windows.md)voor meer informatie over de gebruikers ervaring en stapsgewijze richt lijnen die beschikbaar zijn in azure Disk Encryption.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hoeveel kost het Azure Disk Encryption?

Er worden geen kosten in rekening gebracht voor het versleutelen van VM-schijven met Azure Disk Encryption, maar er zijn kosten verbonden aan het gebruik van Azure Key Vault. Zie de pagina met [Key Vault prijzen](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie over Azure Key Vault kosten.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hoe kan ik beginnen met Azure Disk Encryption?

Lees het [overzicht van Azure Disk Encryption](disk-encryption-overview.md)om aan de slag te gaan.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Welke VM-grootten en besturings systemen ondersteunen Azure Disk Encryption?

Het [overzichts artikel Azure Disk Encryption](disk-encryption-overview.md) bevat een lijst met de [VM-grootten](disk-encryption-overview.md#supported-vms) en [VM-besturings systemen](disk-encryption-overview.md#supported-operating-systems) die ondersteuning bieden voor Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan ik zowel opstart-als gegevens volumes versleutelen met Azure Disk Encryption?

U kunt zowel opstart-als gegevens volumes versleutelen, maar u kunt de gegevens niet versleutelen zonder eerst het volume van het besturings systeem te versleutelen.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan ik een niet-gekoppeld volume versleutelen met Azure Disk Encryption?

Nee, Azure Disk Encryption worden alleen gekoppelde volumes versleuteld.

## <a name="what-is-storage-server-side-encryption"></a>Wat is versleuteling van opslag server zijde?

Versleuteling van de opslag server versleutelt Azure Managed disks in Azure Storage. Beheerde schijven worden standaard versleuteld met versleuteling aan de server zijde met een door het platform beheerde sleutel (vanaf 10 juni 2017). U kunt versleuteling van beheerde schijven met uw eigen sleutels beheren door een door de klant beheerde sleutel op te geven. Zie [server-side Encryption of Azure Managed disks](../disk-encryption.md)(Engelstalig) voor meer informatie.
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Hoe wijkt Azure Disk Encryption af van de versleuteling van opslag server met door de klant beheerde sleutel en wanneer moet ik elke oplossing gebruiken?

Azure Disk Encryption biedt end-to-end-versleuteling voor de besturingssysteem schijf, gegevens schijven en de tijdelijke schijf met een door de klant beheerde sleutel.

- Als uw vereisten het versleutelen van alle bovenstaande en end-to-end-versleuteling bevatten, gebruikt u Azure Disk Encryption. 
- Als uw vereisten het versleutelen van alleen gegevens in rust met door de klant beheerde sleutel bevatten, gebruikt u [versleuteling aan de server zijde met door de klant beheerde sleutels](../disk-encryption.md). U kunt een schijf niet versleutelen met zowel Azure Disk Encryption als opslag server versleuteling met door de klant beheerde sleutels.
- Als u een scenario gebruikt dat wordt genoemd in [niet-ondersteunde scenario's voor Windows](disk-encryption-windows.md#unsupported-scenarios), overweeg dan [Server versleuteling met door de klant beheerde sleutels](../disk-encryption.md). 
- Als het beleid van uw organisatie toestaat dat u inhoud in rust versleutelt met een door Azure beheerde sleutel, is er geen actie vereist: de inhoud wordt standaard versleuteld. Voor beheerde schijven wordt de inhoud in de opslag standaard versleuteld met versleuteling aan de server zijde met door het platform beheerde sleutel. De sleutel wordt beheerd door de Azure Storage-service. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hoe kan ik geheimen of versleutelings sleutels draaien?

Als u geheimen wilt draaien, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijf versleuteling in te scha kelen, waarbij u een andere Key Vault opgeeft. Als u de sleutel versleuteling wilt roteren, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijf versleuteling in te scha kelen, waarbij u de nieuwe sleutel versleuteling opgeeft. 

>[!WARNING]
> - Als u eerder Azure Disk Encryption hebt gebruikt [met Azure AD-App](disk-encryption-windows-aad.md) door Azure AD-referenties op te geven voor het versleutelen van deze VM, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. U kunt Azure Disk Encryption op deze versleutelde VM niet gebruiken omdat dit geen ondersteund scenario is, wat betekent dat het uitschakelen van de AAD-toepassing voor deze versleutelde virtuele machine niet wordt ondersteund.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hoe kan ik een sleutel versleutelings sleutel toevoegen of verwijderen als u deze niet oorspronkelijk hebt gebruikt?

U kunt een sleutel versleutelings sleutel toevoegen door de opdracht inschakelen opnieuw aan te roepen door de para meter Key Encryption Key door te geven. Als u de sleutel versleutelings sleutel wilt verwijderen, roept u de opdracht Enable opnieuw aan zonder de sleutel parameter Key encryption.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Biedt Azure Disk Encryption u de mogelijkheid om uw eigen sleutel (BYOK) te plaatsen?

Ja, u kunt uw eigen sleutel versleutelings sleutels opgeven. Deze sleutels worden beveiligd in Azure Key Vault. Dit is de sleutel opslag voor Azure Disk Encryption. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie over de ondersteunde scenario's voor sleutel versleutelings sleutels.

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Kan ik een door Azure gemaakte sleutel versleutelings sleutel gebruiken?

Ja, u kunt Azure Key Vault een sleutel versleutelings sleutel genereren voor het gebruik van Azure Disk Encryption. Deze sleutels worden beveiligd in Azure Key Vault. Dit is de sleutel opslag voor Azure Disk Encryption. Zie voor meer informatie over de sleutel versleutelings sleutel [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Kan ik een on-premises sleutel beheer service of HSM gebruiken om de versleutelings sleutels te beschermen?

U kunt de on-premises Key Management-service of HSM niet gebruiken om de versleutelings sleutels met Azure Disk Encryption te beveiligen. U kunt de Azure Key Vault-service alleen gebruiken om de versleutelings sleutels te beveiligen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie over de ondersteunings scenario's voor sleutel versleutelings sleutels.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Wat zijn de vereisten voor het configureren van Azure Disk Encryption?

Er zijn vereisten voor het Azure Disk Encryption. Zie het artikel [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md) om een nieuwe sleutel kluis te maken of een bestaande sleutel kluis in te stellen voor toegang tot schijf versleuteling om versleuteling in te scha kelen en geheimen en sleutels te beveiligen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption](disk-encryption-key-vault.md)voor meer informatie over de ondersteunings scenario's voor sleutel versleutelings sleutels.

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Wat zijn de vereisten voor het configureren van Azure Disk Encryption met een Azure AD-app (vorige versie)?

Er zijn vereisten voor het Azure Disk Encryption. Zie de [Azure Disk Encryption met Azure AD](disk-encryption-windows-aad.md) -inhoud om een Azure Active Directory toepassing te maken, een nieuwe sleutel kluis te maken of een bestaande sleutel kluis in te stellen voor toegang tot schijf versleuteling om versleuteling in te scha kelen en geheimen en sleutels te beveiligen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD](disk-encryption-key-vault-aad.md)voor meer informatie over de ondersteunings scenario's voor sleutel versleutelings sleutels.

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Wordt Azure Disk Encryption het gebruik van een Azure AD-app (eerdere versie) nog steeds ondersteund?
Ja. Schijf versleuteling met een Azure AD-app wordt nog steeds ondersteund. Bij het versleutelen van nieuwe Vm's is het echter raadzaam de nieuwe methode te gebruiken in plaats van te versleutelen met een Azure AD-app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan ik Vm's die zijn versleuteld met een Azure AD-app, migreren naar versleuteling zonder Azure AD-app?
  Op dit moment is er geen direct migratie traject voor computers die zijn versleuteld met een Azure AD-App voor versleuteling zonder Azure AD-app. Daarnaast is er geen direct pad van versleuteling zonder dat een Azure AD-app kan worden versleuteld met een AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Welke versie van Azure PowerShell ondersteunt Azure Disk Encryption?

Gebruik de nieuwste versie van de Azure PowerShell SDK om Azure Disk Encryption te configureren. Down load de nieuwste versie van [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption wordt *niet* ondersteund door de Azure SDK-versie 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Wat is de schijf "bek volume" of "/mnt/azure_bek_disk"?

Het ' bek-volume ' is een lokaal gegevens volume waarmee de versleutelings sleutels voor versleutelde Azure-Vm's veilig worden opgeslagen.

> [!NOTE]
> Geen inhoud op deze schijf verwijderen of bewerken. Ontkoppel de schijf niet, aangezien de aanwezigheid van de versleutelings sleutel nodig is voor versleutelings bewerkingen op de IaaS-VM.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Welke versleutelings methode Azure Disk Encryption gebruiken?

Azure Disk Encryption selecteert de versleutelings methode in BitLocker op basis van de volgende versie van Windows:

| Windows-versies                 | Versie | Versleutelings methode        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 of hoger  | >= 1511 |XTS-AES 256-bits           |
| Windows Server 2012, Windows 8, 8,1, 10 | < 1511 |AES 256 bits *              |
| Windows Server-2008R2            |        |AES 256-bit met diffusor |

\* AES 256 bits met diffuser wordt niet ondersteund in Windows 2012 en hoger.

Als u de versie van het Windows-besturings systeem wilt bepalen, voert u het hulp programma winver uit in de virtuele machine.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan ik een back-up maken van een versleutelde VM en deze herstellen? 

Azure Backup biedt een mechanisme voor het maken van back-ups en het herstellen van versleutelde VM'S binnen hetzelfde abonnement en dezelfde regio.  Zie [back-up en herstel van versleutelde virtuele machines met Azure backup](../../backup/backup-azure-vms-encryption.md)voor instructies.  Het herstellen van een versleutelde VM naar een andere regio wordt momenteel niet ondersteund.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Waar kan ik vragen stellen of feedback geven?

U kunt vragen stellen of feedback geven op de [pagina micro soft Q&een vraag voor Azure Disk Encryption](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u meer geleerd over de meest voorkomende vragen met betrekking tot Azure Disk Encryption. Raadpleeg de volgende artikelen voor meer informatie over deze service:

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Schijf versleuteling Toep assen in Azure Security Center](../../security-center/asset-inventory.md)
- [Versleuteling van inactieve gegevens in Azure](../../security/fundamentals/encryption-atrest.md)
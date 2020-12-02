---
title: Veelgestelde vragen-Azure Disk Encryption voor Linux-Vm's
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Microsoft Azure schijf versleuteling voor virtuele Linux IaaS-machines.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: 9d62195759de2d43e863c1cb2c4c3ef911bb52c6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498520"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Veelgestelde vragen over Azure Disk Encryption voor virtuele Linux-machines

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Disk Encryption voor virtuele Linux-machines (Vm's). Zie [Azure Disk Encryption Overview](disk-encryption-overview.md)voor meer informatie over deze service.

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Wat is Azure Disk Encryption voor virtuele Linux-machines?

Azure Disk Encryption voor Linux-Vm's maakt gebruik van de DM-cryptografie functie van Linux om een volledige schijf versleuteling te bieden van de besturingssysteem schijf * en gegevens schijven. Daarnaast biedt het een versleuteling van de tijdelijke schijf wanneer de [functie EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)wordt gebruikt. De inhouds stromen zijn versleuteld van de virtuele machine naar de back-end van de opslag. Het leveren van end-to-end-versleuteling met een door de klant beheerde sleutel.
 
Zie [ondersteunde vm's en besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Waar wordt Azure Disk Encryption in algemene Beschik baarheid?

Azure Disk Encryption voor Linux-Vm's is in algemene Beschik baarheid in alle open bare Azure-regio's.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Welke gebruikers ervaringen zijn er beschikbaar met Azure Disk Encryption?

Azure Disk Encryption GA ondersteunt Azure Resource Manager sjablonen, Azure PowerShell en Azure CLI. De verschillende gebruikers ervaring bieden u flexibiliteit. Er zijn drie verschillende opties voor het inschakelen van schijf versleuteling voor uw virtuele machines. Zie [Azure Disk Encryption scenario's voor Linux](disk-encryption-linux.md)voor meer informatie over de gebruikers ervaring en stapsgewijze richt lijnen die beschikbaar zijn in azure Disk Encryption.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Hoeveel kost het Azure Disk Encryption?

Er worden geen kosten in rekening gebracht voor het versleutelen van VM-schijven met Azure Disk Encryption, maar er zijn kosten verbonden aan het gebruik van Azure Key Vault. Zie de pagina met [Key Vault prijzen](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie over Azure Key Vault kosten.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hoe kan ik beginnen met Azure Disk Encryption?

Lees het [overzicht van Azure Disk Encryption](disk-encryption-overview.md)om aan de slag te gaan.

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Welke VM-grootten en besturings systemen ondersteunen Azure Disk Encryption?

Het [overzichts artikel Azure Disk Encryption](disk-encryption-overview.md) bevat een lijst met de [VM-grootten](disk-encryption-overview.md#supported-vms) en [VM-besturings systemen](disk-encryption-overview.md#supported-operating-systems) die ondersteuning bieden voor Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Kan ik zowel opstart-als gegevens volumes versleutelen met Azure Disk Encryption?

Ja, u kunt zowel opstart-als gegevens volumes versleutelen, of u kunt het gegevens volume versleutelen zonder eerst het volume van het besturings systeem te hoeven versleutelen. 

Nadat u het volume van het besturings systeem hebt versleuteld, wordt het uitschakelen van versleuteling op het volume van het besturings systeem niet ondersteund. Voor Linux-Vm's in een schaalset kan alleen het gegevens volume worden versleuteld.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Kan ik een niet-gekoppeld volume versleutelen met Azure Disk Encryption?

Nee, Azure Disk Encryption worden alleen gekoppelde volumes versleuteld.

## <a name="what-is-storage-server-side-encryption"></a>Wat is versleuteling van opslag server zijde?

Versleuteling van de opslag server versleutelt Azure Managed disks in Azure Storage. Beheerde schijven worden standaard versleuteld met versleuteling aan de server zijde met een door het platform beheerde sleutel (vanaf 10 juni 2017). U kunt versleuteling van beheerde schijven met uw eigen sleutels beheren door een door de klant beheerde sleutel op te geven. Zie voor meer informatie: [versleuteling aan de server zijde van Azure Managed disks](../disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Hoe wijkt Azure Disk Encryption af van de versleuteling van opslag server met door de klant beheerde sleutel en wanneer moet ik elke oplossing gebruiken?

Azure Disk Encryption biedt end-to-end-versleuteling voor de besturingssysteem schijf, gegevens schijven en de tijdelijke schijf, met behulp van een door de klant beheerde sleutel.
- Als uw vereisten het versleutelen van alle bovenstaande en end-to-end-versleuteling bevatten, gebruikt u Azure Disk Encryption. 
- Als uw vereisten het versleutelen van alleen gegevens in rust met door de klant beheerde sleutel bevatten, gebruikt u [versleuteling aan de server zijde met door de klant beheerde sleutels](../disk-encryption.md). U kunt een schijf niet versleutelen met zowel Azure Disk Encryption als opslag server versleuteling met door de klant beheerde sleutels. 
- Als uw Linux-distributie niet wordt vermeld onder [ondersteunde besturings systemen voor Azure Disk Encryption](disk-encryption-overview.md#supported-operating-systems) of als u een scenario gebruikt dat wordt genoemd in de [niet-ondersteunde scenario's voor Windows](disk-encryption-linux.md#unsupported-scenarios), overweegt u [versleuteling aan de server zijde met door de klant beheerde sleutels](../disk-encryption.md).
- Als het beleid van uw organisatie toestaat dat u inhoud in rust versleutelt met een door Azure beheerde sleutel, is er geen actie vereist: de inhoud wordt standaard versleuteld. Voor beheerde schijven wordt de inhoud in de opslag standaard versleuteld met versleuteling aan de server zijde met door het platform beheerde sleutel. De sleutel wordt beheerd door de Azure Storage-service. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hoe kan ik geheimen of versleutelings sleutels draaien?

Als u geheimen wilt draaien, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijf versleuteling in te scha kelen, waarbij u een andere Key Vault opgeeft. Als u de sleutel versleuteling wilt roteren, roept u dezelfde opdracht aan die u oorspronkelijk hebt gebruikt om schijf versleuteling in te scha kelen, waarbij u de nieuwe sleutel versleuteling opgeeft. 

>[!WARNING]
> - Als u eerder Azure Disk Encryption hebt gebruikt [met Azure AD-App](disk-encryption-linux-aad.md) door Azure AD-referenties op te geven voor het versleutelen van deze VM, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. U kunt Azure Disk Encryption op deze versleutelde VM niet gebruiken omdat dit geen ondersteund scenario is, wat betekent dat het uitschakelen van de AAD-toepassing voor deze versleutelde virtuele machine niet wordt ondersteund.

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

Er zijn vereisten voor het Azure Disk Encryption. Zie de [Azure Disk Encryption met Azure AD](disk-encryption-linux-aad.md) -inhoud om een Azure Active Directory toepassing te maken, een nieuwe sleutel kluis te maken of een bestaande sleutel kluis in te stellen voor toegang tot schijf versleuteling om versleuteling in te scha kelen en geheimen en sleutels te beveiligen. Zie [een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD](disk-encryption-key-vault-aad.md)voor meer informatie over de ondersteunings scenario's voor sleutel versleutelings sleutels.

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Wordt Azure Disk Encryption het gebruik van een Azure AD-app (eerdere versie) nog steeds ondersteund?
Ja. Schijf versleuteling met een Azure AD-app wordt nog steeds ondersteund. Bij het versleutelen van nieuwe Vm's is het echter raadzaam de nieuwe methode te gebruiken in plaats van te versleutelen met een Azure AD-app. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Kan ik Vm's die zijn versleuteld met een Azure AD-app, migreren naar versleuteling zonder Azure AD-app?
  Op dit moment is er geen direct migratie traject voor computers die zijn versleuteld met een Azure AD-App voor versleuteling zonder Azure AD-app. Daarnaast is er geen direct pad van versleuteling zonder dat een Azure AD-app kan worden versleuteld met een AD-app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Welke versie van Azure PowerShell ondersteunt Azure Disk Encryption?

Gebruik de nieuwste versie van de Azure PowerShell SDK om Azure Disk Encryption te configureren. Down load de nieuwste versie van [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Azure Disk Encryption wordt *niet* ondersteund door de Azure SDK-versie 1.1.0.

> [!NOTE]
> De voorbeeld uitbreiding micro soft. OSTCExtension. AzureDiskEncryptionForLinux van Linux Azure Disk Encryption is afgeschaft. Deze uitbrei ding is gepubliceerd voor de preview-versie van Azure Disk Encryption. Gebruik de preview-versie van de uitbrei ding niet in uw test-of productie-implementatie.

> Voor implementatie scenario's als Azure Resource Manager (ARM), waar u de Azure Disk Encryption-extensie voor Linux VM moet implementeren om versleuteling in te scha kelen op uw Linux IaaS VM, moet u de Azure Disk Encryption-extensie productie ondersteund ' micro soft. Azure. Security. AzureDiskEncryptionForLinux ' gebruiken.

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>Kan ik Azure Disk Encryption Toep assen op mijn aangepaste Linux-installatie kopie?

U kunt Azure Disk Encryption niet Toep assen op uw aangepaste Linux-installatie kopie. Alleen de galerie Linux-installatie kopieën voor de ondersteunde distributies die eerder worden aangeroepen, worden ondersteund. Aangepaste Linux-installatie kopieën worden momenteel niet ondersteund.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>Kan ik updates Toep assen op een Linux Red Hat VM die gebruikmaakt van de yum-update?

Ja, u kunt een yum-update uitvoeren op een Red Hat Linux-VM.  Zie [Azure Disk Encryption op een geïsoleerd netwerk](disk-encryption-isolated-network.md)voor meer informatie.

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Wat is de aanbevolen werk stroom voor Azure Disk Encryption voor Linux?

De volgende werk stroom wordt aanbevolen voor de beste resultaten op Linux:
* Beginnen met de niet-gewijzigde aandelen galerie-afbeelding die overeenkomt met de nood zakelijke distributie en-versie van het besturings systeem
* Maak een back-up van gekoppelde stations die worden versleuteld.  Met deze back-up kan herstel worden uitgevoerd als er een fout is opgetreden, bijvoorbeeld als de virtuele machine opnieuw wordt opgestart voordat de versleuteling is voltooid.
* Versleutelen (kan enkele uren of zelfs dagen duren, afhankelijk van de VM-kenmerken en de grootte van gekoppelde gegevens schijven)
* Pas zo nodig software aan de installatie kopie toe en voeg deze toe.

Als deze werk stroom niet mogelijk is, is het afhankelijk van [Storage service Encryption](../../storage/common/storage-service-encryption.md) (SSE) op de laag van het platform opslag account mogelijk een alternatief voor de volledige schijf versleuteling met behulp van DM-cryptografie.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Wat is de schijf "bek volume" of "/mnt/azure_bek_disk"?

Het ' bek-volume ' is een lokaal gegevens volume waarmee de versleutelings sleutels voor versleutelde Azure-Vm's veilig worden opgeslagen.
> [!NOTE]
> Geen inhoud op deze schijf verwijderen of bewerken. Ontkoppel de schijf niet, aangezien de aanwezigheid van de versleutelings sleutel nodig is voor versleutelings bewerkingen op de IaaS-VM.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Welke versleutelings methode Azure Disk Encryption gebruiken?

Azure Disk Encryption maakt gebruik van de ontsleutelen standaard van AES-XTS-plain64 met een 256-bits volume hoofd sleutel.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Als ik EncryptFormatAll gebruik en alle volume typen Specificeer, worden de gegevens op de gegevens stations die al zijn versleuteld, gewist?
Nee, er worden geen gegevens gewist van gegevens stations die al zijn versleuteld met Azure Disk Encryption. Net als bij de manier waarop EncryptFormatAll het OS-station niet opnieuw versleutelt, wordt het al versleutelde gegevens station niet opnieuw versleuteld. Zie de [EncryptFormatAll-criteria](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)voor meer informatie.        

## <a name="is-xfs-filesystem-supported"></a>Wordt XFS-bestands systeem ondersteund?
Versleuteling van XFS-besturingssysteem schijven wordt ondersteund.

Versleuteling van XFS-gegevens schijven wordt alleen ondersteund als de para meter EncryptFormatAll wordt gebruikt. Hiermee wordt het volume opnieuw geformatteerd en worden alle eerder aanwezige gegevens gewist. Zie de [EncryptFormatAll-criteria](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)voor meer informatie.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Kan ik een back-up maken van een versleutelde VM en deze herstellen? 

Azure Backup biedt een mechanisme voor het maken van back-ups en het herstellen van versleutelde VM'S binnen hetzelfde abonnement en dezelfde regio.  Zie [back-up en herstel van versleutelde virtuele machines met Azure backup](../../backup/backup-azure-vms-encryption.md)voor instructies.  Het herstellen van een versleutelde VM naar een andere regio wordt momenteel niet ondersteund.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Waar kan ik vragen stellen of feedback geven?

U kunt vragen stellen of feedback geven op de [pagina micro soft Q&een vraag voor Azure Disk Encryption](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u meer geleerd over de meest voorkomende vragen met betrekking tot Azure Disk Encryption. Raadpleeg de volgende artikelen voor meer informatie over deze service:

- [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)
- [Schijf versleuteling Toep assen in Azure Security Center](../../security-center/asset-inventory.md)
- [Versleuteling van inactieve gegevens in Azure](../../security/fundamentals/encryption-atrest.md)
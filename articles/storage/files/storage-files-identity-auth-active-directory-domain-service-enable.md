---
title: Azure AD Domain Services gebruiken om toegang tot bestands gegevens via SMB te autoriseren
description: Meer informatie over het inschakelen van verificatie op basis van identiteit via SMB (Server Message Block) voor Azure Files via Azure Active Directory Domain Services. Uw virtuele Windows-machines (Vm's) die lid zijn van een domein, kunnen vervolgens toegang krijgen tot Azure-bestands shares met behulp van Azure AD-referenties.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: d75b60c715a758684e6f3a4b331c0b12d17eaf21
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705106"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Active Directory Domain Services authenticatie inschakelen op Azure Files

[Azure files](storage-files-introduction.md)   ondersteunt op identiteit gebaseerde verificatie via Server Message Block (SMB) via twee typen Domain Services: on-premises Active Directory Domain Services (AD DS) en Azure Active Directory Domain Services (Azure AD DS). We raden u ten zeerste aan de [sectie How it Works](./storage-files-active-directory-overview.md#how-it-works) te bekijken om de juiste domein service voor verificatie te selecteren. De installatie verschilt afhankelijk van de domein service die u kiest. Dit artikel richt zich op het inschakelen en configureren van Azure AD DS voor verificatie met Azure-bestands shares.

Als u geen ervaring hebt met Azure-bestands shares, raden we u aan onze [plannings handleiding](storage-files-planning.md) te lezen voordat u de volgende serie artikelen leest.

> [!NOTE]
> Azure Files ondersteunt Kerberos-verificatie met Azure AD DS alleen met RC4-HMAC. AES Kerberos-versleuteling wordt nog niet ondersteund.
> Azure Files ondersteunt verificatie voor Azure AD DS met volledige synchronisatie met Azure AD. Als u scoped Synchronization hebt ingeschakeld in azure AD DS dat alleen een beperkt aantal identiteiten vanuit Azure AD synchroniseert, wordt verificatie en autorisatie niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voordat u Azure AD over SMB voor Azure-bestands shares inschakelt, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid:

1.  **Selecteer of maak een Azure AD-Tenant.**

    U kunt een nieuwe of bestaande Tenant voor Azure AD-verificatie via SMB gebruiken. De Tenant en de bestands share die u wilt openen, moeten zijn gekoppeld aan hetzelfde abonnement.

    Als u een nieuwe Azure AD-Tenant wilt maken, kunt u [een Azure AD-Tenant en een Azure AD-abonnement toevoegen](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Als u een bestaande Azure AD-Tenant hebt, maar een nieuwe Tenant wilt maken voor gebruik met Azure-bestands shares, raadpleegt u [een Azure Active Directory-Tenant maken](/rest/api/datacatalog/create-an-azure-active-directory-tenant).

1.  **Schakel Azure AD Domain Services in voor de Azure AD-Tenant.**

    Als u verificatie met Azure AD-referenties wilt ondersteunen, moet u Azure AD Domain Services inschakelen voor uw Azure AD-Tenant. Als u niet de beheerder van de Azure AD-Tenant bent, neemt u contact op met de beheerder en volgt u de stapsgewijze richt lijnen om Azure Active Directory Domain Services in te [scha kelen met behulp van de Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Het duurt doorgaans ongeveer 15 minuten voordat een Azure AD DS-implementatie is voltooid. Controleer of de status van de Azure-AD DS **wordt weer gegeven**, waarbij de wachtwoord hash-synchronisatie is ingeschakeld, voordat u doorgaat met de volgende stap.

1.  **Domein-lid worden van een Azure-VM met Azure AD DS.**

    Als u toegang wilt krijgen tot een bestands share via Azure AD-referenties van een virtuele machine, moet uw virtuele machine lid zijn van een domein met Azure AD DS. Zie [een virtuele machine van Windows Server toevoegen aan een beheerd domein](../../active-directory-domain-services/join-windows-vm.md)voor meer informatie over het toevoegen van een domein aan een VM.

    > [!NOTE]
    > Azure AD DS-verificatie via SMB met Azure-bestands shares wordt alleen ondersteund op virtuele Azure-machines die worden uitgevoerd op versies van het besturings systeem die ouder zijn dan Windows 7 of Windows Server 2008 R2.

1.  **Selecteer of maak een Azure-bestands share.**

    Selecteer een nieuwe of bestaande bestands share die is gekoppeld aan hetzelfde abonnement als uw Azure AD-Tenant. Zie [een bestands share maken in azure files](storage-how-to-create-file-share.md)voor meer informatie over het maken van een nieuwe bestands share.
    Voor optimale prestaties raden we aan dat uw bestands share zich in dezelfde regio bevinden als de VM waarvan u de share wilt openen.

1.  **Controleer Azure Files connectiviteit door Azure-bestands shares te koppelen met behulp van de sleutel van het opslag account.**

    Als u wilt controleren of de virtuele machine en de bestands share correct zijn geconfigureerd, kunt u de bestands share koppelen met de sleutel van het opslag account. Zie [een Azure-bestands share koppelen en de share openen in Windows](storage-how-to-use-files-windows.md)voor meer informatie.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Azure Files verificatie met Azure AD DS is beschikbaar in [alle regio's van Azure open bare, gov en China](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview-of-the-workflow"></a>Overzicht van de werk stroom

Voordat u Azure AD DS-verificatie via SMB voor Azure-bestands shares inschakelt, moet u controleren of uw Azure AD-en Azure Storage-omgevingen op de juiste wijze zijn geconfigureerd. U kunt het beste de [vereisten](#prerequisites) door lopen om ervoor te zorgen dat u alle vereiste stappen hebt voltooid.

Ga vervolgens als volgt te werk om toegang te verlenen tot Azure Files resources met Azure AD-referenties:

1. Schakel Azure AD DS-verificatie via SMB in voor uw opslag account om het opslag account te registreren bij de bijbehorende Azure AD DS-implementatie.
2. Toegangs machtigingen voor een share toewijzen aan een Azure AD-identiteit (een gebruiker, groep of Service-Principal).
3. Configureer NTFS-machtigingen via SMB voor mappen en bestanden.
4. Een Azure-bestands share koppelen vanaf een virtuele machine die lid is van een domein.

In het volgende diagram ziet u de end-to-end werk stroom voor het inschakelen van Azure AD DS-verificatie via SMB voor Azure Files.

![Diagram van Azure AD over SMB voor Azure Files werk stroom](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Azure AD DS-verificatie inschakelen voor uw account

Als u Azure AD DS-verificatie via SMB voor Azure Files wilt inschakelen, kunt u een eigenschap instellen voor opslag accounts met behulp van de Azure Portal, de Azure PowerShell of de Azure CLI. Als u deze eigenschap impliciet instelt, wordt het opslag account gekoppeld aan de gekoppelde Azure AD DS-implementatie. Azure AD DS-verificatie via SMB wordt vervolgens ingeschakeld voor alle nieuwe en bestaande bestands shares in het opslag account.

Denk eraan dat u Azure AD DS-verificatie via SMB pas kunt inschakelen nadat u Azure AD DS hebt geïmplementeerd in uw Azure AD-Tenant. Zie de [vereisten](#prerequisites)voor meer informatie.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Voer de volgende stappen uit om Azure AD DS-verificatie via SMB met de [Azure Portal](https://portal.azure.com)in te scha kelen:

1. Ga in het Azure Portal naar uw bestaande opslag account of [Maak een opslag account](../common/storage-account-create.md).
1. Selecteer in de sectie **instellingen** de optie **configuratie**.
1. Onder op **identiteit gebaseerde toegang voor bestands shares** schakelt u de wissel knop voor **Azure Active Directory domein service (Aad DS)** in op **ingeschakeld**.
1. Selecteer **Opslaan**.

In de volgende afbeelding ziet u hoe u Azure AD DS-verificatie via SMB inschakelt voor uw opslag account.

![Schakel Azure AD DS-verificatie via SMB in het Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u Azure AD DS-verificatie via SMB met Azure PowerShell wilt inschakelen, installeert u de nieuwste AZ-module (2,4 of hoger) of de AZ. Storage-module (1,5 of hoger). Zie [Azure PowerShell installeren op Windows met PowerShellGet](/powershell/azure/install-Az-ps)voor meer informatie over het installeren van Power shell.

Als u een nieuw opslag account wilt maken, roept u [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)aan en stelt u de para meter **EnableAzureActiveDirectoryDomainServicesForFile** in op **True**. In het volgende voor beeld moet u de waarden voor de tijdelijke aanduiding vervangen door uw eigen waarden. (Als u de vorige preview-module gebruikt, is de para meter voor het inschakelen van de functie **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Gebruik de volgende opdracht om deze functie in te scha kelen op bestaande opslag accounts:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure AD-verificatie via SMB wilt inschakelen met Azure CLI, installeert u de nieuwste CLI-versie (versie 2.0.70 of nieuwer). Zie [de Azure cli installeren](/cli/azure/install-azure-cli?view=azure-cli-latest)voor meer informatie over het installeren van Azure cli.

Als u een nieuw opslag account wilt maken, roept u [AZ Storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)aan en stelt `--enable-files-aadds` u de eigenschap in op **True**. In het volgende voor beeld moet u de waarden voor de tijdelijke aanduiding vervangen door uw eigen waarden. (Als u de vorige preview-module gebruikt, is de para meter voor functie activering **bestand-Aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Gebruik de volgende opdracht om deze functie in te scha kelen op bestaande opslag accounts:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

U hebt Azure AD DS-verificatie via SMB ingeschakeld en er is een aangepaste rol toegewezen die toegang biedt tot een Azure-bestands share met een Azure AD-identiteit. Als u extra gebruikers toegang wilt verlenen tot uw bestands share, volgt u de instructies in de [toegangs machtigingen toewijzen](#assign-access-permissions-to-an-identity) voor het gebruik van een identiteit en [NTFS-machtigingen configureren via SMB-secties](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Files en het gebruik van Azure AD via SMB:

- [Overview of Azure Files identity-based authentication support for SMB access](storage-files-active-directory-overview.md) (Overzicht van ondersteuning voor verificatie op basis van identiteiten van Azure Files voor SMB-toegang)
- [Veelgestelde vragen](storage-files-faq.md)

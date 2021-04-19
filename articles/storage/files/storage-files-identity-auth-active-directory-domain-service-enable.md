---
title: Gebruik Azure AD Domain Services om toegang tot bestandsgegevens via SMB toe te staan
description: Meer informatie over het inschakelen van verificatie op basis van identiteit via Server Message Block (SMB) voor Azure Files via Azure Active Directory Domain Services. Uw virtuele Windows-machines (VM's) die lid zijn van een domein, hebben vervolgens toegang tot Azure-bestands shares met behulp van Azure AD-referenties.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: df2bd1c12c86de43e2a5057813a743d822dbda33
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718341"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Verificatie Azure Active Directory Domain Services inschakelen op Azure Files

[Azure Files](storage-files-introduction.md)   ondersteunt verificatie op basis van identiteiten via Server Message Block (SMB) via twee typen Domeinservices: on-premises Active Directory Domain Services (AD DS) en Azure Active Directory Domain Services (Azure AD DS). We raden u ten zeerste aan de sectie [Hoe werkt het te bekijken om](./storage-files-active-directory-overview.md#how-it-works) de juiste domeinservice voor verificatie te selecteren. De installatie is anders, afhankelijk van de domeinservice die u kiest. Dit artikel is gericht op het inschakelen en configureren van Azure AD DS voor verificatie met Azure-bestands shares.

Als u geen tijd hebt voor Azure-bestands shares, raden we u aan onze planningshandleiding te [lezen](storage-files-planning.md) voordat u de volgende reeks artikelen leest.

> [!NOTE]
> Azure Files ondersteunt Kerberos-verificatie met alleen Azure AD DS RC4-HMAC. AES Kerberos-versleuteling wordt nog niet ondersteund.
> Azure Files ondersteunt verificatie voor Azure AD DS met volledige synchronisatie met Azure AD. Als u synchronisatie binnen bereik hebt ingeschakeld in Azure AD DS waarmee alleen een beperkte set identiteiten van Azure AD wordt gesynchroniseerd, worden verificatie en autorisatie niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voordat u Azure AD via SMB inschakelen voor Azure-bestands shares, moet u ervoor zorgen dat u aan de volgende vereisten hebt voltooid:

1.  **Selecteer of maak een Azure AD-tenant.**

    U kunt een nieuwe of bestaande tenant gebruiken voor Azure AD-verificatie via SMB. De tenant en de bestands share die u wilt openen, moeten zijn gekoppeld aan hetzelfde abonnement.

    Als u een nieuwe Azure AD-tenant wilt maken, kunt u [een Azure AD-tenant en een Azure AD-abonnement toevoegen.](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Als u een bestaande Azure AD-tenant hebt maar een nieuwe tenant wilt maken voor gebruik met Azure-bestands shares, zie Een tenant [Azure Active Directory maken.](/rest/api/datacatalog/create-an-azure-active-directory-tenant)

1.  **Schakel Azure AD Domain Services in op de Azure AD-tenant.**

    Als u verificatie wilt ondersteunen met Azure AD-referenties, moet u Azure AD Domain Services voor uw Azure AD-tenant. Als u niet de beheerder van de Azure AD-tenant bent, neem dan contact op met de beheerder en volg de stapsgewijs richtlijnen voor het inschakelen van Azure Active Directory Domain Services met behulp van [Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Het duurt doorgaans ongeveer 15 minuten voordat een Azure AD DS is voltooid. Controleer of in de status van Azure AD DS wordt **uitgevoerd,** met wachtwoordhashsynchronisatie ingeschakeld, voordat u doorgaat met de volgende stap.

1.  **Domein toevoegen aan een Azure-VM met Azure AD DS.**

    Als u toegang wilt krijgen tot een bestands share met behulp van Azure AD-referenties vanaf een VM, moet uw VM lid zijn van een Azure AD DS. Zie Een virtuele [Windows Server-machine](../../active-directory-domain-services/join-windows-vm.md)toevoegen aan een beheerd domein voor meer informatie over het toevoegen van een VM aan een domein.

    > [!NOTE]
    > Azure AD DS via SMB met Azure-bestands shares wordt alleen ondersteund op Azure-VM's met besturingssysteemversies boven Windows 7 of Windows Server 2008 R2.

1.  **Selecteer of maak een Azure-bestands share.**

    Selecteer een nieuwe of bestaande bestands share die is gekoppeld aan hetzelfde abonnement als uw Azure AD-tenant. Zie Een bestands share maken in Azure Files voor meer informatie over het maken [van een nieuwe Azure Files.](storage-how-to-create-file-share.md)
    Voor optimale prestaties wordt u aangeraden dat uw bestands share zich in dezelfde regio als de VM van waaruit u toegang wilt krijgen tot de share.

1.  **Controleer Azure Files connectiviteit door Azure-bestands shares te verbinden met behulp van de sleutel van uw opslagaccount.**

    Als u wilt controleren of uw VM en bestands share correct zijn geconfigureerd, probeert u de bestands share te maken met behulp van de sleutel van uw opslagaccount. Zie Een [Azure-bestands share toevoegen en toegang krijgen tot de share in Windows voor meer informatie.](storage-how-to-use-files-windows.md)

## <a name="regional-availability"></a>Regionale beschikbaarheid

Azure Files verificatie met Azure AD DS is beschikbaar in alle [openbare Azure-, Gov- en China-regio's.](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="overview-of-the-workflow"></a>Overzicht van de werkstroom

Voordat u verificatie Azure AD DS via SMB inschakelen voor Azure-bestands shares, moet u controleren of uw Azure AD- en Azure Storage goed zijn geconfigureerd. U wordt aangeraden de vereisten door [te nemen om](#prerequisites) ervoor te zorgen dat u alle vereiste stappen hebt voltooid.

Ga vervolgens als volgt te werk om toegang te verlenen tot Azure Files resources met Azure AD-referenties:

1. Schakel Azure AD DS via SMB in voor uw opslagaccount om het opslagaccount te registreren bij de gekoppelde Azure AD DS implementatie.
2. Wijs toegangsmachtigingen voor een share toe aan een Azure AD-identiteit (een gebruiker, groep of service-principal).
3. Configureer NTFS-machtigingen via SMB voor mappen en bestanden.
4. Een Azure-bestands share vanaf een domein-VM aan een domein toevoegen.

Het volgende diagram illustreert de end-to-end-werkstroom voor het inschakelen Azure AD DS verificatie via SMB voor Azure Files.

![Diagram met Azure AD via SMB voor Azure Files werkstroom](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Verificatie Azure AD DS uw account inschakelen

Als u Azure AD DS via SMB voor Azure Files wilt inschakelen, kunt u een eigenschap voor opslagaccounts instellen met behulp van de Azure Portal, Azure PowerShell of Azure CLI. Als u deze eigenschap impliciet 'domein-joins' in het opslagaccount instelt met de bijbehorende Azure AD DS implementatie. Azure AD DS via SMB wordt vervolgens ingeschakeld voor alle nieuwe en bestaande bestands shares in het opslagaccount.

Houd er rekening mee dat u verificatie Azure AD DS via SMB pas kunt inschakelen nadat u de Azure AD DS uw Azure AD-tenant hebt geïmplementeerd. Zie de vereisten voor [meer informatie.](#prerequisites)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Als u Azure AD DS via SMB wilt inschakelen met de [Azure Portal,](https://portal.azure.com)volgt u deze stappen:

1. Ga in Azure Portal naar uw bestaande opslagaccount of [maak een opslagaccount.](../common/storage-account-create.md)
1. Selecteer configuratie **in** de sectie **Instellingen.**
1. Schakel **onder Op identiteit gebaseerde toegang voor bestands** shares de wisselknop voor Azure Active Directory Domain Service **(AAD DS) in** Ingeschakeld **in.**
1. Selecteer **Opslaan**.

In de volgende afbeelding ziet u hoe u Azure AD DS via SMB kunt inschakelen voor uw opslagaccount.

:::image type="content" source="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png" alt-text="Schermopname van de configuratieblade in uw opslagaccount, Azure Active Directory Doman Services is ingeschakeld." lightbox="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u Azure AD DS via SMB met Azure PowerShell wilt inschakelen, installeert u de nieuwste Az-module (2.4 of nieuwer) of de Az.Storage-module (1.5 of nieuwer). Zie Install Azure PowerShell on Windows [with PowerShellGet (Windows](/powershell/azure/install-Az-ps)installeren met PowerShellGet) voor meer informatie over het installeren van PowerShell.

Als u een nieuw opslagaccount wilt maken, roept u [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)aan en stelt u de parameter **EnableAzureActiveDirectoryDomainServicesForFile** in op **true**. In het volgende voorbeeld moet u de waarden van de tijdelijke aanduidingen vervangen door uw eigen waarden. (Als u de vorige preview-module gebruikt, is **EnableAzureFilesAadIntegrationForSMB** de parameter voor het inschakelen van de functie.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Als u deze functie wilt inschakelen voor bestaande opslagaccounts, gebruikt u de volgende opdracht:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u Azure AD-verificatie via SMB met Azure CLI wilt inschakelen, installeert u de nieuwste CLI-versie (versie 2.0.70 of hoger). Zie Azure CLI installeren voor meer informatie over het [installeren van Azure CLI.](/cli/azure/install-azure-cli)

Als u een nieuw opslagaccount wilt maken, roept [u az storage account create aan](/cli/azure/storage/account#az-storage-account-create)en stelt u de eigenschap in op `--enable-files-aadds` **true**. In het volgende voorbeeld moet u de waarden van de tijdelijke aanduidingen vervangen door uw eigen waarden. (Als u de vorige preview-module hebt gebruikt, is **file-aad** de parameter voor het inschakelen van functies.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Als u deze functie wilt inschakelen voor bestaande opslagaccounts, gebruikt u de volgende opdracht:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

U hebt nu verificatie via Azure AD DS via SMB ingeschakeld en een aangepaste rol toegewezen die toegang biedt tot een Azure-bestands share met een Azure AD-identiteit. Volg de instructies in de [secties Toegangsmachtigingen](#assign-access-permissions-to-an-identity) toewijzen voor het gebruik van een identiteit en [NTFS-machtigingen](#configure-ntfs-permissions-over-smb)configureren via SMB om extra gebruikers toegang te verlenen tot uw bestands share.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources Azure Files informatie over het gebruik van Azure AD via SMB:

- [Overview of Azure Files identity-based authentication support for SMB access](storage-files-active-directory-overview.md) (Overzicht van ondersteuning voor verificatie op basis van identiteiten van Azure Files voor SMB-toegang)
- [Veelgestelde vragen](storage-files-faq.md)

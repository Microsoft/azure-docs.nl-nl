---
title: Azure Blob-opslag koppelen met behulp van het NFS 3,0-protocol (preview) | Microsoft Docs
description: Meer informatie over het koppelen van een container in Blob Storage vanaf een virtuele Azure-machine (VM) of een client die on-premises wordt uitgevoerd met behulp van het NFS 3,0-protocol.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b61ce696c28a2c72a2cd3d0eb2d2fde0022dbb01
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897736"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Blob Storage koppelen met behulp van het NFS-protocol (Network File System) 3,0 (preview)

U kunt een container in Blob Storage koppelen vanaf een op Windows of Linux gebaseerde Azure virtual machine (VM) of een Windows-of Linux-systeem dat on-premises wordt uitgevoerd met het NFS 3,0-protocol. In dit artikel vindt u stapsgewijze richt lijnen. Zie [Network File System (NFS) 3,0-protocol ondersteuning in Azure Blob-opslag (preview)](network-file-system-protocol-support.md)voor meer informatie over NFS 3,0-protocol ondersteuning in Blob Storage.

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Stap 1: de functie NFS 3,0-protocol registreren bij uw abonnement

1. Open een Power shell-opdracht venster. 

2. Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

3. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

4. Registreer de `AllowNFSV3` functie met behulp van de volgende opdracht.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registreer de resource provider met behulp van de volgende opdracht.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Stap 2: controleren of de functie is geregistreerd 

De registratie goedkeuring kan tot een uur duren. Gebruik de volgende opdrachten om te controleren of de registratie is voltooid.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Stap 3: een Azure-Virtual Network maken (VNet)

Uw opslag account moet zich in een VNet bevinden. Met een VNet kunnen clients veilig verbinding maken met uw opslag account. Zie de [Virtual Network-documentatie](../../virtual-network/index.yml)voor meer informatie over VNet en het maken van een account.

> [!NOTE]
> Clients in hetzelfde VNet kunnen containers in uw account koppelen. U kunt ook een container koppelen van een client die wordt uitgevoerd in een on-premises netwerk, maar u moet eerst uw on-premises netwerk verbinden met uw VNet. Zie [ondersteunde netwerk verbindingen](network-file-system-protocol-support.md#supported-network-connections).

## <a name="step-4-configure-network-security"></a>Stap 4: netwerk beveiliging configureren

De enige manier om de gegevens in uw account te beveiligen, is door gebruik te maken van een VNet en andere netwerk beveiligings instellingen. Elk ander hulp programma dat wordt gebruikt om gegevens te beveiligen, waaronder account sleutel autorisatie, Azure Active Directory (AD)-beveiliging en toegangs beheer lijsten (Acl's), worden nog niet ondersteund in accounts waarvoor ondersteuning voor NFS 3,0-protocol is ingeschakeld. 

Als u de gegevens in uw account wilt beveiligen, raadpleegt u de volgende aanbevelingen: [aanbevelingen voor netwerk beveiliging voor Blob-opslag](security-recommendations.md#networking).

## <a name="step-5-create-and-configure-a-storage-account"></a>Stap 5: een opslag account maken en configureren

Als u een container wilt koppelen met behulp van NFS 3,0, moet u een opslag account maken **nadat** u de functie hebt geregistreerd bij uw abonnement. U kunt geen accounts inschakelen die bestonden voordat u de functie registreerde. 

In de preview-versie van deze functie wordt het NFS 3,0-protocol ondersteund in [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) -en [algemene v2](../common/storage-account-overview.md#general-purpose-v2-accounts) -accounts.

Kies bij het configureren van het account deze waarden:

|Instelling | Premium-prestaties | Standaard prestaties  
|----|---|---|
|Locatie|Alle beschik bare regio's |Een van de volgende regio's: Australië-oost, Korea-centraal en Zuid-Centraal VS   
|Prestaties|Premium| Standard
|Soort account|BlockBlobStorage| Algemeen v2
|Replicatie|Lokaal redundante opslag (LRS)| Lokaal redundante opslag (LRS)
|Verbindingsmethode|Openbaar eind punt (geselecteerde netwerken) of persoonlijk eind punt |Openbaar eind punt (geselecteerde netwerken) of persoonlijk eind punt
|Veilige overdracht vereist|Uitgeschakeld|Uitgeschakeld
|Hiërarchische naamruimte|Ingeschakeld|Ingeschakeld
|NFS V3|Ingeschakeld |Ingeschakeld 

U kunt de standaard waarden voor alle andere instellingen accepteren. 

## <a name="step-6-create-a-container"></a>Stap 6: een container maken

Maak een container in uw opslag account met behulp van een van deze hulpprogram ma's of Sdk's:

|Hulpprogramma's|SDK's|
|---|---|
|[Azure-portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-blobs.md#create-a-container)|[Java](data-lake-storage-directory-file-acl-java.md#create-a-container)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure-CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Stap 7: de container koppelen

Maak een map in uw Windows-of Linux-systeem en koppel vervolgens een container in het opslag account.

### <a name="linux"></a>[Linux](#tab/linux)

1. Maak een directory op een Linux-systeem.

   ```
   mkdir -p /mnt/test
   ```

2. Koppel een container met behulp van de volgende opdracht.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Vervang de `<storage-account-name>` tijdelijke aanduiding die wordt weer gegeven in deze opdracht door de naam van uw opslag account.  

   - Vervang de `<container-name>` tijdelijke aanduiding door de naam van de container.


### <a name="windows"></a>[Windows](#tab/windows)

1. Open het dialoog venster **Windows-onderdelen** en schakel vervolgens de functie **client voor NFS** in. 

   ![Functie client voor Network File System](media/network-file-system-protocol-how-to/client-for-network-files-system-feature.png)

2. Open een **opdracht prompt** venster (cmd.exe). Koppel vervolgens een container met behulp van de [koppelings](/windows-server/administration/windows-commands/mount) opdracht.

   ```
   mount -o nolock <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name> *
   ```

   - Vervang de `<storage-account-name>` tijdelijke aanduiding die wordt weer gegeven in deze opdracht door de naam van uw opslag account.  

   - Vervang de `<container-name>` tijdelijke aanduiding door de naam van de container.

3. Als u schrijf machtigingen nodig hebt, moet u mogelijk de standaard-UID en GID wijzigen die Windows gebruikt om verbinding te maken met de share. Als u dit wilt doen, voert u de volgende Power shell-opdrachten uit als beheerder:

   ```
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousUid -PropertyType DWord -Value 0
   New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\ClientForNFS\CurrentVersion\Default -Name AnonymousGid -PropertyType DWord -Value 0
   ```
   
   - Start de NFS-client service opnieuw of start de server opnieuw op nadat u deze wijziging hebt aangebracht.

---

## <a name="resolve-common-issues"></a>Veelvoorkomende problemen oplossen

|Probleem/fout | Oplossing|
|---|---|
|`Access denied by server while mounting`|Zorg ervoor dat de client wordt uitgevoerd in een ondersteund subnet. Bekijk de [ondersteunde netwerk locaties](network-file-system-protocol-support.md#supported-network-connections).|
|`No such file or directory`| Zorg ervoor dat de container die u koppelt, is gemaakt nadat u hebt gecontroleerd of de functie is geregistreerd. Zie [stap 2: controleren of de functie is geregistreerd](#step-2-verify-that-the-feature-is-registered). Zorg er ook voor dat u de koppel opdracht en de para meters rechtstreeks in de Terminal typt. Als u een deel van deze opdracht vanuit een andere toepassing in de terminal kopieert, kunnen verborgen tekens in de gekopieerde gegevens deze fout veroorzaken.|

## <a name="see-also"></a>Zie ook

[Ondersteuning voor het protocol Network File System (NFS) 3,0 in Azure Blob-opslag (preview-versie)](network-file-system-protocol-support.md)
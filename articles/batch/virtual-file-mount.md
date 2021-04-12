---
title: Een virtueel bestands systeem koppelen aan een groep
description: Meer informatie over het koppelen van een virtueel bestands systeem aan een batch-pool.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 03/26/2021
ms.openlocfilehash: dcd56a12d8728b83cdcb7cea4c16c4aedd4251a7
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105745"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Een virtueel bestands systeem koppelen aan een batch-pool

Azure Batch biedt ondersteuning voor het koppelen van Cloud opslag of een extern bestands systeem op Windows-of Linux-reken knooppunten in uw batch-Pools. Wanneer een reken knooppunt wordt toegevoegd aan een pool, wordt het virtuele bestands systeem gekoppeld en behandeld als een lokaal station op het knoop punt. U kunt bestands systemen zoals Azure Files, Azure Blob Storage, Network File System (NFS), waaronder een [avere vFXT-cache](../avere-vfxt/avere-vfxt-overview.md)of een common Internet File System (CIFS) koppelen.

In dit artikel leert u hoe u een virtueel bestands systeem koppelt aan een pool van reken knooppunten met behulp [van de batch-beheer bibliotheek voor .net](/dotnet/api/overview/azure/batch).

> [!NOTE]
> Het koppelen van een virtueel bestands systeem wordt alleen ondersteund voor batch-Pools die zijn gemaakt op of na 8 augustus 2019. Batch-Pools die vóór die datum zijn gemaakt, bieden geen ondersteuning voor deze functie.

## <a name="benefits-of-mounting-on-a-pool"></a>Voor delen van koppelen aan een groep

Het bestands systeem koppelen aan de groep, in plaats van taken om hun eigen gegevens uit een grote gegevensset op te halen, is het eenvoudiger en efficiënter voor taken om toegang te krijgen tot de gegevens die nodig zijn.

Overweeg een scenario met meerdere taken waarvoor toegang tot een gemeen schappelijke set gegevens is vereist, zoals het renderen van een film. Met elke taak worden een of meer frames tegelijk uit de scène bestanden weer gegeven. Door een station met de scène bestanden te koppelen, is het eenvoudiger voor reken knooppunten om toegang te krijgen tot gedeelde gegevens.

Daarnaast kan het onderliggende bestands systeem afzonderlijk worden gekozen en geschaald op basis van de prestaties en de schaal (door Voer en IOPS) die vereist zijn voor het aantal reken knooppunten dat gelijktijdig toegang heeft tot de gegevens. Een gedistribueerde in-memory cache van [avere](../avere-vfxt/avere-vfxt-overview.md) kan bijvoorbeeld worden gebruikt voor het ondersteunen van grootschalige Renders op schaal van een afbeelding met duizenden gelijktijdige weergave knooppunten, waarbij toegang wordt verkregen tot bron gegevens die on-premises zijn opgeslagen. Voor gegevens die zich al in Cloud opslag op basis van een BLOB bevinden, kan [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) ook worden gebruikt om deze gegevens als een lokaal bestands systeem te koppelen. Blobfuse is alleen beschikbaar op Linux-knoop punten, maar [Azure files](../storage/files/storage-files-introduction.md) biedt een vergelijk bare werk stroom en is beschikbaar op Windows en Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Een virtueel bestands systeem koppelen aan een groep  

Als u een virtueel bestands systeem op een groep koppelt, wordt het bestands systeem beschikbaar voor elk reken knooppunt in de pool. Het bestands systeem wordt geconfigureerd wanneer een reken knooppunt wordt toegevoegd aan een groep of wanneer het knoop punt opnieuw wordt opgestart of de installatie kopie wordt gerecycled.

Als u een bestands systeem in een groep wilt koppelen, maakt u een- `MountConfiguration` object. Kies het object dat bij uw virtuele bestands systeem past: `AzureBlobFileSystemConfiguration` ,, `AzureFileShareConfiguration` `NfsMountConfiguration` , of `CifsMountConfiguration` .

Voor alle koppel configuratie objecten zijn de volgende basis parameters vereist. Sommige koppel configuraties hebben specifieke para meters voor het bestands systeem dat wordt gebruikt. dit wordt in meer detail beschreven in de code voorbeelden.

- **Account naam of-bron**: voor het koppelen van een virtuele bestands share hebt u de naam van het opslag account of de bron nodig.
- **Relatief koppel pad of bron**: de locatie van het bestands systeem dat is gekoppeld aan het reken knooppunt, ten opzichte van de standaard `fsmounts` Directory die toegankelijk is op het knoop punt via `AZ_BATCH_NODE_MOUNTS_DIR` . De exacte locatie varieert, afhankelijk van het besturings systeem dat op het knoop punt wordt gebruikt. Bijvoorbeeld, de fysieke locatie op een Ubuntu-knoop punt wordt toegewezen aan `mnt\batch\tasks\fsmounts` en op een CentOS-knoop punt waaraan deze is toegewezen `mnt\resources\batch\tasks\fsmounts` .
- Opties voor **koppelen of blobfuse**: deze opties beschrijven specifieke para meters voor het koppelen van een bestands systeem.

Wanneer het `MountConfiguration` object is gemaakt, moet u het object toewijzen aan de `MountConfigurationList` eigenschap wanneer u de groep maakt. Het bestands systeem wordt gekoppeld wanneer een knoop punt wordt toegevoegd aan een pool of wanneer het knoop punt opnieuw wordt opgestart of een installatie kopie wordt gemaakt.

Wanneer het bestands systeem is gekoppeld, wordt er een omgevings variabele `AZ_BATCH_NODE_MOUNTS_DIR` gemaakt die verwijst naar de locatie van de gekoppelde bestands systemen en de logboek bestanden, die handig zijn voor het oplossen van problemen en fout opsporing. Logboek bestanden worden uitgebreid beschreven in het gedeelte fout bij het vaststellen van de [koppeling](#diagnose-mount-errors) .  

> [!IMPORTANT]
> Het maximum aantal gekoppelde bestands systemen in een groep is 10. Zie [quota's en limieten](batch-quota-limit.md#other-limits) voor de batch-service voor meer informatie en andere beperkingen.

## <a name="examples"></a>Voorbeelden

De volgende code voorbeelden laten zien hoe u een verscheidenheid aan bestands shares koppelt aan een pool van reken knooppunten.

### <a name="azure-files-share"></a>Azure Files share

Azure Files is de standaard aanbieding van het Azure Cloud-bestands systeem. Zie voor meer informatie over het verkrijgen van een van de para meters in het koppel configuratie code voorbeeld [een Azure Files share-SMB gebruiken](../storage/files/storage-how-to-use-files-windows.md) of [een Azure Files share met-NFS](../storage/files/storage-files-how-to-create-nfs-shares.md)gebruiken.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob-bestands systeem

Een andere optie is het gebruik van Azure Blob Storage via [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). Voor het koppelen van een BLOB-bestands systeem is een `AccountKey` or vereist `SasKey` voor uw opslag account. Zie [toegangs sleutels voor opslag accounts beheren](../storage/common/storage-account-keys-manage.md) of [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md)voor meer informatie over het ophalen van deze sleutels. Zie de blobfuse voor meer informatie en tips over het gebruik van blobfuse.

Als u standaard toegang wilt krijgen tot de gekoppelde blobfuse Directory, voert u de taak uit als **beheerder**. Blobfuse koppelt de directory aan de gebruikers ruimte en bij het maken van de groep wordt deze als root gekoppeld. In Linux zijn alle **beheerders** taken hoofdmap. Alle opties voor de ZEKERing-module worden beschreven op de [pagina zekerheid](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Raadpleeg de [Veelgestelde vragen over problemen oplossen](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) voor meer informatie en tips over het gebruik van blobfuse. U kunt ook [github-problemen in de blobfuse-opslag plaats](https://github.com/Azure/azure-storage-fuse/issues) bekijken om te controleren op de huidige blobfuse problemen en oplossingen.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Netwerk bestandssysteem

Network File System (NFS) kan worden gekoppeld aan groeps knooppunten, zodat traditionele bestands systemen kunnen worden geopend door Azure Batch. Dit kan een enkele NFS-server zijn die is geïmplementeerd in de Cloud, of een on-premises NFS-server die toegankelijk is via een virtueel netwerk. U kunt ook de [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) gedistribueerde in-memory-cache oplossing gebruiken voor gegevensintensieve HPC-taken (High-Performance Computing)

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Common Internet File System

Het koppelen van [Common Internet File System (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) aan pool knooppunten is een andere manier om toegang te bieden tot traditionele bestands systemen. CIFS is een protocol voor het delen van bestanden dat een open en platformoverschrijdende mechanisme biedt voor het aanvragen van netwerk server bestanden en-services. CIFS is gebaseerd op de uitgebreide versie van het [SMB-protocol (Server Message Block)](/windows-server/storage/file-server/file-server-smb-overview) voor het delen van Internet-en intranet bestanden, en kan worden gebruikt voor het koppelen van externe bestands systemen op Windows-knoop punten.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Fouten bij koppelen vaststellen

Als een koppelings configuratie mislukt, mislukt het reken knooppunt in de groep en wordt de status van het knoop punt ingesteld op `unusable` . Als u een fout bij het koppelen van een configuratie wilt vaststellen, inspecteert u de [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) eigenschap voor meer informatie over de fout.

Als u de logboek bestanden voor fout opsporing wilt ophalen, gebruikt u [OutputFiles](batch-task-output-files.md) om de bestanden te uploaden `*.log` . De `*.log` bestanden bevatten informatie over de bestandssysteem koppeling op de `AZ_BATCH_NODE_MOUNTS_DIR` locatie. Het koppelen van logboek bestanden heeft de volgende indeling: `<type>-<mountDirOrDrive>.log` voor elke koppeling. Een `cifs` koppeling op een koppelings Directory met de naam heeft bijvoorbeeld `test` een koppel logboek bestand met de naam: `cifs-test.log` .

## <a name="supported-skus"></a>Ondersteunde Sku's

| Uitgever | Aanbieding | SKU | Azure Files share | Blobfuse | NFS-koppeling | CIFS koppelen |
|---|---|---|---|---|---|---|
| batch | Rendering-centos73 | aanwijzer | :heavy_check_mark: <br>Opmerking: compatibel met CentOS 7,7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16,04-LTS, 18,04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | BxDxH | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| micro soft-Ads | Linux-Data-Science-VM | linuxdsvm | :heavy_check_mark: <br>Opmerking: compatibel met CentOS 7,4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| micro soft-Azure-batch | CentOS-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| micro soft-Azure-batch | CentOS-container-RDMA | 7.4 | :heavy_check_mark: <br>Opmerking: ondersteunt A_8 of 9 opslag</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| micro soft-Azure-batch | Ubuntu-Server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| micro soft-dsvm | Linux-Data-Science-VM-Ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | BxDxH | BxDxH | BxDxH | BxDxH |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | BxDxH | BxDxH | BxDxH |

## <a name="networking-requirements"></a>Netwerkvereisten

Houd bij het gebruik van virtuele bestands koppelingen met [Azure batch groepen in een virtueel netwerk](batch-virtual-network.md)de volgende vereisten en zorg ervoor dat er geen vereist verkeer wordt geblokkeerd.

- **Azure files**:
  - Vereist dat TCP-poort 445 is geopend voor verkeer van/naar de service-tag opslag. Zie [een Azure-bestands share gebruiken met Windows](../storage/files/storage-how-to-use-files-windows.md#prerequisites)voor meer informatie.
- **Blobfuse**:
  - Vereist dat TCP-poort 443 is geopend voor verkeer van/naar de service-tag opslag.
  - Vm's moeten toegang hebben tot om https://packages.microsoft.com de blobfuse-en GPG-pakketten te kunnen downloaden. Afhankelijk van uw configuratie hebt u mogelijk ook toegang tot andere Url's nodig om extra pakketten te downloaden.
- **Network File System (NFS)**:
  - Vereist toegang tot poort 2049 (standaard instelling; uw configuratie heeft mogelijk andere vereisten).
  - Vm's moeten toegang hebben tot de juiste pakket beheer om het pakket nfs-common (voor Debian of Ubuntu) of NFS-hulppr. (voor CentOS) te downloaden. Deze URL kan variëren afhankelijk van de versie van uw besturings systeem. Afhankelijk van uw configuratie hebt u mogelijk ook toegang tot andere Url's nodig om extra pakketten te downloaden.
- **Common Internet File System (CIFS)**:
  - Vereist toegang tot TCP-poort 445.
  - Vm's moeten toegang hebben tot de juiste pakket beheer (s) om het CIFS-utils-pakket te kunnen downloaden. Deze URL kan variëren afhankelijk van de versie van uw besturings systeem.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het koppelen van een Azure Files share met [Windows](../storage/files/storage-how-to-use-files-windows.md) of [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Meer informatie over het gebruik van en koppelen van [blobfuse](https://github.com/Azure/azure-storage-fuse) Virtual File Systems.
- Zie [overzicht Network File System](/windows-server/storage/nfs/nfs-overview) voor meer informatie over NFS en de bijbehorende toepassingen.
- Zie het [overzicht van micro soft SMB-protocol en CIFS-protocol](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) voor meer informatie over CIFS.

---
title: Volledige back-up/herstel en selectief herstel voor Azure Managed HSM
description: In dit document wordt uitgelegd wat volledige back-up/herstel en selectief herstel inhoudt
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: daf7a9fe3291850bea8c78680bcf375931383020
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805594"
---
# <a name="full-backup-and-restore"></a>Volledige back-up en herstel

> [!NOTE]
> Deze functie is alleen beschikbaar voor het resourcetype Beheerde HSM.

Beheerde HSM ondersteunt het maken van een volledige back-up van de hele inhoud van de HSM, met inbegrip van alle sleutels, versies, kenmerken, tags en roltoewijzingen. De back-up wordt versleuteld met cryptografische sleutels die zijn gekoppeld aan het beveiligingsdomein van de HSM.

Back-up is een gegevensvlakbewerking. De oproepende functie die de back-upbewerking initieert, moet gemachtigd zijn om dataAction **Microsoft.KeyVault/managedHsm/backup/start/action** uit te voeren.

Alleen de volgende ingebouwde rollen hebben toestemming voor het uitvoeren van een volledige back-up:
- Beheerder van beheerde HSM
- Back-up van beheerde HSM

U moet de volgende informatie opgeven voor het uitvoeren van een volledige back-up:
- Naam of URL van HSM
- Naam van het opslagaccount
- Blobopslagcontainer van het opslagaccount
- SAS-token van opslagcontainer met machtigingen `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Volledige back-up

Back-up is een langdurige bewerking, maar retourneert onmiddellijk een taak-id. U kunt de status van het back-upproces controleren met behulp van deze taak-id. Met het back-upproces maakt een map in de aangewezen container met het benoemingspatroon **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** , waarbij HSM_NAME de naam is van de beheerde HSM waarvan een back-up wordt gemaakt en YYYY, MM, DD, HH, mm, SS het jaar, de maand, de dag, het uur, het aantal minuten en seconden van de datum/tijd in UTC zijn dat de back-upopdracht is ontvangen.

Tijdens het maken van de back-up mag de HSM niet op volle doorvoersnelheid draaien, omdat sommige HSM-partities worden gebruikt voor de back-upbewerking.

```azurecli-interactive
# time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Volledig herstel

Met volledig herstel kunt u de inhoud van de HSM volledig herstellen met een eerdere back-up, met inbegrip van alle sleutels, versies, kenmerken, tags en roltoewijzingen. Alles wat momenteel in de HSM is opgeslagen, wordt gewist, en de HSM wordt teruggezet in de toestand zoals die was toen de back-up werd gemaakt.

> [!IMPORTANT]
> Volledig herstel is een erg destructieve en verstorende bewerking. Daarom is het verplicht een volledige back-up in de laatste 30 minuten te hebben voltooid voordat een `restore`-bewerking kan worden uitgevoerd.

Herstel is een gegevensvlakbewerking. De oproepende functie die de herstelbewerking start, moet gemachtigd zijn om dataAction **Microsoft.KeyVault/managedHsm/restore/start/action** uit te voeren. De bron-HSM waar de back-up is gemaakt en de doel-HSM waar de herstelbewerking wordt uitgevoerd, **moeten** hetzelfde beveiligingsdomein hebben. Zie meer informatie [over het beveiligingsdomein van een beheerde HSM](security-domain.md).

U moet de volgende informatie opgeven voor het uitvoeren van een volledige herstelbewerking:
- Naam of URL van HSM
- Naam van het opslagaccount
- Blobcontainer van het opslagaccount
- SAS-token van opslagcontainer met machtigingen `rl`
- Naam van de map van de opslagcontainer waarin de back-up van de bron is opgeslagen

Herstel is een langdurige bewerking, maar retourneert onmiddellijk een taak-id. U kunt de status van het herstelproces controleren met behulp van deze taak-id. Tijdens het herstelproces wordt de HSM in een herstelmodus gezet en worden alle gegevensvlakopdrachten (behalve herstelstatus controleren) uitgeschakeld.

```azurecli-interactive
#### time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="restore-hsm"></a>HSM herstellen

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Volgende stappen
- Zie [Een beheerde HSM beheren met de Azure CLI](key-management.md).
- Meer informatie over het [beveiligingsdomein van een beheerde HSM](security-domain.md)

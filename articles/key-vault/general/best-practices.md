---
title: Aanbevolen procedures voor het gebruik van Key Vault-Azure Key Vault | Microsoft Docs
description: Meer informatie over aanbevolen procedures voor Azure Key Vault, waaronder het beheren van toegang, wanneer u afzonderlijke sleutel kluizen, back-ups, logboek registratie en herstel opties gebruikt.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: e81cbd7e6584f4a280ab9507a989b52d3b188f2d
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072625"
---
# <a name="best-practices-to-use-key-vault"></a>Aanbevolen procedures voor het gebruik van Key Vault

## <a name="use-separate-key-vaults"></a>Gebruik afzonderlijke sleutel kluizen

Onze aanbeveling is een kluis per toepassing per omgeving te gebruiken (ontwikkeling, voor bereiding en productie). Zo kunt u geen geheimen delen tussen omgevingen en vermindert u ook de dreiging in het geval van een schending.

## <a name="control-access-to-your-vault"></a>Toegang tot uw kluis beheren

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, moet u de toegang tot uw sleutel kluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In dit [artikel](secure-your-key-vault.md) vindt u een overzicht van het toegangs model van Key Vault. Hierin worden verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw sleutel kluizen kunt beveiligen.

Suggesties voor het beheren van de toegang tot uw kluis zijn als volgt:
1. Vergren deling van toegang tot uw abonnement, resource groep en sleutel kluizen (Azure RBAC)
2. Toegangs beleid maken voor elke kluis
3. Access principal met minimale bevoegdheden gebruiken om toegang te verlenen
4. Firewall-en [VNET-service-eind punten](overview-vnet-service-endpoints.md) inschakelen

## <a name="backup"></a>Backup

Zorg ervoor dat u regel matig back-ups van uw kluis onderneemt op het bijwerken/verwijderen/maken van objecten in een kluis.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell-back-upopdrachten

* [Back-upcertificaat](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Back-upsleutel](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Back-upgeheim](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Back-upopdrachten van Azure CLI

* [Back-upcertificaat](/cli/azure/keyvault/certificate#az-keyvault-certificate-backup)
* [Back-upsleutel](/cli/azure/keyvault/key#az-keyvault-key-backup)
* [Back-upgeheim](/cli/azure/keyvault/secret#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Logboek registratie inschakelen

[Schakel logboek registratie in](logging.md) voor uw kluis. Stel ook waarschuwingen in.

## <a name="turn-on-recovery-options"></a>Herstel opties inschakelen

1. Schakel [zacht verwijderen](soft-delete-overview.md)in.
2. Schakel opschonen beveiliging in als u wilt dat de geheime sleutel wordt verwijderd tegen het verwijderen van het geheim of de kluis, zelfs nadat het zacht verwijderen is ingeschakeld.
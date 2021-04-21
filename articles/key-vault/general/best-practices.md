---
title: Best practices voor het gebruik van Key Vault - Azure Key Vault | Microsoft Docs
description: Meer informatie over best practices voor Azure Key Vault, waaronder het beheren van toegang, wanneer u afzonderlijke sleutelkluizen, back-up-, logboekregistratie- en herstelopties gebruikt.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 7cfa2059cc03b96db39183cfa5056c9934a02290
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814348"
---
# <a name="best-practices-to-use-key-vault"></a>Best practices voor het gebruik van Key Vault

## <a name="use-separate-key-vaults"></a>Afzonderlijke sleutelkluizen gebruiken

Het wordt aanbevolen om per toepassing per omgeving een kluis te gebruiken (ontwikkeling, preproductie en productie). Dit helpt u om geen geheimen te delen in omgevingen en vermindert ook de bedreiging in het geval van een inbreuk.

## <a name="control-access-to-your-vault"></a>Toegang tot uw kluis bepalen

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. Omdat deze gegevens gevoelig en bedrijfskritiek zijn, moet u de toegang tot uw sleutelkluizen beveiligen door alleen geautoriseerde toepassingen en gebruikers toe te staan. In [dit artikel](security-features.md) vindt u een overzicht van Key Vault toegangsmodel. Hier wordt verificatie en autorisatie uitgelegd en wordt beschreven hoe u de toegang tot uw sleutelkluizen beveiligt.

Suggesties voor het beheren van de toegang tot uw kluis zijn als volgt:
1. Toegang tot uw abonnement, resourcegroep en sleutelkluizen (Azure RBAC) vergrendelen
2. Toegangsbeleid maken voor elke kluis
3. Toegangsprincipaal met minste bevoegdheden gebruiken om toegang te verlenen
4. Firewall- en [VNET-service-eindpunten in-](overview-vnet-service-endpoints.md)

## <a name="backup"></a>Backup

Zorg ervoor dat u regelmatig back-ups van uw kluis maakt bij het bijwerken/verwijderen/maken van objecten in een kluis.

### <a name="azure-powershell-backup-commands"></a>Azure PowerShell Back-upopdrachten

* [Back-upcertificaat](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Back-upsleutel](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Back-upgeheim](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Azure CLI Backup-opdrachten

* [Back-upcertificaat](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [Back-upsleutel](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [Back-upgeheim](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>Logboekregistratie inschakelen

[Schakel logboekregistratie in](logging.md) voor uw kluis. Stel ook waarschuwingen in.

## <a name="turn-on-recovery-options"></a>Herstelopties in-

1. Schakel Soft [Delete in.](soft-delete-overview.md)
2. Schakel beveiliging tegen opsluizen in als u zich wilt beschermen tegen het geforceer verwijderen van het geheim/de kluis, zelfs nadat de soft-delete is ingeschakeld.
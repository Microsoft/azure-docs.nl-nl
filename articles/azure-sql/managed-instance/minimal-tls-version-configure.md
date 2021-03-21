---
title: Minimale TLS-versie configureren-beheerd exemplaar
description: Meer informatie over het configureren van een minimale TLS-versie voor een beheerd exemplaar
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: ''
ms.date: 05/25/2020
ms.openlocfilehash: 17d430946f3cba1aa4680d1eaf8979fa4338bc22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788397"
---
# <a name="configure-minimal-tls-version-in-azure-sql-managed-instance"></a>Minimale versie van TLS configureren in Azure SQL Managed Instance
Met de instelling minimale versie van [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) kunnen klanten de versie van TLS beheren die wordt gebruikt door hun Azure SQL Managed instance.

Momenteel worden TLS 1.0, 1.1 en 1.2 ondersteund. Door een minimale TLS-versie in te stellen, zorgt u ervoor dat nieuwere versies van TLS worden gebruikt. Bijvoorbeeld, bijvoorbeeld het kiezen van een TLS-versie die groter is dan 1,1. worden alleen verbindingen met TLS 1.1 en 1.2 toegestaan en worden verbindingen met TLS 1.0 geweigerd. Nadat u hebt gecontroleerd of uw toepassingen deze ondersteunen, wordt u aangeraden de minimale TLS-versie in te stellen op 1.2, omdat deze fixes bevat voor beveiligingsproblemen die in vorige versies zijn aangetroffen. Het is ook de hoogste versie van TLS die wordt ondersteund in Azure SQL Managed Instance.

Voor klanten met toepassingen die afhankelijk zijn van oudere versies van TLS, wordt u aangeraden de minimale TLS-versie in te stellen volgens de vereisten van uw toepassingen. Voor klanten die afhankelijk zijn van toepassingen om verbinding te maken met behulp van een niet-versleutelde verbinding, wordt u aangeraden geen minimale TLS-versie in te stellen. 

Zie [TLS-overwegingen voor SQL database connectiviteit](../database/connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity)voor meer informatie.

Nadat u de minimale versie van TLS hebt ingesteld, mislukken aanmeldings pogingen van clients die een TLS-versie gebruiken die lager is dan de minimale TLS-versie van de server, mislukt met de volgende fout:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Minimale TLS-versie instellen via Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Voor het volgende script is de [Azure PowerShell-module](/powershell/azure/install-az-ps)vereist.

Het volgende Power shell-script laat zien hoe `Get` en hoe `Set` de eigenschap **minimale TLS-versie** op instantie niveau is:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group).MinimalTlsVersion

# Update Minimal TLS Version Property
Set-AzSqlInstance -Name sql-instance-name -ResourceGroupName resource-group -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Minimale TLS-versie instellen via Azure CLI

> [!IMPORTANT]
> Alle scripts in deze sectie vereisen [Azure cli](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI in een bash-shell

Het volgende CLI-script laat zien hoe u de instelling **minimale TLS-versie** in een bash-shell wijzigt:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql mi show -n sql-instance-name -g resource-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql mi update -n sql-instance-name -g resource-group --set minimalTlsVersion="1.2"
```
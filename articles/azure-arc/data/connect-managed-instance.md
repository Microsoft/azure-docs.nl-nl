---
title: Verbinding maken met Azure Arc enabled SQL Managed instance
description: Verbinding maken met Azure Arc enabled SQL Managed instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: abd27e15ccf5b421e69e78b2b726d192ffdecacb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372358"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Verbinding maken met Azure Arc enabled SQL Managed instance

In dit artikel wordt uitgelegd hoe u verbinding kunt maken met uw met Azure Arc ingeschakeld SQL Managed instance. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Door Azure Arc ingeschakelde SQL Managed instances weer geven

Gebruik de volgende opdracht om het door Azure Arc ingeschakelde SQL Managed instance en de externe eind punten weer te geven:

```console
azdata arc sql mi list
```

De uitvoer moet er als volgt uitzien:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Als u AKS of kubeadm of open Shift enzovoort gebruikt, kunt u het externe IP-adres en poort nummer van hier kopiëren en er verbinding mee maken met uw favoriete hulp programma om verbinding te maken met een SQL Server/Azure SQL-exemplaar, zoals Azure Data Studio of SQL Server Management Studio.  Als u echter de snelle start-VM gebruikt, raadpleegt u hieronder voor speciale informatie over het maken van een verbinding met de virtuele machine van buiten Azure. 

> [!NOTE]
> Uw bedrijfs beleid blokkeert mogelijk de toegang tot het IP-adres en de poort, met name als dit wordt gemaakt in de open bare Cloud.

## <a name="connect"></a>Verbinding maken 

Verbinding maken met Azure Data Studio, SQL Server Management Studio of SQLCMD

Open Azure Data Studio en maak verbinding met uw exemplaar met het externe IP-adres van het eind punt en het bovenstaande poort nummer. Als u een virtuele Azure-machine gebruikt, hebt u het _open bare_ IP-adres nodig. Dit is herkenbaar aan de [speciale opmerking over implementaties van virtuele Azure-machines](#special-note-about-azure-virtual-machine-deployments).

Bijvoorbeeld:

- Server: 52.229.9.30, 30913
- Gebruikers naam: SA
- Wacht woord: het opgegeven SQL-wacht woord bij de inrichtings tijd

> [!NOTE]
> U kunt Azure Data Studio [de SQL Managed instance-Dash boards weer geven](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

Als u verbinding wilt maken met SQLCMD of Linux of Windows, kunt u een opdracht zoals deze gebruiken. Voer het SQL-wacht woord in wanneer u hierom wordt gevraagd:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Speciale opmerking over implementaties van virtuele Azure-machines

Als u een virtuele machine van Azure gebruikt, wordt het open bare IP-adres niet weer gegeven op het IP-adres van het eind punt. Als u het externe IP-adres wilt zoeken, gebruikt u de volgende opdracht:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

U kunt het open bare IP-adres vervolgens combi neren met de poort om verbinding te maken.

Mogelijk moet u ook de poort van het SQL-exemplaar beschikbaar maken via de netwerk beveiligings gateway (NSG). Als u verkeer wilt toestaan via de (NSG), moet u een regel toevoegen die u kunt uitvoeren met behulp van de volgende opdracht.

Als u een regel wilt instellen, moet u de naam weten van uw NSG die u kunt vinden met behulp van de onderstaande opdracht:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Zodra u de naam van de NSG hebt, kunt u een firewall regel toevoegen met behulp van de volgende opdracht. De voorbeeld waarden maken hier een NSG-regel voor poort 30913 en staan de verbinding vanaf **elk** bron-IP-adres toe.  Dit is geen beveiligings best practice!  U kunt de functies beter vergrendelen door een waarde voor de bron-adres-voorvoegsels op te geven die specifiek is voor uw client-IP-adres of een IP-adresbereik dat betrekking heeft op de IP-adressen van uw team of organisatie.

Vervang de waarde van de `--destination-port-ranges` onderstaande para meter door het poort nummer dat u hebt ontvangen van de `azdata sql instance list` F-opdracht hierboven.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Volgende stappen

- [De Dash boards van het SQL-beheerde exemplaar weer geven](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [SQL Managed instance in de Azure Portal weer geven](view-arc-data-services-inventory-in-azure-portal.md)

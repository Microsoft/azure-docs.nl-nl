---
title: Een door Azure SQL beheerd exemplaar maken op Azure Arc
description: Een door Azure SQL beheerd exemplaar maken op Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0d1d6c7349adc65270e13d09d771963cb9cacea3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280404"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Een door Azure SQL beheerd exemplaar maken op Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="login-to-the-azure-arc-data-controller"></a>Meld u aan bij de Azure-Arc-gegevens controller

Voordat u een exemplaar kunt maken, meldt u zich aan bij de Azure Arc-gegevens controller als u zich nog niet hebt aangemeld.

```console
azdata login
```

Vervolgens wordt u gevraagd om de gebruikers naam, het wacht woord en de naam ruimte van het systeem.  

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-an-azure-sql-managed-instance"></a>Een Azure SQL Managed Instance maken

Gebruik de volgende opdracht om beschik bare Create-opties forSQL Managed instance te bekijken:
```console
azdata arc sql mi create --help
```

Gebruik de volgende opdracht om een SQL Managed instance te maken:

```console
azdata arc sql mi create -n <instanceName> --storage-class-data <storage class> --storage-class-logs <storage class>
```

Voorbeeld:

```console
azdata arc sql mi create -n sqldemo --storage-class-data managed-premium --storage-class-logs managed-premium
```
> [!NOTE]
>  Namen mogen niet langer zijn dan 13 tekens en voldoen aan de [naamgevings conventies voor DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  Wanneer u geheugen toewijzing en vCore toewijzing opgeeft, gebruikt u deze formule om te controleren of het maken is geslaagd. voor elke 1 vCore hebt u ten minste 4 GB RAM-geheugen van de beschik bare capaciteit op het Kubernetes-knoop punt waar het SQL Managed instance pod wordt uitgevoerd.
>
>  Bij het maken van een SQL-exemplaar worden bij het inrichten in Azure geen hoofdletters in de naam gebruikt
>
>  Beschik bare opslag klassen in uw Kubernetes-cluster uitvoeren `kubectl get storageclass` 


> [!NOTE]
> Als u het maken van SQL-instanties wilt automatiseren en de interactieve prompt voor het beheerders wachtwoord wilt vermijden, kunt u de `AZDATA_USERNAME` `AZDATA_PASSWORD` omgevings variabelen instellen op de gewenste gebruikers naam en wacht woord voordat u de `azdata arc sql mi create` opdracht uitvoert.
> 
>  Als u de gegevens controller hebt gemaakt met behulp van AZDATA_USERNAME en AZDATA_PASSWORD in dezelfde terminal sessie, worden de waarden voor AZDATA_USERNAME en AZDATA_PASSWORD gebruikt voor het maken van de SQL Managed instance.

> [!NOTE]
> Als u Azure SQL Managed instance maakt, worden de resources in azure niet geregistreerd. De stappen voor het registreren van de resource bevinden zich in de volgende artikelen: 
> - [Logboeken en metrische gegevens weer geven met Kibana en Grafana](monitor-grafana-kibana.md)
> - [Upload facturerings gegevens naar Azure en Bekijk deze in de Azure Portal](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Exemplaar in azure-boog weer geven

Als u het exemplaar wilt weer geven, gebruikt u de volgende opdracht:

```console
azdata arc sql mi list
```

De uitvoer moet er als volgt uitzien:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Als u AKS of open `kubeadm` SHIFT etc. gebruikt, kunt u het externe IP-adres en poort nummer van hier kopiëren en er verbinding mee maken met behulp van uw favoriete hulp programma om verbinding te maken met een SQL Server/Azure SQL-exemplaar, zoals Azure Data Studio of SQL Server Management Studio. Als u echter de Quick Start-VM gebruikt, raadpleegt u het artikel [verbinding maken met Azure Arc enabled SQL Managed instance](connect-managed-instance.md) voor speciale instructies.


## <a name="next-steps"></a>Volgende stappen
- [Verbinding maken met Azure Arc enabled SQL Managed instance](connect-managed-instance.md)
- [Registreer uw exemplaar bij Azure en upload metrische gegevens en logboeken over uw exemplaar](upload-metrics-and-logs-to-azure-monitor.md)
- [Azure SQL Managed instance met Azure Data Studio implementeren](create-sql-managed-instance-azure-data-studio.md)

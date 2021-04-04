---
title: Een door Azure SQL beheerd exemplaar maken met behulp van Azure Data Studio
description: Een door Azure SQL beheerd exemplaar maken met behulp van Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 651811384a8e831c41c48ca8c4849e5ee3852054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280467"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>Een door SQL beheerd exemplaar maken-Azure Arc met Azure Data Studio

In dit document worden de stappen beschreven voor het installeren van Azure SQL Managed instance-Azure Arc met Azure Data Studio

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="log-in-to-the-azure-arc-data-controller"></a>Meld u aan bij de Azure Arc-gegevens controller

Voordat u een exemplaar kunt maken, meldt u zich aan bij de Azure Arc-gegevens controller als u zich nog niet hebt aangemeld.

```console
azdata login
```

Vervolgens wordt u gevraagd om de naam ruimte waarin de gegevens controller is gemaakt, de gebruikers naam en het wacht woord voor aanmelding bij de controller.  

> Als u de naam ruimte moet valideren, kunt u uitvoeren ```kubectl get pods -A``` om een lijst met alle naam ruimten op het cluster op te halen.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>Een Azure SQL Managed instance maken op Azure Arc

- Azure Data Studio starten
- Klik op het tabblad verbindingen op de drie puntjes linksboven en kies nieuwe implementatie
- Selecteer in de implementatie opties **Azure SQL Managed instance-Azure Arc** 
  > [!NOTE]
  > U wordt mogelijk gevraagd om het hier te installeren [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] als dit nog niet is gebeurd.
- Accepteer de privacy-en licentie voorwaarden en klik onderaan op **selecteren** .



- Voer de volgende gegevens in op de Blade Azure SQL Managed instance implementeren: Azure Arc.
  - Voer een naam in voor de SQL Server instantie
  - Voer een wacht woord in en bevestig dit voor het SQL Server exemplaar
  - Selecteer de opslag klasse waar dit van toepassing is voor gegevens
  - Selecteer de opslag klasse die geschikt is voor logboeken

- Klik op de knop **implementeren**

- Hiermee wordt het maken van het Azure SQL Managed instance-Azure Arc op de gegevens controller gestart.

- Het maken van een paar minuten is voltooid

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>Verbinding maken met Azure SQL Managed instance-Azure Arc van Azure Data Studio

- Meld u aan bij de Azure Arc-gegevens controller door de naam ruimte, de gebruikers naam en het wacht woord voor de gegevens controller op te geven: 
```console
azdata login
```

- Bekijk alle Azure SQL Managed instances die zijn ingericht met behulp van de volgende opdrachten:

```console
azdata arc sql mi list
```

De uitvoer moet er als volgt uitzien, kopieer de ServerEndpoint (inclusief het poort nummer) hier.

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- Klik in Azure Data Studio, onder tabblad **verbindingen** , op de **nieuwe verbinding** in de weer gave **servers**
- Plak in de Blade **verbinding** de ServerEndpoint in het tekstvak Server
- Selecteer **SQL-aanmelding** als verificatie type
- *Sa* als gebruikers naam invoeren
- Voer het wacht woord voor het `sa` account in
- Voer desgewenst de naam van de specifieke data base in om verbinding te maken
- Selecteer eventueel nieuwe server groep en voeg deze toe, indien van toepassing
- Selecteer **verbinding maken** om verbinding te maken met het beheerde Azure SQL-exemplaar-Azure-boog




## <a name="next-steps"></a>Volgende stappen

Probeer nu [uw SQL-exemplaar te controleren](monitor-grafana-kibana.md)

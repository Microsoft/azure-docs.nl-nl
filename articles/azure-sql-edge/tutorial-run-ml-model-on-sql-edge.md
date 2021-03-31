---
title: ML-model op Azure SQL Edge implementeren met behulp van ONNX
description: In deel drie van deze driedelige Azure SQL Edge-zelfstudie over het voorspellen van verontreinigingen in ijzererts voert u de ONNX-machine learning-modellen uit op SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422192"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>ML-model op Azure SQL Edge implementeren met behulp van ONNX 

In deel drie van deze driedelige zelfstudie over het voorspellen van onzuiverheden in ijzererts in Azure SQL Edge gaat u het volgende doen:

1. Azure Data Studio gebruiken om verbinding te maken met SQL Database in de Azure SQL Edge-instance.
2. Verontreinigingen in ijzererts voorspellen met ONNX in Azure SQL Edge.

## <a name="key-components"></a>Belangrijkste onderdelen

1. De oplossing gebruikt standaard 500 milliseconden tussen elk bericht dat naar de Edge Hub wordt verzonden. Dit kan worden gewijzigd in het bestand **Program.cs**. 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. De oplossing heeft een bericht gegenereerd met de volgende kenmerken. Voeg de kenmerken toe aan of verwijder ze indien vereist. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Verbinding maken met de SQL Database in de Azure SQl Edge-instantie om ML-modellen te trainen, implementeren en testen

1. Open Azure Data Studio.

2. Start op het tabblad **Welkom** een nieuwe verbinding met de volgende gegevens:

   |_Veld_|_Waarde_|
   |-------|-------|
   |Verbindingstype| Microsoft SQL Server|
   |server|Openbaar IP-adres dat wordt vermeld in de VM die is gemaakt voor deze demo|
   |Gebruikersnaam|sa|
   |Wachtwoord|Het sterke wachtwoord dat is gebruikt bij het maken van de Azure SQL Edge-instance|
   |Database|Standaard|
   |Servergroep|Standaard|
   |Naam (optioneel)|Geef desgewenst een naam op|

3. Klik op **Verbinden**

4. Open in de sectie **Bestand** **/DeploymentScripts/MiningProcess_ONNX.jpynb** vanuit de map waarin u de projectbestanden op uw computer hebt gekloond.

5. Stel de kernel in op Python 3.


## <a name="next-steps"></a>Volgende stappen

Zie [Machine learning and AI with ONNX in SQL Edge](onnx-overview.md) voor meer informatie over het gebruiken van ONNX-modules in Azure SQL Edge.
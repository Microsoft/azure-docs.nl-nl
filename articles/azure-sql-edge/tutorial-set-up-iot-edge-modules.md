---
title: IoT Edge-modules instellen in Azure SQL Edge
description: In deel twee van deze driedelige Azure SQL Edge-zelfstudie over het voorspellen van verontreinigingen in ijzererts gaat u IoT Edge-modules en -verbindingen instellen.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 75e6ebaea4c5ba883820d2309212b35fed128142
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93422124"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge-modules en -verbindingen instellen

In deel twee van deze driedelige zelfstudie over het voorspellen van verontreinigingen in ijzererts in Azure SQL Edge gaat u de volgende IoT Edge-modules en -verbindingen instellen:

- Azure SQL Edge
- Gegevensgenerator IoT Edge-module

## <a name="specify-container-registry-credentials"></a>Referenties voor het containerregister opgeven

De referenties van de containerregisters waar de module-installatiekopieën worden gehost, moeten worden opgegeven. Deze zijn te vinden in het containerregister dat in uw resource groep is gemaakt. Navigeer naar de sectie **Toegangssleutels**. Noteer de volgende velden:

- Registernaam
- Aanmeldingsserver
- Gebruikersnaam
- Wachtwoord

Geef nu de containerreferenties op in de IoT Edge-module.

1. Navigeer naar de IoT-hub die in uw resourcegroep is gemaakt.

2. Klik in de sectie **IoT Edge** onder **Automatisch apparaatbeheer** op **Apparaat-id**. De id voor deze zelfstudie is `IronOrePredictionDevice`.

3. Selecteer de sectie **Modules instellen**.

4. Voer onder **Containerregisterreferenties** de volgende waarden in:

   _Veld_|_Waarde_
   -------|-------
   Naam|Registernaam
   Adres|Aanmeldingsserver
   Gebruikersnaam|Gebruikersnaam
   Wachtwoord|Wachtwoord
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>Bouw, push en implementeer de Gegevensgeneratormodule

1. Kloon de [projectbestanden](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica) naar uw computer.
2. Open het bestand **IronOre_Silica_Predict.sln** met behulp van Visual Studio 2019
3. Werk de details van het containerregister bij in **deployment.template.json** 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. Werk het bestand **modules.json** bij om het doelcontainerregister (of de opslagplaats voor de module) op te geven
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. Voer het project uit in foutopsporings- of releasemodus om zeker te zijn dat het project zonder problemen wordt uitgevoerd. 
6. Push het project naar uw containerregister door met de rechtermuisknop op de naam van het project te klikken en vervolgens **IoT Edge-modules bouwen en pushen** te selecteren.
7. Implementeer de gegevensgeneratormodule als een IoT Edge-module op uw Edge-apparaat. 

## <a name="deploy-the-azure-sql-edge-module"></a>De Azure SQL Edge-module implementeren

1. Implementeer de Azure SQL Edge-module door te klikken op **+ Toevoegen**, gevolgd door **Marketplace-module**. 

2. Zoek op de blade **IoT Edge-module-Marketplace** naar *Azure SQL Edge* en kies *Azure SQL Edge-ontwikkelaar*. 

3. Klik op de zojuist toegevoegde *Azure SQL Edge*-module onder **IoT Edge modules** om de Azure SQL Edge-module te configureren. Zie [Azure SQL Edge implementeren](./deploy-portal.md) voor meer informatie over de configuratieopties.

4. Voeg de `MSSQL_PACKAGE`omgevingsvariabele toe aan de implementatie van de *Azure SQL Edge*-module en geef de SAS-URL op van het dacpac-databasebestand dat is gemaakt in stap 8 van [Deel één](tutorial-deploy-azure-resources.md) van deze zelfstudie.

5. Klik op **Update**

6. Klik op de pagina **Modules instellen op apparaat** op **Volgende: Routes >** .

7. Geef in het deelvenster routes op de pagina **Modules instellen op apparaat** als volgt de routes op voor communicatie van de module met de IoT Edge-hub. Zorg ervoor dat u de modulenamen in de onderstaande routedefinities bijwerkt.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Bijvoorbeeld:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. Klik op de pagina **Modules instellen op apparaat** op **Volgende: Beoordelen en maken >**

8. Klik op de pagina **Modules instellen op apparaat** op **Maken**

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Maak en start de taak voor het streamen van T-SQL in Azure SQL Edge.

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

4. Open in het tabblad van het menu **Bestand** een nieuw notebook of gebruik de sneltoets CTRL + N.

5. Voer in het nieuwe queryvenster het onderstaande script uit om de taak voor T-SQL-streaming te maken. Vergeet niet om de volgende variabelen te wijzigen voordat u het script uitvoert. 
   - *SQL_SA_Password:* De MSSQL_SA_PASSWORD-waarde die is opgegeven tijdens de implementatie van de Azure SQL Edge-module. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Gebruik de volgende query om te verifiëren of de gegevens uit de module voor het genereren van gegevens naar de database worden gestreamd. 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


In deze zelfstudie hebben we de module voor het genereren van gegevens en de SQL Edge-module geïmplementeerd. Vervolgens hebben we een streamingtaak gemaakt om de gegevens die door de module voor het genereren van gegevens zijn gegenereerd naar SQL te streamen. 

## <a name="next-steps"></a>Volgende stappen

- [ML-model op Azure SQL Edge implementeren met behulp van ONNX](tutorial-run-ml-model-on-sql-edge.md)
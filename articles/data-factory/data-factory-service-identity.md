---
title: Beheerde identiteit voor Data Factory
description: Meer informatie over beheerde identiteit voor Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: jingwang
ms.openlocfilehash: 65512f8e46b5545929a798392ac5f19ddeab39ed
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562457"
---
# <a name="managed-identity-for-data-factory"></a>Beheerde identiteit voor Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Dit artikel helpt u te begrijpen wat een beheerde identiteit is voor Data Factory (voorheen bekend als Managed Service Identity/MSI) en hoe deze werkt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

Bij het maken van een data factory kan een beheerde identiteit worden gemaakt, samen met het maken van de fabriek. De beheerde identiteit is een beheerde toepassing die is geregistreerd bij Azure Active Directory, en vertegenwoordigt deze specifieke data factory.

Beheerde identiteit voor Data Factory voor delen de volgende functies:

- [Sla referentie op in azure Key Vault](store-credentials-in-key-vault.md). in dat geval wordt Data Factory beheerde identiteit gebruikt voor Azure Key Vault verificatie.
- Toegang tot gegevens archieven of berekeningen met beheerde identiteits verificatie, waaronder Azure Blob Storage, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Managed instance, Azure Synapse Analytics, REST, Databricks activiteit, webactiviteit en meer. Raadpleeg de artikelen connector en activiteit voor meer informatie.

## <a name="generate-managed-identity"></a>Beheerde identiteit genereren

De beheerde identiteit voor Data Factory wordt als volgt gegenereerd:

- Wanneer u data factory via **Azure portal of Power shell** maakt, wordt er altijd automatisch een beheerde identiteit gemaakt.
- Wanneer u data factory via **SDK** maakt, wordt er alleen een beheerde identiteit gemaakt als u ' identiteit = New FactoryIdentity () ' opgeeft in het fabrieks object dat u wilt maken. Zie voor beeld in [.net quick start-Data Factory maken](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Wanneer u data factory via **rest API** maakt, wordt er alleen een beheerde identiteit gemaakt als u de sectie ' identiteit ' opgeeft in de hoofd tekst van de aanvraag. Bekijk het voor beeld in [rest Quick Start: create Data Factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Als uw data factory geen beheerde identiteit heeft die is gekoppeld aan de instructies [Managed Identity ophalen](#retrieve-managed-identity) , kunt u er expliciet een genereren door de Data Factory met de id-initiator programmatisch bij te werken:

- [Beheerde identiteit genereren met behulp van Power shell](#generate-managed-identity-using-powershell)
- [Beheerde identiteit genereren met behulp van REST API](#generate-managed-identity-using-rest-api)
- [Beheerde identiteit genereren met behulp van een Azure Resource Manager sjabloon](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Beheerde identiteit genereren met behulp van SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- De beheerde identiteit kan niet worden gewijzigd. Het bijwerken van een data factory die al een beheerde identiteit heeft, heeft geen invloed op de beheerde identiteit.
>- Als u een data factory hebt bijgewerkt die al een beheerde identiteit heeft zonder de para meter Identity op te geven in het object Factory of als u de sectie ' identiteit ' niet opgeeft in de hoofd tekst van de REST-aanvraag, krijgt u een fout melding.
>- Wanneer u een data factory verwijdert, wordt de bijbehorende beheerde identiteit tegelijk verwijderd.

### <a name="generate-managed-identity-using-powershell"></a>Beheerde identiteit genereren met behulp van Power shell

Roep **set-AzDataFactoryV2** opdracht. vervolgens ziet u dat er nieuwe identiteits velden worden gegenereerd:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Beheerde identiteit genereren met behulp van REST API

Aanroep onder ' identiteit ' in de hoofd tekst van de aanvraag:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Hoofd tekst van aanvraag**: add "identiteit": {"type": "SystemAssigned"}.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Antwoord**: de beheerde identiteit wordt automatisch gemaakt en de sectie ' identiteit ' is dienovereenkomstig ingevuld.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Beheerde identiteit genereren met behulp van een Azure Resource Manager sjabloon

**Sjabloon**: Voeg "identiteit": {"type": "SystemAssigned"} toe.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Beheerde identiteit genereren met behulp van SDK

Roep de data factory-create_or_update functie aan met identiteit = New FactoryIdentity (). Voorbeeld code met behulp van .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Beheerde identiteit ophalen

U kunt de beheerde identiteit ophalen van Azure Portal of via een programma. In de volgende secties ziet u enkele voor beelden.

>[!TIP]
> Als u de beheerde identiteit niet ziet, moet u [beheerde identiteit genereren](#generate-managed-identity) door uw fabriek bij te werken.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Beheerde identiteit ophalen met behulp van Azure Portal

U kunt de informatie over beheerde identiteiten vinden in Azure Portal-> uw data factory-> eigenschappen.

- ID van beheerd identiteits object
- Beheerde identiteits Tenant

De beheerde identiteits gegevens worden ook weer gegeven wanneer u een gekoppelde service maakt, die beheerde identiteits verificatie ondersteunt, zoals Azure Blob, Azure Data Lake Storage, Azure Key Vault, enzovoort.

Bij het verlenen van machtigingen, op het tabblad Access Control van Azure resource-> roltoewijzing toevoegen-> toegang toewijzen aan-> Selecteer Data Factory onder door het systeem toegewezen beheerde identiteit-> Selecteer op fabrieks naam. of in het algemeen kunt u de object-ID of data factory naam (als beheerde identiteits naam) gebruiken om deze identiteit te vinden. Als u de toepassings-ID van de beheerde identiteit wilt ophalen, kunt u Power shell gebruiken.

### <a name="retrieve-managed-identity-using-powershell"></a>Beheerde identiteit ophalen met behulp van Power shell

De ID van de beheerde ID en Tenant-ID wordt als volgt weer gegeven wanneer u een specifieke data factory ontvangt. Gebruik de **PrincipalId** om toegang te verlenen:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

U kunt de toepassings-ID ophalen door boven de principal-ID te kopiëren en vervolgens onder Azure Active Directory opdracht met de principal-ID als para meter.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>Beheerde identiteit ophalen met behulp van REST API

De ID van de beheerde ID en Tenant-ID wordt als volgt weer gegeven wanneer u een specifieke data factory ontvangt.

Bel de volgende API in de aanvraag:

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**Antwoord**: er wordt een antwoord weer gegeven zoals in het onderstaande voor beeld. De sectie ' identiteit ' is dienovereenkomstig ingevuld.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> Als u de beheerde identiteit uit een ARM-sjabloon wilt ophalen, voegt u een sectie **outputs** toe aan de arm van het been:

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende onderwerpen voor meer informatie over het gebruik van data factory beheerde identiteit:

- [Referenties opslaan in Azure Key Vault](store-credentials-in-key-vault.md)
- [Gegevens kopiëren van/naar Azure Data Lake Store met behulp van beheerde identiteiten voor Azure-bronnen verificatie](connector-azure-data-lake-store.md)

Zie [beheerde identiteiten voor Azure-bronnen overzicht](../active-directory/managed-identities-azure-resources/overview.md) voor meer achtergrond informatie over beheerde identiteiten voor Azure-resources, waarop Data Factory beheerde identiteit is gebaseerd.
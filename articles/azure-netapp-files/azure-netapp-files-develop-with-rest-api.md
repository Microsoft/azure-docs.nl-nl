---
title: Ontwikkel voor Azure NetApp Files met REST API | Microsoft Docs
description: De REST API voor de Azure NetApp Files-service definieert HTTP-bewerkingen voor resources, zoals het NetApp-account, de capaciteits pool, de volumes en moment opnamen.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/02/2020
ms.author: b-juche
ms.openlocfilehash: c5993dc1dc645319e272ab310a97bc3ff8ac495d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174237"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>Ontwikkelen voor Azure NetApp Files met REST API 

De REST API voor de Azure NetApp Files-service definieert HTTP-bewerkingen op basis van resources, zoals het NetApp-account, de capaciteits pool, de volumes en moment opnamen. Dit artikel helpt u aan de slag te gaan met het gebruik van de Azure NetApp Files REST API.

## <a name="azure-netapp-files-rest-api-specification"></a>Specificatie van Azure NetApp Files REST API

De REST API-specificatie voor Azure NetApp Files wordt gepubliceerd via [github](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager):

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`


## <a name="access-the-azure-netapp-files-rest-api"></a>Toegang tot de Azure NetApp Files REST API  

1. [Installeer de Azure cli](/cli/azure/install-azure-cli) als u dit nog niet hebt gedaan.
2. Een service-principal maken in uw Azure Active Directory (Azure AD):
   1. Controleer of u [voldoende machtigingen](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)hebt.

   2. Voer de volgende opdracht in Azure CLI in: 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE
        ```

      De uitvoer van de opdracht is vergelijkbaar met het volgende voor beeld:  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      Behoud de uitvoer van de opdracht.  U hebt de `appId` waarden, `password` en nodig `tenant` . 

3. Een OAuth-toegangs token aanvragen:

    De voor beelden in dit artikel gebruiken krul. U kunt ook verschillende API-hulpprogram ma's gebruiken, zoals [postman](https://www.getpostman.com/), [slapeloosheid](https://insomnia.rest/)en [paw](https://paw.cloud/).  

    Vervang de variabelen in het volgende voor beeld door de opdracht uitvoer van stap 2 hierboven. 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    De uitvoer biedt een toegangs token dat lijkt op het volgende voor beeld:

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    Het weer gegeven token is 3600 seconden geldig. Daarna moet u een nieuw token aanvragen. 
    Sla het token op in een tekst editor.  U hebt deze nodig voor de volgende stap.

4. Een test oproep verzenden en het token opnemen om uw toegang tot de REST API te valideren:

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>Voor beelden met behulp van de API  

In dit artikel wordt de volgende URL gebruikt voor de basis lijn van aanvragen. Deze URL verwijst naar de hoofdmap van de naam ruimte Azure NetApp Files. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

Vervang de `SUBIDGOESHERE` `RESOURCEGROUPGOESHERE` waarden en in de volgende voor beelden door uw eigen waarden. 

### <a name="get-request-examples"></a>Voor beelden van aanvragen ophalen

U gebruikt een GET-aanvraag voor het opvragen van objecten van Azure NetApp Files in een abonnement, zoals in de volgende voor beelden wordt weer gegeven: 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>Voor beelden van PUT-aanvraag

U gebruikt een PUT-aanvraag om nieuwe objecten te maken in Azure NetApp Files, zoals in de volgende voor beelden wordt weer gegeven. De hoofd tekst van de PUT-aanvraag kan de met JSON opgemaakte gegevens bevatten voor de wijzigingen. Het moet worden opgenomen in de krul opdracht als tekst of als een bestand. Als u wilt verwijzen naar de hoofd tekst als bestand, slaat u het JSON-voor beeld op in een bestand en voegt `-d @<filename>` u toe aan de opdracht krul.

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
```

### <a name="json-examples"></a>JSON-voor beelden

In het volgende voor beeld ziet u hoe u een NetApp-account maakt:

```json
{ 
    "name": "MYNETAPPACCOUNT", 
    "type": "Microsoft.NetApp/netAppAccounts", 
    "location": "westus2", 
    "properties": { 
        "name": "MYNETAPPACCOUNT" 
    }
} 
```

In het volgende voor beeld ziet u hoe u een capaciteits groep maakt: 

```json
{
    "name": "MYNETAPPACCOUNT/POOLNAME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
    "location": "westus2",
    "properties": {
        "name": "POOLNAME",
        "size": "4398046511104",
        "serviceLevel": "Premium"
    }
}
```

In het volgende voor beeld ziet u hoe u een nieuw volume maakt. (Het standaard protocol voor het volume is NFSV3.) 

```json
{
    "name": "MYNEWVOLUME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
    "location": "westus2",
    "properties": {
        "serviceLevel": "Premium",
        "usageThreshold": "322122547200",
        "creationToken": "MY-FILEPATH",
        "snapshotId": "",
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
         }
}
```

In het volgende voor beeld ziet u hoe u een moment opname maakt van een volume: 

```json
{
    "name": "apitest2/apiPool01/apiVol01/snap02",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
    "location": "westus2",
    "properties": {
         "name": "snap02",
        "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
    }
}
```

> [!NOTE] 
> U moet opgeven `fileSystemId` voor het maken van een moment opname.  U kunt de `fileSystemId` waarde ophalen met een GET-aanvraag naar een volume. 

## <a name="next-steps"></a>Volgende stappen

[Zie de naslag informatie voor Azure NetApp Files REST API](/rest/api/netapp/)
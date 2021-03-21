---
title: Registreren voor Azure NetApp Files | Microsoft Docs
description: Meer informatie over het registreren voor Azure NetApp Files door het verzenden van een Waitlist-aanvraag en het registreren van de Azure-resource provider voor Azure NetApp Files.
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
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: a61d6ba1f908a7dae56db066bfae329fc26f1c5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696129"
---
# <a name="register-for-azure-netapp-files"></a>Registreren voor Azure NetApp Files

> [!IMPORTANT] 
> Voordat u de Azure NetApp Files Resource provider registreert, moet u een e-mail adres hebben ontvangen van het Azure NetApp Files team bevestigen dat u toegang hebt gekregen tot de service. 

In dit artikel wordt beschreven hoe u zich registreert voor Azure NetApp Files, zodat u de service kunt gaan gebruiken.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Een Waitlist-aanvraag indienen voor toegang tot de service

1. Ga naar de volgende pagina en verzend een Waitlist-verzoek om toegang te krijgen tot de Azure NetApp Files-service:  
    [**Waitlist-pagina voor het verzenden van Azure NetApp Files**](https://aka.ms/azurenetappfiles) 

    Waitlist-aanmelding biedt geen garantie voor directe toegang tot de service. 

2. Wacht op een officiële bevestigings-e-mail van het Azure NetApp Files team voordat u doorgaat met andere taken. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>De NetApp-resourceprovider registreren

Voor het gebruik van de service moet u de Azure-resourceprovider registreren voor Azure NetApp Files.

> [!NOTE] 
> U kunt de NetApp-resource provider ook registreren zonder dat hiervoor toegang is verleend voor de service. Zonder toegangs autorisatie wordt elke Azure Portal-of API-aanvraag voor het maken van een NetApp-account of een andere Azure NetApp Files resource echter afgewezen met de volgende fout:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Klik op het pictogram voor Azure Cloud Shell in de rechterbovenhoek van Azure Portal:

      ![Pictogram voor Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Als u meerdere abonnementen hebt op uw Azure-account, selecteert u het abonnement dat is goedgekeurd voor Azure NetApp Files:
    
    ```azurepowershell
    az account set --subscription <subscriptionId>
    ```

3. Voer in de Azure Cloud Shell-console de volgende opdracht in om te controleren of uw abonnement is goedgekeurd:
    
    ```azurepowershell
    az feature list | grep NetApp
    ```

   De uitvoer van de opdracht ziet er als volgt uit:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` is uw abonnements-id.

    Als u de naam van de functie niet ziet `Microsoft.NetApp/ANFGA` , hebt u geen toegang tot de service. Stop bij deze stap. Volg de instructies in [een Waitlist-aanvraag verzenden om toegang te krijgen tot de service](#waitlist) om service toegang aan te vragen voordat u doorgaat. 

4. Voer de volgende opdracht uit om de Azure-resourceprovider te registreren in de Azure Cloud Shell-console: 
    
    ```azurepowershell
    az provider register --namespace Microsoft.NetApp --wait
    ```

   Met de parameter `--wait` wordt de console geïnstrueerd te wachten totdat het registratieproces is voltooid. Dit kan enige tijd duren.

5. Voer de volgende opdracht uit in de Azure Cloud Shell-console om te controleren of de Azure-resourceprovider is geregistreerd: 
    
    ```azurepowershell
    az provider show --namespace Microsoft.NetApp
    ```

   De uitvoer van de opdracht ziet er als volgt uit:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` is uw abonnements-id.  De parameterwaarde `state` geeft `Registered` aan.

6. Klik in Azure Portal op de blade **Abonnementen**.
7. Klik in de blade Abonnementen op uw abonnements-id. 
8. Klik in de instellingen van het abonnement op **Resourceproviders** om te controleren of de Microsoft.NetApp-provider de status Geregistreerd heeft: 

      ![Microsoft.NetApp geregistreerd](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Volgende stappen

[Een NetApp-account maken](azure-netapp-files-create-netapp-account.md)

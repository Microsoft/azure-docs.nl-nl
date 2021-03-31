---
title: Een NetApp-account maken voor toegang tot Azure NetApp Files | Microsoft Docs
description: Beschrijft hoe u toegang tot Azure NetApp Files verkrijgt en een NetApp-account maakt zodat u een capaciteitspool kunt instellen en een volume kunt maken.
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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: e5e0c43aa8c4fbf0db89065e0fab93ee174a4ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006590"
---
# <a name="create-a-netapp-account"></a>Een NetApp-account maken
Als u een NetApp-account maakt, kunt u een capaciteitspool instellen en vervolgens een volume maken. U gebruikt de Azure NetApp Files-blade om een nieuw NetApp-account te maken.

## <a name="before-you-begin"></a>Voordat u begint
U moet een e-mail adres hebben ontvangen van het Azure NetApp Files team bevestigen dat u toegang hebt gekregen tot de service. Zie [een Waitlist-aanvraag indienen voor toegang tot de service](azure-netapp-files-register.md#waitlist).

U moet uw abonnement ook hebben geregistreerd voor het gebruik van de NetApp-resource provider. Zie [de NetApp-resource provider registreren](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Stappen 

1. Meld u aan bij Azure Portal. 
2. Verkrijg toegang tot de Azure NetApp Files-blade via een van de volgende methoden:  
   * Zoek naar **Azure NetApp Files** in het zoekvak van de Azure-portal.  
   * Klik in de navigatie op **Alle services** en filter vervolgens op Azure NetApp Files.  

   U kunt de Azure NetApp Files-blade als favoriet opslaan door op het sterpictogram ernaast te klikken. 

3. Klik op **+ Toevoegen** om een nieuw NetApp-account te maken.  
   Het venster Nieuw NetApp-account verschijnt.  

4. Geef de volgende informatie voor uw NetApp-account op: 
   * **Accountnaam**  
     Geef een unieke naam voor het abonnement op.
   * **Abonnement**  
     Kies een abonnement uit uw bestaande abonnementen.
   * **Resource groep**   
     Gebruik een bestaande resourcegroep of maak een nieuwe.
   * **Locatie**  
     Selecteer de regio waarin u wilt dat het account en zijn onderliggende resources zich bevinden.  

     ![Nieuw NetApp-account](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Klik op **Create**.     
   Het NetApp-account dat u hebt gemaakt, verschijnt nu in de Azure NetApp Files-blade. 

> [!NOTE] 
> Als u geen toegang hebt gekregen tot de Azure NetApp Files-service, wordt de volgende fout weer gegeven wanneer u probeert om het eerste NetApp-account te maken:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Volgende stappen  

[Een capaciteitspool instellen](azure-netapp-files-set-up-capacity-pool.md)


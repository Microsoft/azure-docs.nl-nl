---
title: Een Pivotal Cloud Foundry-cluster maken op Azure
description: Meer informatie over het instellen van parameters die nodig zijn voor het inrichten van een PCF-cluster (Pivotal Cloud Foundry) op Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: 3444d6c62b5e36b7e68cfaf6da1ec534e2ea4ec6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551450"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Een Pivotal Cloud Foundry-cluster maken op Azure

Deze zelfstudie biedt snelle stappen voor het maken en genereren van parameters voor het inrichten van een PCF-cluster (Pivotal Cloud Foundry) op Azure. U vindt de Pivotal Cloud Foundry-oplossing door het uitvoeren van een zoekopdracht in Azure [MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Pivotal Cloud Foundry zoeken in Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Een openbare SSH-sleutel genereren

Er zijn verschillende manieren om een openbare SSH-sleutel te genereren met behulp van Windows, Mac of Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Zie voor meer informatie, [SSH-sleutels gebruiken met Windows op Azure](../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-service-principal"></a>Een service-principal maken

> [!NOTE]
>
> Voor het maken van een service-principal is een machtiging voor het eigenaarsaccount vereist. U kunt bovendien een script schrijven om het maken van de service-principal te automatiseren. Bijvoorbeeld met behulp van de Azure CLI-opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp).

1. Meld u aan bij uw Azure-account.

    ```azurecli
    az login
    ```

    ![Aanmelding bij Azure CLI](media/deploy/az-login-output.png )
 
    Kopieer de waarde 'id' als uw **abonnements-ID** en kopieer de waarde 'tenantId' voor later gebruik.

2. Stel uw standaardabonnement voor deze configuratie in.

    ```azurecli
    az account set -s {id}
    ```

3. Een Azure Active Directory-toepassing maken voor uw PCF. Geef een uniek alfanumeriek wachtwoord op. Sla het wachtwoord op als uw **clientSecret** voor later gebruik.

    ```azurecli
    az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}
    ```

    Kopieer de waarde 'appId' in de uitvoer als uw **ClientID** voor later gebruik.

    > [!NOTE]
    >
    > Kies uw eigen toepassingsstartpagina en id-URI, bijvoorbeeld http\://www\.contoso.com.

4. Maak een service-principal met uw nieuwe app-id.

    ```azurecli
    az ad sp create --id {appId}
    ```

5. Stel de machtigingsrol van de service-principal in als Inzender.

    ```azurecli
    az role assignment create --assignee "{enter-your-homepage}" --role "Contributor"
    ```

    U kunt ook

    ```azurecli
    az role assignment create --assignee {service-principal-name} --role "Contributor"
    ```

    ![de roltoewijzing van de service-principal gebruiken](media/deploy/svc-princ.png )

6. Controleer of u zich kunt aanmelden bij uw service-principal met behulp van de app-id, het wachtwoord en de tenant-id.

    ```azurecli
    az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}
    ```

7. Maak een JSON-bestand in de volgende indeling. Gebruik de waarden van **abonnements-id**, **tenantID**, **clientID** en **clientSecret** die u eerder hebt gekopieerd. Sla het bestand op.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Het Pivotal Network-token ophalen

1. Registreer u of meld u aan bij uw [Pivotal Network](https://network.pivotal.io)-account.
2. Selecteer uw profielnaam rechtsboven op de pagina. Selecteer **Profiel bewerken**.
3. Ga naar de onderkant van de pagina en kopieer de waarde van **LEGACY API TOKEN**. Dit is de waarde die u later zult gebruiken als uw **Pivotal Network-token**.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Cloud Foundry-cluster op Azure inrichten

Nu hebt u alle parameters die nodig zijn voor het inrichten van uw [Pivotal Cloud Foundry-cluster op Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Voer de parameters in en maak uw PCF-cluster.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Controleer de implementatie en meld u aan bij Pivotal Ops Manager

1. Er wordt nu in uw PCF-cluster een implementatiestatus weergegeven.

    ![Azure-implementatiestatus](media/deploy/deployment.png )

2. Selecteer de koppeling **Deployments** (Implementaties) in de navigatiebalk aan de linkerkant om referenties voor uw PCF Ops Manager op te halen. Selecteer de **Deployment Name** (naam van de implementatie) op de volgende pagina.
3. Selecteer de koppeling **Outputs** (uitvoergegevens) om de URL, de gebruikersnaam en het wachtwoord voor de PCF Ops Manager weer te geven. De waarde 'OPSMAN-FQDN' is de URL.
 
    ![Uitvoer voor Cloud Foundry-implementatie](media/deploy/deploy-outputs.png )
 
4. Start de URL in een webbrowser. Voer de referenties van de vorige stap in om u aan te melden.

    ![Aanmeldingspagina van Pivotal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Als het met Internet Explorer als browser niet lukt vanwege een waarschuwingsbericht dat de site niet veilig is, klikt u op **Meer informatie** en gaat u naar de webpagina. Klik in Firefox op **Advance** en voeg de certificering toe om door te gaan.

5. In PCF Ops Manager worden de geïmplementeerde Azure-exemplaren weergeven. Nu kunt u uw toepassingen hier implementeren en beheren.
               
    ![Geïmplementeerd Azure-exemplaar in Pivotal](media/deploy/ops-mgr.png )

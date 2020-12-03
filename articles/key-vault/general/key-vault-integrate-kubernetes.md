---
title: Azure Key Vault integreren met Kubernetes
description: In deze zelfstudie gaat u geheimen openen en ophalen uit Azure Key Vault met behulp van het CSI-stuurprogramma (Secrets Store Container Storage Interface), om deze vervolgens te koppelen aan Kubernetes-pods.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: c628ba780ae64fceb32322fdb2004d69e2ebf24b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452753"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Zelfstudie: De Azure Key Vault-provider voor het stuurprogramma voor het Secrets Store CSI-stuurprogramma configureren en uitvoeren op Kubernetes

> [!IMPORTANT]
> CSI-stuurprogramma is een open-sourceproject dat niet wordt ondersteund door de technische ondersteuning van Azure. Meld alle feedback en problemen met betrekking tot Key Vault-integratie met het CSI-stuurprogramma via de GitHub-koppeling onderaan de pagina. Dit hulpprogramma is bedoeld voor gebruikers om zelf te installeren in clusters en om feedback te verzamelen van onze community.

In deze zelfstudie gaat u geheimen openen en ophalen uit Azure Key Vault met behulp van het stuurprogramma Secrets Store CSI, en deze vervolgens koppelen aan Kubernetes-pods.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een service-principal maken of beheerde identiteiten gebruiken.
> * Een Azure Kubernetes Service-cluster (AKS) implementeren met behulp van de Azure CLI.
> * Helm en het stuurprogramma Secrets Store CSI installeren.
> * Een Azure-sleutelkluis maken en geheimen instellen.
> * Uw eigen SecretProviderClass-object maken.
> * Uw service-principal toewijzen of beheerde identiteiten gebruiken.
> * Uw pod met gekoppelde geheimen uit uw sleutelkluis implementeren.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

* Voordat u met deze zelfstudie begint, moet u [Azure CLI](/cli/azure/install-azure-cli-windows?view=azure-cli-latest) installeren.

## <a name="create-a-service-principal-or-use-managed-identities"></a>Een service-principal maken of beheerde identiteiten gebruiken

Als u beheerde identiteiten wilt gebruiken, kunt u doorgaan naar de volgende sectie.

Maak een service-principal om te bepalen welke resources vanuit uw Azure-sleutelkluis toegankelijk zijn. De toegang van deze service-principal wordt beperkt door de rollen die eraan toegewezen zijn. Met deze functie bepaalt u hoe de service-principal uw geheimen kan beheren. In het volgende voorbeeld is de naam van de service-principal *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Met deze bewerking wordt een reeks sleutel-waardeparen geretourneerd:

![Schermopname van de app-id en het wachtwoord voor contosoServicePrincipal](../media/kubernetes-key-vault-1.png)

Kopieer de **app-id** en het **wachtwoord** voor later gebruik.

## <a name="flow-for-using-managed-identity"></a>Stroom voor het gebruik van Beheerde identiteit

Dit diagram illustreert de AKS/Key Vault-integratiestroom voor Beheerde identiteit:

![Diagram met weergave van de AKS/Key Vault-integratiestroom voor Beheerde identiteit](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Een Azure Kubernetes Service-cluster (AKS) implementeren met behulp van de Azure CLI

U hoeft Azure Cloud Shell niet te gebruiken. De opdrachtprompt (terminal) waarop de Azure CLI is geïnstalleerd, is voldoende. 

Voltooi de secties 'Een resource groep maken', 'AKS-cluster maken' en 'Verbinding maken met het cluster' in [Deploy an Azure Kubernetes Service cluster by using the Azure CLI](../../aks/kubernetes-walkthrough.md) (Een Azure Kubernetes Service-cluster implementeren met de Azure CLI). 

> [!NOTE] 
> Als u van plan bent een pod-identiteit te gebruiken in plaats van een service-principal, moet u deze inschakelen wanneer u het Kubernetes-cluster maakt, zoals wordt weergegeven in de volgende opdracht:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Stel de PATH-omgevingsvariabele in](https://www.java.com/en/download/help/path.xml) op het gedownloade bestand *kubectl.exe*.
1. Controleer uw Kubernetes-versie met behulp van de volgende opdracht, waarmee de client- en serverversie worden gegenereerd. De clientversie is het *kubectl.exe*-bestand dat u hebt geïnstalleerd en de serverversie is de versie van Azure Kubernetes Service (AKS) waarop uw cluster wordt uitgevoerd.
    ```azurecli
    kubectl version
    ```
1. Zorg ervoor dat uw Kubernetes-versie 1.16.0 of hoger is. Zorg er voor Windows-clusters voor dat uw Kubernetes-versie 1.18.0 of hoger is. Met de volgende opdracht worden zowel het Kubernetes-cluster als de knooppuntpool bijgewerkt. Het uitvoeren van de opdracht kan een paar minuten duren. In dit voorbeeld is de resourcegroep *contosoResourceGroup* en is het Kubernetes-cluster *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Gebruik de volgende opdracht om de metagegevens weer te geven van het AKS-cluster dat u hebt gemaakt. Kopieer de waarden van **principalId**, **clientId**, **subscriptionId** en **nodeResourceGroup** voor later gebruik. Als het ASK-cluster niet is gemaakt met beheerde identiteiten ingeschakeld, zijn **principalId** en **clientId** null. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    In de uitvoer zijn beide parameters gemarkeerd:
    
    ![Schermopname van de Azure CLI met de waarden voor principalId en clientId gemarkeerd](../media/kubernetes-key-vault-2.png) ![Schermopname van de Azure CLI met de waarden voor subscriptionId en nodeResourceGroup gemarkeerd](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Helm en het stuurprogramma Secrets Store CSI installeren

U moet [Helm](https://helm.sh/docs/intro/install/) installeren om het stuurprogramma Secrets Store CSI te kunnen installeren.

Met de interface van het [Secrets Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md)-stuurprogramma kunt u de geheimen ophalen die zijn opgeslagen in uw Azure-sleutelkluisexemplaar en vervolgens de interface van het stuurprogramma gebruiken om de geheime inhoud aan Kubernetes-pods te koppelen.

1. Controleer of de Helm-versie v3 of hoger is:
    ```azurecli
    helm version
    ```
1. Installeer het stuurprogramma Secrets Store CSI en de Azure Key Vault-provider voor het stuurprogramma:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Een Azure-sleutelkluis maken en geheimen instellen

Als u uw eigen sleutel kluis wilt maken en uw geheimen wilt instellen, volgt u de instructies in [Set and retrieve a secret from Azure Key Vault by using the Azure CLI](../secrets/quick-create-cli.md) (Een geheim instellen en ophalen van Azure Key Vault met behulp van de Azure CLI).

> [!NOTE] 
> U hoeft niet Azure Cloud Shell te gebruiken of een nieuwe resourcegroep te maken. U kunt de resourcegroep gebruiken die u eerder hebt gemaakt voor het Kubernetes-cluster.

## <a name="create-your-own-secretproviderclass-object"></a>Uw eigen SecretProviderClass-object maken

[Gebruik deze sjabloon](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml) om uw eigen aangepast SecretProviderClass-object te maken met providerspecifieke parameters voor het stuurprogramma Secrets Store CSI. Dit object biedt identiteit- en toegangsbeheer voor uw sleutelkluis.

Vul in het SecretProviderClass YAML-voorbeeldbestand de ontbrekende parameters in. De volgende parameters zijn vereist:

* **userAssignedIdentityID**: # [VEREIST] Als u gebruikmaakt van een service-principal, gebruikt u de client-id om op te geven welke door de gebruiker toegewezen beheerde identiteit moet worden gebruikt. Als u een door de gebruiker toegewezen identiteit gebruikt als de beheerde identiteit van de VM, geeft u de client-id van de identiteit op. Als de waarde leeg is, wordt standaard de door het systeem toegewezen identiteit op de VM gebruikt 
* **keyvaultName**: De naam van de sleutelkluis
* **objects**: De container voor alle geheime inhoud die u wilt koppelen
    * **objectName**: De naam van de geheime inhoud
    * **objectType**: Het objecttype (geheim, sleutel, certificaat)
* **resourceGroup**: De naam van de resourcegroep # [VEREIST voor versie < 0.0.4] de resourcegroep van de sleutelkluis
* **subscriptionId**: De abonnements-id van uw sleutelkluis # [VEREIST voor versie < 0.0.4] de abonnements-id van de hoofdkluis
* **tenantID**: De tenant-id of map-id van de sleutelkluis

Documentatie over alle vereiste velden is hier beschikbaar: [Koppeling](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

De bijgewerkte sjabloon wordt weergegeven in de volgende code. Download deze als een YAML-bestand en vul de vereiste velden in. In dit voorbeeld is de sleutelkluis **contosoKeyVault5**. De kluis heeft twee geheimen: **secret1** en **secret2**.

> [!NOTE] 
> Als u beheerde identiteiten gebruikt, stelt u de waarde voor **usePodIdentity** in op *Waar* en stelt u de waarde **userAssignedIdentityID** in als een paar aanhalingstekens ( **""** ). 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
In de volgende afbeelding ziet u de uitvoer van de console voor **az keyvault show --name contosoKeyVault5** met de relevante gemarkeerde metagegevens:

![Schermopname van de console-uitvoer voor 'az keyvault show --name contosoKeyVault5'](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Uw service-principal toewijzen of beheerde identiteiten gebruiken

### <a name="assign-a-service-principal"></a>Een service-principal toewijzen

Als u een service-principal gebruikt, verleent u deze machtigingen om uw sleutelkluis te openen en geheimen op te halen. Wijs de rol *Lezer* toe en geef de service-principal toestemming om geheimen *op te halen* uit uw sleutelkluis met behulp van de volgende opdracht:

1. Wijs uw service-principal toe aan uw bestaande sleutelkluis. De parameter **$AZURE_CLIENT_ID** is de **appId** die u hebt gekopieerd na het maken van uw service-principal.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    De uitvoer van de opdracht wordt in de volgende afbeelding weergegeven: 

    ![Schermopname van de principalId-waarde](../media/kubernetes-key-vault-5.png)

1. De service-principal machtigen geven voor het ophalen van geheimen:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    az keyvault set-policy -n $KEYVAULT_NAME --key-permissions get --spn $AZURE_CLIENT_ID
    ```

1. U hebt nu de service-principal geconfigureerd met machtigingen voor het lezen van geheimen uit uw sleutelkluis. **$AZURE_CLIENT_SECRET** is het wachtwoord van uw service-principal. Uw service-principal-referenties toevoegen als een Kubernetes-geheim dat toegankelijk is voor het stuurprogramma Secrets Store CSI:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Als u de Kubernetes-pod implementeert en er een fout voor een ongeldige clientgeheim-id wordt weergegeven, hebt u mogelijk een oudere clientgeheim-id die is verlopen of opnieuw is ingesteld. U kunt dit probleem oplossen door uw geheim *secrets-store-creds* te verwijderen en een nieuw geheim te maken met de huidige clientgeheim-id. Als u uw *secrets-store-creds* wilt verwijderen, voert u de volgende opdracht uit:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Als u de clientgeheim-id van uw service-principal vergeten bent, kunt u deze opnieuw instellen met de volgende opdracht:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Beheerde identiteiten gebruiken

Als u beheerde identiteiten gebruikt, wijst u specifieke rollen toe aan het AKS-cluster dat u hebt gemaakt. 

1. Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, vermelden of lezen, moet aan uw AKS-cluster de rol [Operator beheerde identiteit](../../role-based-access-control/built-in-roles.md#managed-identity-operator) worden toegewezen. Zorg ervoor dat de **$clientId** de client-id van het Kubernetes-cluster is. Het bereik valt onder uw Azure-abonnementsservice, met name de knooppuntresourcegroep die is gemaakt toen het AKS-cluster werd gemaakt. Met dit bereik worden alleen resources in deze groep beïnvloed door de rollen die hieronder worden toegewezen. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Installeer de Azure Active Directory-identiteit (Azure AD) in AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Maak een Azure Active Directory-identiteit. Kopieer in de uitvoer de **clientId** en **principalId** voor later gebruik.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Wijs de rol *Lezer* toe aan de Azure AD-identiteit die u in de voorgaande stap voor uw sleutelkluis hebt gemaakt, en verleen de identiteit machtigingen om geheimen van uw sleutelkluis op te halen. Gebruik de **clientId** en **principalId** van de Azure AD-identiteit.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Uw pod met gekoppelde geheimen implementeren vanuit uw sleutelkluis

Als u uw SecretProviderClass-object wilt configureren, voert u de volgende opdracht uit:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Een service-principal gebruiken

Als u een service-principal gebruikt, gebruikt u de volgende opdracht om uw Kubernetes-pods te implementeren met de SecretProviderClass en de secrets-store-creds die u eerder hebt geconfigureerd. Dit zijn de implementatiesjablonen:
* Voor [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml)
* Voor [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>Beheerde identiteiten gebruiken

Als u beheerde identiteiten gebruikt, maakt u een *AzureIdentity* in uw cluster die verwijst naar de identiteit die u eerder hebt gemaakt. Maak vervolgens een *AzureIdentityBinding* die verwijst naar de AzureIdentity die u hebt gemaakt. Vul de parameters in de volgende sjabloon in en sla deze op als *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Voer de volgende opdracht uit om de binding uit te voeren:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Vervolgens implementeert u de pod. De volgende code is het YAML-implementatiebestand, dat gebruikmaakt van de pod-id-binding uit de vorige stap. Sla dit bestand op als *podBindingDeployment.yaml*.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
        nodePublishSecretRef:           # Only required when using service principal mode
          name: secrets-store-creds     # Only required when using service principal mode
```

Voer de volgende opdracht uit om de pod te implementeren:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>De status en de geheime inhoud van de pod controleren 

Voer de volgende opdracht uit om de pods weer te geven die u hebt geïmplementeerd:
```azurecli
kubectl get pods
```

Voer de volgende opdracht uit om de status van uw pod te controleren:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Schermopname van de Azure CLI-uitvoer met de status 'In uitvoering' van de pod en alle gebeurtenissen weergegeven als 'normaal' ](../media/kubernetes-key-vault-6.png)

In het uitvoervenster moet de geïmplementeerde pod de status *In uitvoering* hebben. In de sectie **Gebeurtenissen** onderaan worden alle gebeurtenistypen weergegeven als *Normaal*.

Nadat u hebt gecontroleerd of de pod wordt uitgevoerd, kunt u controleren of de pod de geheimen van uw sleutelkluis bevat.

Als u alle geheimen die zijn opgenomen in de pod wilt weergeven, voert u de volgende opdracht uit:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Als u de inhoud van een specifiek geheim wilt weergeven, voert u de volgende opdracht uit:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Controleer of de inhoud van het geheim wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

Om ervoor te zorgen dat uw sleutelkluis kan worden hersteld, raadpleegt u:
> [!div class="nextstepaction"]
> [Voorlopig verwijderen inschakelen](./key-vault-recovery.md)
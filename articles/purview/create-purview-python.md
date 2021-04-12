---
title: 'Quick Start: een controle sfeer liggen-account maken met behulp van python'
description: Maak een Azure controle sfeer liggen-account met behulp van python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387477"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Quick Start: een controle sfeer liggen-account maken met behulp van python

In deze Quick Start maakt u een controle sfeer liggen-account met behulp van python. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Uw eigen [Azure Active Directory-tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Uw account moet gemachtigd zijn om resources te maken in het abonnement

* Als **Azure Policy** alle toepassingen ervan weerhoudt om een **Storage-account** en een **EventHub-naamruimte** te maken, moet u met behulp van tags uitzonderingen voor het beleid instellen. Deze kunt u invoeren tijdens het proces voor het maken van een Purview-account. De belangrijkste reden hiervoor is dat voor elk gemaakt Purview-account een beheerde resourcegroep wordt gemaakt met daarin een Storage-account en een EventHub-naamruimte. Raadpleeg voor meer informatie [catalogus portal maken](create-catalog-portal.md)


## <a name="install-the-python-package"></a>Het Python-pakket installeren

1. Open een terminal of opdrachtprompt met beheerdersbevoegdheden. 
2. Installeer eerst het Python-pakket voor Azure-beheerresources:

    ```python
    pip install azure-mgmt-resource
    ```
3. Als u het python-pakket voor controle sfeer liggen wilt installeren, voert u de volgende opdracht uit:

    ```python
    pip install azure-mgmt-purview
    ```

    De [python-SDK voor controle sfeer liggen](https://github.com/Azure/azure-sdk-for-python) biedt ondersteuning voor python 2,7, 3,3, 3,4, 3,5, 3,6 en 3,7.

4. Voer de volgende opdracht uit om het Python-pakket voor Azure Identity Authentication te installeren:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > Het pakket 'azure-identity' bevat mogelijk conflicten met 'azure-cli' voor enkele algemene afhankelijkheden. Als u een verificatieprobleem ondervindt, verwijdert u 'azure-cli' en de bijbehorende afhankelijkheden. U kunt ook een schone machine gebruiken zonder het pakket 'azure-cli' te installeren.
    
## <a name="create-a-purview-client"></a>Een controle sfeer liggen-client maken

1. Maak een bestand met de naam **purview.py**. Voeg de volgende instructies toe om verwijzingen naar naamruimten toe te voegen.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Voeg de volgende code toe aan de methode **Main** om een instantie van de klasse PurviewManagementClient te maken. U gebruikt dit object om een controle sfeer liggen-account te maken, een controle sfeer liggen-account te verwijderen, de beschik baarheid van de naam en andere bewerkingen van de resource provider te controleren.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Een controle sfeer liggen-account maken

Voeg de volgende code toe aan de methode **Main** om een **controle sfeer liggen-account** te maken. Als uw resourcegroep al bestaat, maakt u van de eerste `create_or_update`-instructie een commentaar.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Voeg nu de volgende instructie toe om de methode **Main** aan te roepen wanneer het programma wordt uitgevoerd:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Volledige script

Dit is de volledige Python-code:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>De code uitvoeren

Bouw en start de toepassing en controleer vervolgens de uitvoering van de pijplijn.

In de console wordt de voortgang weergegeven van het maken van een data factory, een gekoppelde service, gegevenssets, pijplijn en pijplijnuitvoering. Wacht totdat u details ziet van de uitvoering van de kopieeractiviteit, waaronder de omvang van de gelezen/weggeschreven gegevens. Gebruik vervolgens hulpprogramma's als [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om te controleren of de blob(s) is/zijn gekopieerd van het 'inputBlobPath' naar het 'outputBlobPath' zoals u hebt opgegeven in de variabelen.

Hier volgt een voorbeeld van uitvoer:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>De uitvoer controleren

Ga naar de pagina **controle sfeer liggen accounts** in het Azure Portal en controleer het account dat is gemaakt met behulp van de bovenstaande code. 

## <a name="delete-purview-account"></a>Controle sfeer liggen-account verwijderen

Als u het controle sfeer liggen-account wilt verwijderen, voegt u de volgende code toe aan het programma:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Volgende stappen

Met de code in deze zelf studie maakt u een controle sfeer liggen-account en verwijdert u een controle sfeer liggen-account. U kunt nu de python-SDK downloaden en meer informatie over andere acties van de resource provider die u kunt uitvoeren voor een controle sfeer liggen-account.

Ga naar het volgende artikel voor informatie over hoe u gebruikers toegang kunt geven tot uw Azure Purview-account. 

> [!div class="nextstepaction"]
> [Gebruikers toevoegen aan uw Azure Purview-account](catalog-permissions.md)

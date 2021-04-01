---
title: Een toepassing toegang verlenen tot andere Azure-resources
description: In dit artikel wordt uitgelegd hoe u uw beheerde identiteits Service Fabric toegang tot toepassingen kunt verlenen aan andere Azure-resources die op Azure Active Directory gebaseerde verificatie ondersteunen.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: c7560294fbf6d122396b6a5a8ffd3ee93bc89048
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97507452"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Het verlenen van toegang tot Azure-resources door de beheerde identiteit van een Service Fabric-toepassing

Voordat de toepassing de beheerde identiteit voor toegang tot andere bronnen kan gebruiken, moeten er machtigingen worden verleend aan deze identiteit op de beveiligde Azure-resource die wordt geopend. Het verlenen van machtigingen is doorgaans een beheer actie op het ' besturings vlak ' van de Azure-service die eigenaar is van de beveiligde resource die wordt gerouteerd via Azure Resource Manager, waardoor alle toepasselijke op rollen gebaseerde toegangs controle wordt afgedwongen.

De exacte volg orde van de stappen is afhankelijk van het type Azure-resource waartoe toegang wordt verkregen en de taal/client die wordt gebruikt om machtigingen te verlenen. In de rest van het artikel wordt ervan uitgegaan dat er een door de gebruiker toegewezen identiteit aan de toepassing is toegewezen en een aantal typische voor beelden bevat voor uw gemak, maar dit is op geen enkele manier een uitgebreide verwijzing naar dit onderwerp. Raadpleeg de documentatie van de betreffende Azure-Services voor actuele instructies voor het verlenen van machtigingen.  

## <a name="granting-access-to-azure-storage"></a>Toegang verlenen tot Azure Storage
U kunt de Service Fabric beheerde identiteit van de toepassing (in dit geval toegewezen door de gebruiker) gebruiken om de gegevens op te halen uit een Azure Storage-blob. Wijs de vereiste machtigingen toe aan de Azure Portal met de volgende stappen:

1. Navigeer naar het opslag account
2. Klik op de koppeling Toegangsbeheer (IAM) in het linkerpaneel.
3. Beschrijving Controleer de bestaande toegang: een door het systeem of de gebruiker toegewezen beheerde identiteit selecteren in het besturings element ' zoeken '; Selecteer de juiste identiteit in de lijst met resultaten
4. Klik boven aan de pagina op functie toewijzing toevoegen om een nieuwe roltoewijzing toe te voegen voor de identiteit van de toepassing.
In de vervolgkeuzelijst onder Rol selecteert u Gegevenslezer voor Storage Blob.
5. Kies in de volgende vervolg keuzelijst toegang toewijzen aan `User assigned managed identity` .
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst Abonnement, en stel Resourcegroep in op Alle resourcegroepen.
7. Kies onder selecteren de UAI die overeenkomt met de Service Fabric toepassing en klik vervolgens op opslaan.

Ondersteuning voor door het systeem toegewezen Service Fabric beheerde identiteiten omvatten geen integratie in de Azure Portal; Als uw toepassing gebruikmaakt van een door het systeem toegewezen identiteit, moet u eerst de client-ID van de identiteit van de toepassing zoeken en vervolgens de bovenstaande stappen herhalen, maar de `Azure AD user, group, or service principal` optie selecteren in het besturings element zoeken.

## <a name="granting-access-to-azure-key-vault"></a>Toegang verlenen tot Azure Key Vault
Net als bij het openen van opslag kunt u gebruikmaken van de beheerde identiteit van een Service Fabric toepassing om toegang te krijgen tot een Azure-sleutel kluis. De stappen voor het verlenen van toegang in de Azure Portal zijn vergelijkbaar met die hierboven vermeld, en worden hier niet herhaald. Raadpleeg de onderstaande afbeelding voor verschillen.

![Toegangs beleid Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Het volgende voor beeld illustreert het verlenen van toegang tot een kluis via een sjabloon implementatie. Voeg de onderstaande fragmenten toe als een andere vermelding onder het `resources` element van de sjabloon. In het voor beeld wordt gedemonstreerd hoe toegang wordt verleend voor door de gebruiker toegewezen en door het systeem toegewezen identiteits typen, respectievelijk het betreffende type kiezen.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
En voor door het systeem toegewezen beheerde identiteiten:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Zie [kluizen-Access Policy bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)

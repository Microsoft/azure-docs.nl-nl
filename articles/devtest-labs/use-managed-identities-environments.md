---
title: Azure Managed Identities gebruiken om omgevingen te maken in DevTest Labs | Microsoft Docs
description: Meer informatie over het gebruik van beheerde identiteiten in azure voor het implementeren van omgevingen in een lab in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f3e4b4d7030eb26c25b291e03caaa430d1979c4
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185781"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Door Azure beheerde identiteiten gebruiken voor het implementeren van omgevingen in een Lab 

Als eigenaar van een lab kunt u een beheerde identiteit gebruiken voor het implementeren van omgevingen in een lab. Deze functie is handig in scenario's waarin de omgeving bevat of verwijzingen naar Azure-resources, zoals sleutel kluizen, gemeen schappelijke afbeeldings galerieën en netwerken die zich buiten de resource groep van de omgeving bevinden. Het maakt het mogelijk sandbox-omgevingen te maken die niet beperkt zijn tot de resource groep van die omgeving.

> [!NOTE]
> Op dit moment wordt één door de gebruiker toegewezen identiteit ondersteund per Lab. 

## <a name="prerequisites"></a>Vereisten

- [Een rol maken, weer geven, verwijderen of toewijzen aan een door de gebruiker toegewezen beheerde identiteit met behulp van de Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Zorg ervoor dat uw beheerde identiteit is gemaakt in dezelfde regio en hetzelfde abonnement als uw Lab. De beheerde identiteit hoeft zich niet in dezelfde resource groep te bekomen.

## <a name="use-azure-portal"></a>Azure Portal gebruiken

In deze sectie kunt u als eigenaar van het lab de Azure Portal gebruiken om een door de gebruiker beheerde identiteit toe te voegen aan het lab. 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **DevTest Labs**.
1. Selecteer in de lijst met Labs het gewenste Lab.
1. Selecteer de identiteit van de **configuratie en het beleid**  ->  **(preview-versie)**. 
1. Als u een door de gebruiker toegewezen identiteit wilt toevoegen, selecteert u het tabblad door de **gebruiker toegewezen** .
1. Druk op **toevoegen** .
1. Selecteer een bestaande gebruiker die u hebt gemaakt en/of toegang hebt tot in de vervolg keuzelijst.
 
    ![Door de gebruiker beheerde identiteit toevoegen](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. Druk boven op de pagina op **Opslaan** .

    Wanneer het lab is opgeslagen, gebruikt deze identiteit tijdens het implementeren van alle test omgevingen. U kunt ook toegang krijgen tot de identiteits bron in azure door de identiteit te selecteren in de lijst. 

De eigenaar van het lab hoeft niets te doen bij het implementeren van een omgeving zolang de identiteit die aan het lab is toegevoegd, machtigingen heeft voor de externe bronnen waartoe de omgeving toegang moet hebben. 

Als u de door de gebruiker beheerde identiteit die is toegewezen aan het lab, wilt wijzigen, moet u eerst de identiteit die aan het lab is gekoppeld, verwijderen en vervolgens een andere id toevoegen aan het lab. Als u een aan het lab gekoppelde identiteit wilt verwijderen, selecteert u **... (weglatings tekens)** en klik op **verwijderen**. 

## <a name="use-api"></a>API gebruiken

1. Nadat u een identiteit hebt gemaakt, noteert u de resource-ID van deze identiteit. Deze moet eruitzien als in het volgende voor beeld: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Voer een PUT https-methode uit om een nieuwe resource toe te voegen `ServiceRunner` aan het lab, vergelijkbaar met het volgende voor beeld. Resource voor het uitloper van Services is een proxy bron voor het beheer en beheer van beheerde identiteiten in DevTest Labs. De naam van het service loper kan een geldige naam zijn, maar we raden u aan de naam van de beheerde identiteits bron te gebruiken. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Hier volgt een voorbeeld: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Zodra de door de gebruiker toegewezen identiteit aan het lab is toegevoegd, gebruikt de Azure DevTest Labs-service deze tijdens de implementatie van Azure Resource Manager omgevingen. Als u bijvoorbeeld uw Resource Manager-sjabloon nodig hebt om toegang te krijgen tot een installatie kopie van een externe gedeelde installatie kopie, moet u ervoor zorgen dat de identiteit die u aan de Lab hebt toegevoegd, mini maal vereiste machtigingen heeft voor de resource van de gedeelde installatie kopie galerie. 

---
title: Azure Stream Analytics verifiëren voor Azure Data Lake Storage Gen1
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om uw Azure Stream Analytics-taak te verifiëren voor Azure Data Lake Storage Gen1 uitvoer.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18
ms.openlocfilehash: d5b406d260b1c6518d9227c51d84ab7eb3754329
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598147"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Stream Analytics verifiëren voor het Azure Data Lake Storage Gen1 met behulp van beheerde identiteiten

Azure Stream Analytics ondersteunt beheerde identiteits verificatie met de gen1-uitvoer van Azure Data Lake Storage (ADLS). De identiteit is een beheerde toepassing die is geregistreerd in Azure Active Directory die een bepaalde Stream Analytics taak vertegenwoordigt, en kan worden gebruikt om te verifiëren bij een doel resource. Beheerde identiteiten elimineren de beperkingen van verificatie methoden op basis van gebruikers, zoals het opnieuw verifiëren als gevolg van wachtwoord wijzigingen of gebruikers token verloop die elke 90 dagen worden uitgevoerd. Daarnaast helpen beheerde identiteiten bij de automatisering van Stream Analytics taak implementaties die naar Azure Data Lake Storage Gen1 worden uitgevoerd.

In dit artikel ziet u drie manieren om beheerde identiteit in te scha kelen voor een Azure Stream Analytics-taak die naar een Azure Data Lake Storage Gen1 wordt uitgevoerd via de Azure Portal, Azure Resource Manager sjabloon implementatie en Azure Stream Analytics hulpprogram ma's voor Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Begin met het maken van een nieuwe Stream Analytics-taak of het openen van een bestaande taak in Azure Portal. Selecteer in de menu balk aan de linkerkant van het scherm **beheerde identiteit** onder **configureren**.

   ![Stream Analytics beheerde identiteit configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selecteer door het **systeem toegewezen beheerde identiteit gebruiken** in het venster dat aan de rechter kant wordt weer gegeven. Klik op **Opslaan** in een service-principal voor de identiteit van de stream Analytics taak in azure Active Directory. De levens cyclus van de zojuist gemaakte identiteit wordt beheerd door Azure. Wanneer de Stream Analytics taak wordt verwijderd, wordt de bijbehorende identiteit (de service-principal) automatisch verwijderd door Azure.

   Wanneer de configuratie is opgeslagen, wordt de object-ID (OID) van de Service-Principal vermeld als de principal-ID zoals hieronder wordt weer gegeven:

   ![Service-Principal-ID Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   De service-principal heeft dezelfde naam als de Stream Analytics taak. Als de naam van uw taak bijvoorbeeld **MyASAJob** is, is de naam van de service-principal die is gemaakt ook **MyASAJob**.

3. Klik in het venster uitvoer eigenschappen van de ADLS Gen1 uitvoer Sink op de vervolg keuzelijst verificatie modus en selecteer **beheerde identiteit**.

4. Vul de rest van de eigenschappen in. Zie [een Data Lake Store-uitvoer maken met Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)voor meer informatie over het maken van een ADLS-uitvoer. Klik op **Opslaan** als u klaar bent.

   ![Azure Data Lake Storage configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Ga naar de pagina overzicht van de ADLS Gen1 en klik op **Data Explorer**.

   ![Data Lake Storage overzicht configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. Selecteer in het deel venster Data Explorer de optie **toegang** en klik op **toevoegen** in het deel venster toegang.

   ![Data Lake Storage toegang configureren](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Typ de naam van de Service-Principal in het tekstvak in het deel venster **gebruiker of groep selecteren** . Houd er rekening mee dat de naam van de Service-Principal ook de naam van de bijbehorende Stream Analytics taak is. Wanneer u begint met het typen van de principal-naam, wordt deze onder het tekstvak weer gegeven. Kies de gewenste Service Principal Name en klik op **selecteren**.

   ![Een Service Principal Name selecteren](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. Controleer in het deel venster **machtigingen** de machtigingen **schrijven** en **uitvoeren** en wijs deze toe aan **deze map en alle onderliggende items**. Klik vervolgens op **OK**.

   ![Schrijf-en uitvoer machtigingen selecteren](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. De service-principal wordt vermeld onder **toegewezen machtigingen** in het deel venster **toegang** , zoals hieronder wordt weer gegeven. U kunt nu teruggaan en uw Stream Analytics-taak starten.

   ![Toegangs lijst Stream Analytics in Portal](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Zie [Access Control in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie over de machtigingen voor het bestands systeem van data Lake Storage gen1.

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics-hulpprogram ma's voor Visual Studio

1. Stel in JobConfig.jsin op ' door **systeem toegewezen identiteit** in op **' True '**.

   ![Beheerde identiteiten voor taak configuratie Stream Analytics](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Klik in het venster uitvoer eigenschappen van de ADLS Gen1 uitvoer Sink op de vervolg keuzelijst verificatie modus en selecteer **beheerde identiteit**.

   ![Beheerde identiteiten voor ADLS-uitvoer](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Vul de overige eigenschappen in en klik op **Opslaan**.

4. Klik in de query-editor op **verzenden naar Azure** .

   Wanneer u de taak verzendt, voert de hulpprogram ma's twee dingen uit:

   * Maakt automatisch een service-principal voor de identiteit van de Stream Analytics taak in Azure Active Directory. De levens cyclus van de zojuist gemaakte identiteit wordt beheerd door Azure. Wanneer de Stream Analytics taak wordt verwijderd, wordt de bijbehorende identiteit (de service-principal) automatisch verwijderd door Azure.

   * Stel de machtigingen **schrijven** en **uitvoeren** automatisch in voor het pad ADLS gen1 voorvoegsel dat in de taak wordt gebruikt en wijs dit toe aan deze map en alle onderliggende items.

5. U kunt de Resource Manager-sjablonen met de volgende eigenschap genereren met behulp van [Stream Analytics CI.cd NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) versie 1.5.0 of hoger op een build-machine (buiten Visual Studio). Volg de implementatie stappen voor de Resource Manager-sjabloon in de volgende sectie om de Service-Principal op te halen en toegang te verlenen tot de Service-Principal via Power shell.

## <a name="resource-manager-template-deployment"></a>Implementatie van Resource Manager-sjabloon

1. U kunt een *micro soft. StreamAnalytics/streamingjobs-* resource maken met een beheerde identiteit door de volgende eigenschap op te nemen in de resource sectie van uw Resource Manager-sjabloon:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Met deze eigenschap geeft u Azure Resource Manager om de identiteit voor uw Azure Stream Analytics taak te maken en te beheren.

   **Voorbeeld taak**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Voorbeeld taak respons**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Noteer de principal-ID van de taak respons om toegang te verlenen tot de vereiste ADLS-resource.

   De **Tenant-id** is de id van de Azure Active Directory Tenant waar de Service-Principal is gemaakt. De service-principal wordt gemaakt in de Azure-Tenant die wordt vertrouwd door het abonnement.

   Het **type** geeft het type beheerde identiteit aan zoals wordt uitgelegd in typen beheerde identiteiten. Alleen het door het systeem toegewezen type wordt ondersteund.

2. Geef toegang tot de service-principal met behulp van Power shell. Voer de volgende opdracht uit om toegang te geven tot de Service-Principal via Power shell:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   De **PrincipalId** is de object-id van de Service-Principal en wordt weer gegeven op het portal scherm wanneer de Service-Principal is gemaakt. Als u de taak hebt gemaakt met behulp van een resource manager-sjabloon implementatie, wordt de object-ID weer gegeven in de eigenschap identiteit van het antwoord op de taak.

   **Voorbeeld**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Raadpleeg de documentatie van [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) voor meer informatie over de bovenstaande Power shell-opdracht.

## <a name="remove-managed-identity"></a>Beheerde identiteit verwijderen

De beheerde identiteit die is gemaakt voor een Stream Analytics taak wordt alleen verwijderd wanneer de taak wordt verwijderd. Het is niet mogelijk om de beheerde identiteit te verwijderen zonder de taak te verwijderen. Als u de beheerde identiteit niet meer wilt gebruiken, kunt u de verificatie methode voor de uitvoer wijzigen. De beheerde identiteit blijft bestaan totdat de taak is verwijderd en wordt gebruikt als u ervoor kiest beheerde identiteits verificatie opnieuw te gebruiken.

## <a name="limitations"></a>Beperkingen
Deze functie biedt geen ondersteuning voor het volgende:

1. **Multi tenant-toegang**: de service-principal die is gemaakt voor een bepaalde stream Analytics taak bevindt zich op de Azure Active Directory Tenant waarop de taak is gemaakt en kan niet worden gebruikt voor een resource die zich op een andere Azure Active Directory Tenant bevindt. Daarom kunt u alleen MSI gebruiken op ADLS gen 1-resources die zich in dezelfde Azure Active Directory Tenant bevinden als uw Azure Stream Analytics-taak. 

2. Door **[gebruiker toegewezen identiteit](../active-directory/managed-identities-azure-resources/overview.md)**: wordt niet ondersteund. Dit betekent dat de gebruiker niet in staat is om hun eigen Service-Principal in te voeren om te worden gebruikt door hun Stream Analytics-taak. De service-principal wordt gegenereerd door Azure Stream Analytics.

## <a name="next-steps"></a>Volgende stappen

* [Een Data Lake Store-uitvoer maken met Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Stream Analytics query's lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Live-gegevens lokaal testen met Azure Stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-live-data-local-testing.md) 

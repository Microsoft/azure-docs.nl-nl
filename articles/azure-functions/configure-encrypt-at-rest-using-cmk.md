---
title: De bron-at-rest van uw toepassing versleutelen
description: Versleutel uw toepassingsgegevens in Azure Storage en implementeer deze als een pakketbestand.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 71668bf27628bb2af2dfc7112d28ba10ecfdf9f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768809"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>Uw toepassingsgegevens in rust versleutelen met behulp van door de klant beheerde sleutels

Voor het versleutelen van de data-at-rest van uw functie-app zijn een Azure Storage account en een Azure Key Vault. Deze services worden gebruikt wanneer u uw app vanuit een implementatiepakket hebt uitgevoerd.

  - [Azure Storage biedt versleuteling in rust.](../storage/common/storage-service-encryption.md) U kunt door het systeem geleverde sleutels of uw eigen, door de klant beheerde sleutels gebruiken. Hier worden uw toepassingsgegevens opgeslagen wanneer deze niet worden uitgevoerd in een functie-app in Azure.
  - [Uitvoeren vanuit een implementatiepakket](run-functions-from-deployment-package.md) is een implementatiefunctie van App Service. Hiermee kunt u uw site-inhoud implementeren vanuit een Azure Storage-account met behulp van Shared Access Signature URL (SAS).
  - [Key Vault zijn een](../app-service/app-service-key-vault-references.md) beveiligingsfunctie van App Service. Hiermee kunt u geheimen tijdens runtime importeren als toepassingsinstellingen. Gebruik deze om de SAS-URL van uw Azure Storage versleutelen.

## <a name="set-up-encryption-at-rest"></a>Versleuteling in rust instellen

### <a name="create-an-azure-storage-account"></a>Een Azure Storage-account maken

Maak eerst [een Azure Storage account en](../storage/common/storage-account-create.md) [versleutel dit met door de klant beheerde sleutels.](../storage/common/customer-managed-keys-overview.md) Zodra het opslagaccount is gemaakt, gebruikt u de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) om pakketbestanden te uploaden.

Gebruik vervolgens de Storage Explorer om een [SAS te genereren.](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer) 

> [!NOTE]
> Sla deze SAS-URL op. Deze wordt later gebruikt om beveiligde toegang tot het implementatiepakket tijdens runtime in te stellen.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Uitvoeren vanuit een pakket vanuit uw opslagaccount configureren
  
Nadat u het bestand hebt geüpload naar Blob Storage en een SAS-URL voor het bestand hebt, stelt u de `WEBSITE_RUN_FROM_PACKAGE` toepassingsinstelling in op de SAS-URL. In het volgende voorbeeld wordt dit uitgevoerd met behulp van Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Als u deze toepassingsinstelling toevoegt, wordt uw functie-app opnieuw gestart. Nadat de app opnieuw is opgestart, bladert u er naar en zorgt u ervoor dat de app correct is gestart met behulp van het implementatiepakket. Zie de probleemoplossingsgids Uitvoeren vanuit pakket als de toepassing niet [goed is begonnen.](run-functions-from-deployment-package.md#troubleshooting)

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>De toepassingsinstelling versleutelen met Key Vault referenties

U kunt nu de waarde van de toepassingsinstelling vervangen door een `WEBSITE_RUN_FROM_PACKAGE` Key Vault naar de MET SAS gecodeerde URL. Hierdoor blijft de SAS-URL versleuteld in Key Vault, wat een extra beveiligingslaag biedt.

1. Gebruik de volgende [`az keyvault create`](/cli/azure/keyvault#az_keyvault_create) opdracht om een Key Vault maken.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Volg [deze instructies om uw app toegang te verlenen tot](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) uw sleutelkluis:

1. Gebruik de volgende [`az keyvault secret set`](/cli/azure/keyvault/secret#az_keyvault_secret_set) opdracht om uw externe URL toe te voegen als een geheim in uw sleutelkluis:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Gebruik de volgende [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) opdracht om de toepassingsinstelling te maken met de waarde als Key Vault `WEBSITE_RUN_FROM_PACKAGE` verwijzing naar de externe URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    De `<secret-version>` staat in de uitvoer van de vorige `az keyvault secret set` opdracht.

Het bijwerken van deze toepassingsinstelling zorgt ervoor dat uw functie-app opnieuw wordt gestart. Nadat de app opnieuw is opgestart, bladert u naar de app en zorgt u ervoor dat deze correct is gestart met behulp van Key Vault referentie.

## <a name="how-to-rotate-the-access-token"></a>Het toegangs token roteren

Het is best practice om de SAS-sleutel van uw opslagaccount periodiek te roteren. Om ervoor te zorgen dat de functie-app niet per ongeluk toegang verliest, moet u ook de SAS-URL bijwerken in Key Vault.

1. Roteren van de SAS-sleutel door te navigeren naar uw opslagaccount in Azure Portal. Klik **onder Instellingen**  >  **Toegangssleutels** op het pictogram om de SAS-sleutel te draaien.

1. Kopieer de nieuwe SAS-URL en gebruik de volgende opdracht om de bijgewerkte SAS-URL in te stellen in uw sleutelkluis:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Werk de sleutelkluisverwijzing in uw toepassingsinstelling bij naar de nieuwe geheime versie:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    De `<secret-version>` staat in de uitvoer van de vorige `az keyvault secret set` opdracht.

## <a name="how-to-revoke-the-function-apps-data-access"></a>De toegang tot gegevens van de functie-app intrekken

Er zijn twee methoden om de toegang van de functie-app tot het opslagaccount in te trekken. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>De SAS-sleutel voor het Azure Storage draaien

Als de SAS-sleutel voor het opslagaccount wordt geroteerd, heeft de functie-app geen toegang meer tot het opslagaccount, maar blijft deze worden uitgevoerd met de laatst gedownloade versie van het pakketbestand. Start de functie-app opnieuw om de laatst gedownloade versie te verwijderen.

### <a name="remove-the-function-apps-access-to-key-vault"></a>De toegang van de functie-app tot Key Vault

U kunt de toegang van de functie-app tot de sitegegevens intrekken door de toegang van de functie-app tot de Key Vault. Hiervoor verwijdert u het toegangsbeleid voor de identiteit van de functie-app. Dit is dezelfde identiteit die u eerder hebt gemaakt tijdens het configureren van key vault-verwijzingen.

## <a name="summary"></a>Samenvatting

Uw toepassingsbestanden zijn nu 'at rest' versleuteld in uw opslagaccount. Wanneer uw functie-app wordt gestart, wordt de SAS-URL opgehaald uit uw sleutelkluis. Ten slotte laadt de functie-app de toepassingsbestanden uit het opslagaccount. 

Als u de toegang van de functie-app tot uw opslagaccount moet intrekken, kunt u de toegang tot de sleutelkluis intrekken of de sleutels van het opslagaccount roteren, waardoor de SAS-URL ongeldig wordt.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Zijn er extra kosten voor het uitvoeren van mijn functie-app vanuit het implementatiepakket?

Alleen de kosten die zijn gekoppeld aan Azure Storage-account en eventuele toepasselijke kosten voor het verwijderen van het account.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Wat is de invloed van het uitvoeren vanuit het implementatiepakket op mijn functie-app?

- Het uitvoeren van uw app vanuit het implementatiepakket zorgt `wwwroot/` voor alleen-lezen. Uw app ontvangt een foutmelding wanneer deze probeert naar deze map te schrijven.
- TAR- en GZIP-indelingen worden niet ondersteund.
- Deze functie is niet compatibel met lokale cache.

## <a name="next-steps"></a>Volgende stappen

- [Key Vault voor App Service](../app-service/app-service-key-vault-references.md)
- [Azure Storage-versleuteling voor inactieve gegevens](../storage/common/storage-service-encryption.md)
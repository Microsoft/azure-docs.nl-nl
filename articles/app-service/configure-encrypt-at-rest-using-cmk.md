---
title: Uw toepassings bron op rest versleutelen
description: Meer informatie over het versleutelen van uw toepassings gegevens in Azure Storage en het implementeren ervan als een pakket bestand.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 5524b749b1e15342dd0133920d7190e33ced18ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92146042"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>Versleuteling in rust met door de klant beheerde sleutels

Voor het versleutelen van de toepassings gegevens van uw web-app in rust zijn een Azure Storage-account en een Azure Key Vault vereist. Deze services worden gebruikt wanneer u uw app uitvoert vanuit een implementatie pakket.

  - [Azure Storage voorziet in rest versleuteling](../storage/common/storage-service-encryption.md). U kunt door het systeem gegeven sleutels of uw eigen door de klant beheerde sleutels gebruiken. Hier worden uw toepassings gegevens opgeslagen wanneer deze niet worden uitgevoerd in een web-app in Azure.
  - [Uitvoeren vanuit een implementatie pakket](deploy-run-package.md) is een implementatie functie van app service. Hiermee kunt u uw site-inhoud vanuit een Azure Storage-account implementeren met behulp van een URL voor Shared Access Signature (SAS).
  - [Key Vault verwijzingen](app-service-key-vault-references.md) zijn een beveiligings functie van app service. Hiermee kunt u geheimen in runtime importeren als toepassings instellingen. Gebruik deze om de SAS-URL van uw Azure Storage-account te versleutelen.

## <a name="set-up-encryption-at-rest"></a>Versleuteling op rest instellen

### <a name="create-an-azure-storage-account"></a>Een Azure Storage-account maken

Maak eerst [een Azure Storage-account](../storage/common/storage-account-create.md) en [Versleutel het met door de klant beheerde sleutels](../storage/common/customer-managed-keys-overview.md). Zodra het opslag account is gemaakt, gebruikt u de [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) om pakket bestanden te uploaden.

Gebruik vervolgens de Storage Explorer om [een SAS te genereren](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Sla deze SAS-URL op. dit wordt later gebruikt om beveiligde toegang tot het implementatie pakket tijdens runtime in te scha kelen.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Uitvoeren vanuit een pakket vanuit uw opslag account configureren
  
Wanneer u uw bestand uploadt naar Blob-opslag en een SAS-URL voor het bestand hebt, stelt u de `WEBSITE_RUN_FROM_PACKAGE` toepassings instelling in op de SAS-URL. In het volgende voor beeld wordt dit gedaan met behulp van Azure CLI:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

Door deze toepassings instelling toe te voegen, wordt uw web-app opnieuw opgestart. Nadat de app opnieuw is opgestart, bladert u naar deze en zorgt u ervoor dat de app correct is gestart met het implementatie pakket. Als de toepassing niet correct is gestart, raadpleegt u de [hand leiding uitvoeren vanuit pakket oplossen](deploy-run-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>De toepassings instelling versleutelen met behulp van Key Vault verwijzingen

Nu kunt u de waarde van de `WEBSITE_RUN_FROM_PACKAGE` toepassings instelling vervangen door een Key Vault verwijzing naar de URL met SAS-code ring. Hierdoor blijft de SAS-URL versleuteld in Key Vault, wat een extra beveiligingslaag vormt.

1. Gebruik de volgende [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) opdracht om een Key Vault-exemplaar te maken.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Volg [deze instructies om uw app toegang te geven](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) tot uw sleutel kluis:

1. Gebruik de volgende [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) opdracht om uw externe URL toe te voegen als een geheim in uw sleutel kluis:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Gebruik de volgende [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) opdracht om de `WEBSITE_RUN_FROM_PACKAGE` toepassings instelling te maken met de waarde als een Key Vault verwijzing naar de externe URL:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    De `<secret-version>` wordt uitgevoerd in de uitvoer van de vorige `az keyvault secret set` opdracht.

Wanneer u deze toepassings instelling bijwerkt, wordt uw web-app opnieuw opgestart. Nadat de app opnieuw is opgestart, bladert u naar de juiste start met behulp van de Key Vault verwijzing.

## <a name="how-to-rotate-the-access-token"></a>Het toegangs token draaien

Het is best practice om regel matig de SAS-sleutel van uw opslag account te draaien. Als u er zeker van wilt zijn dat de web-app onopzettelijk toegang heeft, moet u ook de SAS-URL in Key Vault bijwerken.

1. Draai de SAS-sleutel door te navigeren naar uw opslag account in de Azure Portal. Klik onder **instellingen**  >  **toegangs sleutels** op het pictogram om de SAS-sleutel te roteren.

1. Kopieer de nieuwe SAS-URL en gebruik de volgende opdracht om de bijgewerkte SAS-URL in uw sleutel kluis in te stellen:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Werk de sleutel kluis referentie in uw toepassings instelling bij naar de nieuwe geheime versie:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    De `<secret-version>` wordt uitgevoerd in de uitvoer van de vorige `az keyvault secret set` opdracht.

## <a name="how-to-revoke-the-web-apps-data-access"></a>De toegang tot gegevens van de web-app intrekken

Er zijn twee methoden om de toegang van de web-app tot het opslag account in te trekken. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>De SAS-sleutel voor het Azure Storage-account draaien

Als de SAS-sleutel voor het opslag account is gedraaid, heeft de web-app geen toegang meer tot het opslag account, maar wordt deze nog steeds uitgevoerd met de laatst gedownloade versie van het pakket bestand. Start de web-app opnieuw om de laatst gedownloade versie te wissen.

### <a name="remove-the-web-apps-access-to-key-vault"></a>De toegang tot Key Vault van de web-app verwijderen

U kunt de toegang tot de site gegevens van de web-app intrekken door de toegang tot Key Vault van de web-app uit te scha kelen. Als u dit wilt doen, verwijdert u het toegangs beleid voor de identiteit van de web-app. Dit is dezelfde identiteit die u eerder hebt gemaakt tijdens het configureren van sleutel kluis verwijzingen.

## <a name="summary"></a>Samenvatting

Uw toepassings bestanden zijn nu versleuteld op rest in uw opslag account. Als uw web-app wordt gestart, wordt de SAS-URL opgehaald uit uw sleutel kluis. Ten slotte laadt de Web-App de toepassings bestanden van het opslag account. 

Als u de toegang tot uw opslag account wilt intrekken van de web-app, kunt u de toegang tot de sleutel kluis intrekken of de sleutels van het opslag account draaien, waardoor de SAS-URL ongeldig wordt.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>Worden er extra kosten in rekening gebracht voor het uitvoeren van mijn web-app vanuit het implementatie pakket?

Alleen de kosten die zijn gekoppeld aan het Azure Storage-account en eventuele van toepassing zijnde uitgangs kosten.

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>Wat is van invloed op de uitvoering van het implementatie pakket op mijn web-app?

- Als uw app vanuit het implementatie pakket wordt uitgevoerd `wwwroot/` , wordt alleen-lezen gemaakt. Uw app ontvangt een fout melding wanneer wordt geprobeerd naar deze map te schrijven.
- TAR-en GZIP-indelingen worden niet ondersteund.
- Deze functie is niet compatibel met de lokale cache.

## <a name="next-steps"></a>Volgende stappen

- [Key Vault verwijzingen voor App Service](app-service-key-vault-references.md)
- [Azure Storage-versleuteling voor inactieve gegevens](../storage/common/storage-service-encryption.md)
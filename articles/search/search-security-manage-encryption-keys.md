---
title: Versleuteling-at-rest met door de klant beheerde sleutels
titleSuffix: Azure Cognitive Search
description: Aanvullende versleuteling aan de serverzijde voor indexen en synoniemkaarten in Azure Cognitive Search met behulp van sleutels die u in een Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 9679157e7871b043711fff688a8cbb69cf9bb4d8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813610"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Door de klant beheerde sleutels configureren voor gegevensversleuteling in Azure Cognitive Search

Azure Cognitive Search versleuteld geïndexeerde inhoud at rest automatisch met [door de service beheerde sleutels](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Als u meer beveiliging nodig hebt, kunt u standaardversleuteling aanvullen met een extra versleutelingslaag met behulp van sleutels die u in de Azure Key Vault. In dit artikel worden de stappen beschreven voor het instellen van door de klant beheerde sleutelversleuteling.

Door de klant beheerde sleutelversleuteling is afhankelijk [van Azure Key Vault](../key-vault/general/overview.md). U kunt uw eigen versleutelingssleutels maken en deze opslaan in een sleutelkluis, of u kunt de API's van Azure Key Vault gebruiken om versleutelingssleutels te genereren. Met Azure Key Vault kunt u ook het sleutelgebruik controleren als u [logboekregistratie inschakelen.](../key-vault/general/logging.md)  

Versleuteling met door de klant beheerde sleutels wordt toegepast op afzonderlijke indexen of synoniemen wanneer deze objecten worden gemaakt en wordt niet opgegeven op het zoekserviceniveau zelf. Alleen nieuwe objecten kunnen worden versleuteld. U kunt inhoud die al bestaat niet versleutelen.

Sleutels hoeven niet allemaal in dezelfde sleutelkluis te staan. Eén zoekservice kan meerdere versleutelde indexen of synoniemenkaarten hosten, die elk zijn versleuteld met hun eigen door de klant beheerde versleutelingssleutels die zijn opgeslagen in verschillende sleutelkluizen. U kunt ook indexen en synoniemenkaarten in dezelfde service hebben die niet zijn versleuteld met door de klant beheerde sleutels.

>[!Important]
> Als u door de klant beheerde sleutels implementeert, moet u strikte procedures volgen tijdens het routinematig rouleren van sleutelkluissleutels en Active Directory-toepassingsgeheimen en -registratie. Werk altijd alle versleutelde inhoud bij om nieuwe geheimen en sleutels te gebruiken voordat u de oude inhoud kunt verwijderen. Als u deze stap mist, kan uw inhoud niet worden ontsleuteld.

## <a name="double-encryption"></a>Dubbele versleuteling

Voor services die zijn gemaakt na 1 augustus 2020 en in specifieke regio's, [](search-security-overview.md#double-encryption)omvat het bereik van door de klant beheerde sleutelversleuteling tijdelijke schijven, die volledige dubbele versleuteling bereiken, die momenteel beschikbaar zijn in deze regio's: 

+ VS - west 2
+ VS - oost
+ VS - zuid-centraal
+ VS (overheid) - Virginia
+ VS (overheid) - Arizona

Als u een andere regio gebruikt of een service die is gemaakt vóór 1 augustus, is versleuteling van beheerde sleutels beperkt tot alleen de gegevensschijf, met uitzondering van de tijdelijke schijven die door de service worden gebruikt.

## <a name="prerequisites"></a>Vereisten

In dit scenario worden de volgende hulpprogramma's en services gebruikt.

+ [Azure Cognitive Search](search-create-service-portal.md) in een [factureerbare laag](search-sku-tier.md#tier-descriptions) (Basic of hoger, in elke regio).
+ [Azure Key Vault](../key-vault/general/overview.md)kunt u een sleutelkluis maken [met behulp van Azure Portal,](../key-vault//general/quick-create-portal.md) [Azure CLI](../key-vault//general/quick-create-cli.md) [of Azure PowerShell](../key-vault//general/quick-create-powershell.md). in hetzelfde abonnement als Azure Cognitive Search. Voor de sleutelkluis **moet de** beveiliging voor verwijderen **en opsluizen** zijn ingeschakeld.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Als u nog geen tenant hebt, stelt [u een nieuwe tenant in.](../active-directory/develop/quickstart-create-new-tenant.md)

U moet een zoektoepassing hebben die het versleutelde object kan maken. In deze code verwijst u naar een sleutelkluissleutel en Active Directory-registratiegegevens. Deze code kan een werkende app zijn of prototypecode zoals het [C#-codevoorbeeld DotNetHowToEncryptionUsingCMK.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)

> [!TIP]
> U kunt [Postman,](search-get-started-rest.md) [Visual Studio Code](search-get-started-vs-code.md)of [Azure PowerShell](./search-get-started-powershell.md)gebruiken om REST API's aan te roepen die indexen en synoniemenkaarten maken die een versleutelingssleutelparameter bevatten. Er is op dit moment geen ondersteuning voor de portal voor het toevoegen van een sleutel aan indexen of synoniemkaarten.

## <a name="1---enable-key-recovery"></a>1 - Sleutelherstel inschakelen

Vanwege de aard van versleuteling met door de klant beheerde sleutels kan niemand uw gegevens ophalen als uw Azure Key Vault-sleutel wordt verwijderd. Als u wilt voorkomen dat gegevens verloren gaan door Key Vault verwijderen van sleutels, moet de beveiliging voor het verwijderen en opsluizen van de sleutelkluis zijn ingeschakeld. Soft-delete is standaard ingeschakeld, zodat u alleen problemen ondervindt als u deze met opzet hebt uitgeschakeld. Beveiliging tegen opsisten is niet standaard ingeschakeld, maar is vereist voor door de klant beheerde sleutelversleuteling in Cognitive Search. Zie Overzichten van [de beveiliging tegen soft-delete](../key-vault/general/soft-delete-overview.md) en [opsting](../key-vault/general/soft-delete-overview.md#purge-protection) voor meer informatie.

U kunt beide eigenschappen instellen met behulp van de portal, PowerShell of Azure CLI-opdrachten.

### <a name="using-azure-portal"></a>Azure Portal gebruiken

1. [Meld u aan bij Azure Portal](https://portal.azure.com) open de overzichtspagina van uw sleutelkluis.

1. Schakel op **de pagina** Overzicht onder **Essentials** **De beveiliging voor soft-delete en** **Purge in.**

### <a name="using-powershell"></a>PowerShell gebruiken

1. Voer `Connect-AzAccount` uit om uw Azure-referenties in te stellen.

1. Voer de volgende opdracht uit om verbinding te maken met uw sleutelkluis en vervang `<vault_name>` door een geldige naam:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault wordt gemaakt met 'soft-delete' ingeschakeld. Als deze is uitgeschakeld voor uw kluis, moet u de volgende opdracht uitvoeren:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Beveiliging tegen ops manier inschakelen:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Sla uw updates op:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Azure CLI gebruiken

+ Als u een installatie [van Azure CLI hebt,](/cli/azure/install-azure-cli)kunt u de volgende opdracht uitvoeren om de vereiste eigenschappen in teschakelen.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 - Een sleutel maken in Key Vault

Sla deze stap over als u al een sleutel in de Azure Key Vault.

1. [Meld u aan bij Azure Portal](https://portal.azure.com) open de overzichtspagina van uw sleutelkluis.

1. Selecteer **Sleutels** aan de linkerkant en selecteer **vervolgens + Genereren/importeren.**

1. Kies in **het deelvenster** Een sleutel maken in de lijst **met opties** de methode die u wilt gebruiken om een sleutel te maken. U kunt **een nieuwe** sleutel genereren, **een bestaande** sleutel uploaden of **Back-up herstellen gebruiken** om een back-up van een sleutel te selecteren.

1. Voer een **Naam** in voor uw sleutel en selecteer eventueel andere sleuteleigenschappen.

1. Selecteer **Maken om** de implementatie te starten.

1. Noteer de sleutel-id: deze bestaat uit de **sleutelwaarde-URI,** de **sleutelnaam** en de **sleutelversie**. U hebt de id nodig om een versleutelde index te definiëren in Azure Cognitive Search.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Een nieuwe sleutelkluissleutel maken":::

## <a name="3---register-an-app-in-active-directory"></a>3 - Een app registreren in Active Directory

1. Zoek [Azure Portal](https://portal.azure.com)de resource Azure Active Directory uw abonnement.

1. Selecteer aan de **linkerkant onder Beheren** de optie **App-registraties** en selecteer vervolgens **Nieuwe registratie.**

1. Geef de registratie een naam, mogelijk een naam die vergelijkbaar is met de naam van de zoektoepassing. Selecteer **Registreren**.

1. Zodra de app-registratie is gemaakt, kopieert u de toepassings-id. U moet deze tekenreeks aan uw toepassing verstrekken. 

   Als u de [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)doorstapt, plakt u deze waarde in **appsettings.jsbestand.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Toepassings-id in de sectie Essentials":::

1. Selecteer vervolgens **Certificaten & geheimen** aan de linkerkant.

1. Selecteer **Nieuw clientgeheim**. Geef het geheim een weergavenaam en selecteer **Toevoegen.**

1. Kopieer het toepassingsgeheim. Als u het voorbeeld stap voor stap door het voorbeeld gaat, plakt u deze waarde in **appsettings.jsbestand.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Toepassingsgeheim":::

## <a name="4---grant-key-access-permissions"></a>4 - Machtigingen voor sleuteltoegang verlenen

In deze stap maakt u een toegangsbeleid in Key Vault. Dit beleid geeft de toepassing die u hebt geregistreerd met Active Directory toestemming om uw door de klant beheerde sleutel te gebruiken.

Toegangsmachtigingen kunnen op elk moment worden ingetrokken. Zodra de index of synoniemen van de zoekservice die gebruikmaakt van die sleutelkluis zijn ingetrokken, worden ze onbruikbaar. Als u de toegangsmachtigingen voor Key Vault op een later tijdstip herstelt, wordt de toegang tot index\synoniemenkaart hersteld. Zie Beveiligde toegang tot een [sleutelkluis voor meer informatie.](../key-vault/general/security-features.md)

1. Open in de Azure Portal de overzichtspagina van **uw sleutelkluis.** 

1. Selecteer **toegangsbeleid aan de** linkerkant en selecteer **+ Toegangsbeleid toevoegen.**

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Nieuw toegangsbeleid voor key vault toevoegen":::

1. Kies **Principal selecteren** en selecteer de toepassing die u hebt geregistreerd bij Active Directory. U kunt er op naam naar zoeken.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Key Vault-toegangsbeleidsprincipaal selecteren":::

1. Kies **in Sleutelmachtigingen** *de optie Get*, *Unwrap Key* en Wrap *Key.*

1. Selecteer **in Geheime machtigingen** de optie *Get.*

1. Selecteer **in Certificaatmachtigingen** de *optie Get.*

1. Selecteer **Toevoegen** en vervolgens **Opslaan.**

> [!Important]
> Versleutelde inhoud in Azure Cognitive Search is geconfigureerd voor het gebruik van een specifieke Azure Key Vault sleutel met een specifieke **versie**. Als u de sleutel of versie wijzigt, moet de index of synoniemenkaart worden bijgewerkt om de nieuwe sleutel\versie te gebruiken voordat u **de** vorige sleutel\versie kunt verwijderen. Als u dit niet doet, wordt de index of synoniemkaart onbruikbaar. Op kunt u de inhoud niet ontsleutelen zodra de toegang tot de sleutel is verloren.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 - Inhoud versleutelen

Als u een door de klant beheerde sleutel wilt toevoegen aan een index, gegevensbron, vaardighedenset, indexer of synoniemkaart, moet u de [search-REST API](/rest/api/searchservice/) of een SDK gebruiken. In de portal worden synoniemenkaarten of versleutelingseigenschappen niet weergegeven. Wanneer u een geldige API-index gebruikt, ondersteunen gegevensbronnen, vaardighedensets, indexeerders en synoniemenkaarten een **encryptionKey-eigenschap** op het hoogste niveau.

In dit voorbeeld wordt de REST API gebruikt, met waarden voor Azure Key Vault en Azure Active Directory:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Geen van deze sleutelkluisdetails worden beschouwd als geheim en kunnen eenvoudig worden opgehaald door te bladeren naar de relevante Azure Key Vault sleutelpagina in Azure Portal.

## <a name="example-index-encryption"></a>Voorbeeld: Indexversleuteling

Maak een versleutelde index met behulp van [de Azure Cognitive Search REST API](/rest/api/searchservice/create-index). Gebruik de eigenschap `encryptionKey` om op te geven welke versleutelingssleutel moet worden gebruikt.
> [!Note]
> Geen van deze sleutelkluisdetails worden beschouwd als geheim en kunnen eenvoudig worden opgehaald door naar de relevante pagina Azure Key Vault sleutel in de Azure Portal.

## <a name="rest-examples"></a>REST-voorbeelden

In deze sectie ziet u de volledige JSON voor een versleutelde index en synoniemenkaart

### <a name="index-encryption"></a>Indexversleuteling

De details van het maken van een nieuwe index via de REST API vindt u in Index maken [(REST API),](/rest/api/searchservice/create-index)waarbij het enige verschil hier is dat de details van de versleutelingssleutel worden opgegeven als onderdeel van de indexdefinitie:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

U kunt nu de aanvraag voor het maken van de index verzenden en vervolgens de index normaal gaan gebruiken.

### <a name="synonym-map-encryption"></a>Synoniemkaartversleuteling

Maak een versleutelde synoniemenkaart met behulp van [de map](/rest/api/searchservice/create-synonym-map)Synoniem maken Azure Cognitive Search REST API . Gebruik de eigenschap `encryptionKey` om op te geven welke versleutelingssleutel moet worden gebruikt.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

U kunt nu de aanvraag voor het maken van een synoniemkaart verzenden en deze vervolgens normaal gebruiken.

## <a name="example-data-source-encryption"></a>Voorbeeld: Versleuteling van gegevensbron

Maak een versleutelde gegevensbron met behulp van de optie Gegevensbron [maken (Azure Cognitive Search REST API).](/rest/api/searchservice/create-data-source) Gebruik de eigenschap `encryptionKey` om op te geven welke versleutelingssleutel moet worden gebruikt.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

U kunt nu de aanvraag voor het maken van de gegevensbron verzenden en deze vervolgens normaal gaan gebruiken.

## <a name="example-skillset-encryption"></a>Voorbeeld: Versleuteling van vaardighedenset

Maak een versleutelde vaardighedenset met behulp van [de Azure Cognitive Search REST API](/rest/api/searchservice/create-skillset). Gebruik de eigenschap `encryptionKey` om op te geven welke versleutelingssleutel moet worden gebruikt.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

U kunt nu de aanvraag voor het maken van de vaardighedenset verzenden en deze vervolgens normaal gebruiken.

## <a name="example-indexer-encryption"></a>Voorbeeld: Indexerversleuteling

Maak een versleutelde indexer met behulp van [de Azure Cognitive Search REST API](/rest/api/searchservice/create-indexer). Gebruik de eigenschap `encryptionKey` om op te geven welke versleutelingssleutel moet worden gebruikt.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

U kunt nu de aanvraag voor het maken van de indexer verzenden en deze vervolgens normaal gebruiken.

>[!Important]
> Hoewel niet kan worden toegevoegd aan bestaande zoekindexen of synoniemenkaarten, kan het worden bijgewerkt door verschillende waarden op te geven voor een van de drie sleutelkluisdetails (bijvoorbeeld het bijwerken van de `encryptionKey` sleutelversie). Bij het wijzigen naar een nieuwe Key Vault-sleutel of een nieuwe sleutelversie, moet elke zoekindex of synoniemenkaart die gebruikmaakt van de sleutel eerst worden bijgewerkt om de nieuwe sleutel\versie te gebruiken voordat u **de** vorige sleutel\versie kunt verwijderen. Als u dit niet doet, wordt de index of synoniemkaart onbruikbaar, omdat de inhoud niet meer kan worden ontsleuteld zodra de toegang tot de sleutel is verloren. Hoewel het herstellen van toegangsmachtigingen voor Key Vault op een later tijdstip inhoudstoegang herstelt.

## <a name="simpler-alternative-trusted-service"></a>Eenvoudiger alternatief: Vertrouwde service

Afhankelijk van de tenantconfiguratie en verificatievereisten kunt u mogelijk een eenvoudigere benadering implementeren voor toegang tot een sleutelkluis. In plaats van een Active Directory-toepassing te maken en te gebruiken, kunt u van een zoekservice een vertrouwde service maken door er een door het systeem beheerde identiteit voor in te stellen. Vervolgens gebruikt u de vertrouwde zoekservice als een beveiligingsprincipe in plaats van een bij AD geregistreerde toepassing voor toegang tot de sleutelkluissleutel.

Met deze aanpak kunt u de stappen voor toepassingsregistratie en toepassingsgeheimen weglaten en vereenvoudigt u een definitie van een versleutelingssleutel voor alleen de onderdelen van de sleutelkluis (URI, kluisnaam, sleutelversie).

Over het algemeen stelt een beheerde identiteit uw zoekservice in staat om te verifiëren bij Azure Key Vault zonder referenties (ApplicationID of ApplicationSecret) in code op te slaan. De levenscyclus van dit type beheerde identiteit is gekoppeld aan de levenscyclus van uw zoekservice, die slechts één beheerde identiteit kan hebben. Zie Wat zijn beheerde identiteiten voor [Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over hoe beheerde identiteiten werken.

1. Maak van uw zoekservice een vertrouwde service.
   ![Door het systeem toegewezen beheerde identiteit in te zetten](./media/search-managed-identities/turn-on-system-assigned-identity.png "Door het systeem toegewezen beheerde identiteit in te zetten")

1. Bij het instellen van een toegangsbeleid in Azure Key Vault kiest u de vertrouwde zoekservice als het principe (in plaats van de bij AD geregistreerde toepassing). Wijs dezelfde machtigingen toe (meerdere GET's, WRAP, UNWRAP) zoals wordt geïnstrueerd in de stap toegangssleutelmachtigingen verlenen.

1. Gebruik een vereenvoudigde constructie van de `encryptionKey` die de Active Directory-eigenschappen weglaten.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Voorwaarden die verhinderen dat u deze vereenvoudigde benadering gaat hanteren, zijn onder andere:

+ U kunt uw zoekservice niet rechtstreeks toegangsrechten verlenen tot de sleutelkluis (bijvoorbeeld als de zoekservice zich in een andere Active Directory-tenant dan de Azure Key Vault).

+ Eén zoekservice is vereist voor het hosten van meerdere versleutelde indexen\synoniemenkaarten, die elk een  andere sleutel gebruiken dan een andere sleutelkluis, waarbij elke sleutelkluis een andere identiteit moet gebruiken voor verificatie. Omdat een zoekservice slechts één beheerde identiteit kan hebben, komt de vereenvoudigde benadering voor uw scenario niet in aanmerking voor vereisten voor meerdere identiteiten.  

## <a name="work-with-encrypted-content"></a>Werken met versleutelde inhoud

Met door de klant beheerde sleutelversleuteling ziet u latentie voor zowel indexering als query's vanwege het extra versleutelen/ontsleutelen. Azure Cognitive Search geen versleutelingsactiviteit, maar u kunt de toegang tot sleutels bewaken via logboekregistratie van de sleutelkluis. U wordt aangeraden [logboekregistratie in teschakelen](../key-vault/general/logging.md) als onderdeel van de configuratie van de sleutelkluis.

Er wordt verwacht dat sleutelrotatie na een periode zal plaatsvinden. Wanneer u sleutels roteert, is het belangrijk om deze volgorde te volgen:

1. [Bepaal de sleutel die wordt gebruikt door een index- of synoniemkaart](search-security-get-encryption-keys.md).
1. [Maak een nieuwe sleutel in de sleutelkluis,](../key-vault/keys/quick-create-portal.md)maar laat de oorspronkelijke sleutel beschikbaar.
1. [Werk de eigenschappen van encryptionKey bij](/rest/api/searchservice/update-index) op een index- of synoniemenkaart om de nieuwe waarden te gebruiken. Alleen objecten die oorspronkelijk met deze eigenschap zijn gemaakt, kunnen worden bijgewerkt om een andere waarde te gebruiken.
1. Schakel de vorige sleutel in de sleutelkluis uit of verwijder deze. Controleer de sleuteltoegang om te controleren of de nieuwe sleutel wordt gebruikt.

Uit prestatieoverwegingen wordt de sleutel door de zoekservice maximaal enkele uren in de cache opgeslagen. Als u de sleutel uit- of verwijdert zonder een nieuwe op te geven, blijven query's tijdelijk werken totdat de cache verloopt. Zodra de zoekservice inhoud echter niet kan ontsleutelen, krijgt u het volgende bericht: 'Toegang verboden. De gebruikte querysleutel is mogelijk ingetrokken. Probeer het opnieuw. 

## <a name="next-steps"></a>Volgende stappen

Als u niet bekend bent met Azure-beveiligingsarchitectuur, bekijkt u de [Azure-beveiligingsdocumentatie](../security/index.yml)en in het bijzonder dit artikel:

> [!div class="nextstepaction"]
> [Gegevensversleuteling in rust](../security/fundamentals/encryption-atrest.md)
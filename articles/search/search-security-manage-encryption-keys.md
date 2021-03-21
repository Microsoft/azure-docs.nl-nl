---
title: 'Versleuteling: op rust met door de klant beheerde sleutels'
titleSuffix: Azure Cognitive Search
description: Aanvulling op server-side encryptie over indexen en synoniemen in azure Cognitive Search met behulp van sleutels die u in Azure Key Vault maakt en beheert.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 6b1079797f1a753fa8362d6e920f3394087d7e9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119285"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Door de klant beheerde sleutels voor gegevens versleuteling configureren in azure Cognitive Search

Azure Cognitive Search versleutelt automatisch geïndexeerde inhoud met door [service beheerde sleutels](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Als er meer beveiliging nodig is, kunt u standaard versleuteling aanvullen met een extra versleutelings laag met behulp van sleutels die u in Azure Key Vault maakt en beheert. Dit artikel begeleidt u stapsgewijs door de stappen voor het instellen van door de klant beheerde sleutel versleuteling.

Door de klant beheerde sleutel versleuteling is afhankelijk van [Azure Key Vault](../key-vault/general/overview.md). U kunt uw eigen versleutelings sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Api's van Azure Key Vault gebruiken om versleutelings sleutels te genereren. Met Azure Key Vault kunt u ook het sleutel gebruik controleren als u [logboek registratie inschakelt](../key-vault/general/logging.md).  

Versleuteling met door de klant beheerde sleutels wordt toegepast op afzonderlijke indexen of synoniemen kaarten wanneer deze objecten worden gemaakt en niet op het niveau van de zoek service zelf worden opgegeven. Alleen nieuwe objecten kunnen worden versleuteld. U kunt geen inhoud versleutelen die al bestaat.

Sleutels hoeven niet allemaal in dezelfde sleutel kluis te zijn. Eén zoek service kan fungeren als host voor meerdere versleutelde indexen of synoniemen, die zijn versleuteld met hun eigen door de klant beheerde versleutelings sleutels, die zijn opgeslagen in verschillende sleutel kluizen. U kunt ook indexen en synoniemen toewijzen in dezelfde service die niet zijn versleuteld met door de klant beheerde sleutels.

>[!Important]
> Als u door de klant beheerde sleutels implementeert, moet u ervoor zorgen dat u strikte procedures volgt tijdens de routine wisseling van sleutel kluis sleutels en Active Directory toepassings geheimen en-registratie. Alle versleutelde inhoud altijd bijwerken om nieuwe geheimen en sleutels te gebruiken voordat de oude worden verwijderd. Als u deze stap mist, kan de inhoud niet worden ontsleuteld.

## <a name="double-encryption"></a>Dubbele versleuteling

Voor services die zijn gemaakt na 1 augustus 2020 en in specifieke regio's, omvat het bereik van door de klant beheerde sleutel versleuteling tijdelijke schijven, met [volledige dubbele versleuteling](search-security-overview.md#double-encryption), die momenteel beschikbaar is in deze regio's: 

+ VS - west 2
+ VS - oost
+ VS - zuid-centraal
+ VS (overheid) - Virginia
+ VS (overheid) - Arizona

Als u een andere regio gebruikt, of een service die is gemaakt vóór 1 augustus, is de beheerde sleutel versleuteling beperkt tot alleen de gegevens schijf, met uitzonde ring van de tijdelijke schijven die door de service worden gebruikt.

## <a name="prerequisites"></a>Vereisten

De volgende hulpprogram ma's en services worden in dit scenario gebruikt.

+ [Azure-Cognitive Search](search-create-service-portal.md) op een [factureer bare laag](search-sku-tier.md#tier-descriptions) (Basic of hoger, in een wille keurige regio).
+ [Azure Key Vault](../key-vault/general/overview.md)kunt u een sleutel kluis maken met behulp van [Azure Portal](../key-vault//general/quick-create-portal.md), [Azure cli](../key-vault//general/quick-create-cli.md)of [Azure PowerShell](../key-vault//general/quick-create-powershell.md). in hetzelfde abonnement als Azure Cognitive Search. De sleutel kluis moet de beveiliging **voorlopig verwijderen** en **leegmaken** hebben ingeschakeld.
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Als u er nog geen hebt, [stelt u een nieuwe Tenant](../active-directory/develop/quickstart-create-new-tenant.md)in.

U moet een zoek toepassing hebben die het versleutelde object kan maken. In deze code verwijzen we naar een sleutel kluis sleutel en Active Directory registratie gegevens. Deze code kan een werk-app zijn, of prototype code, zoals de [C#-code voorbeeld DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> U kunt [postman](search-get-started-rest.md), [Visual Studio code](search-get-started-vs-code.md)of [Azure PowerShell](./search-get-started-powershell.md)gebruiken om rest-api's aan te roepen waarmee indexen en synoniemen worden gemaakt die een versleutelings sleutel parameter bevatten. Er is op dit moment geen portal ondersteuning voor het toevoegen van een sleutel aan indexen of synoniemen.

## <a name="1---enable-key-recovery"></a>1-sleutel herstel inschakelen

Vanwege de aard van de versleuteling met door de klant beheerde sleutels kan niemand uw gegevens ophalen als uw sleutel kluis sleutel van Azure wordt verwijderd. Als u gegevens verlies wilt voor komen dat wordt veroorzaakt door onopzettelijke verwijderingen van Key Vault sleutels, moet u de beveiliging van zacht verwijderen en leegmaken inschakelen op de sleutel kluis. Zacht verwijderen is standaard ingeschakeld, zodat u alleen problemen ondervindt als u deze voor het doel heeft uitgeschakeld. Het opschonen van de beveiliging is niet standaard ingeschakeld, maar is wel vereist voor door de klant beheerde sleutel versleuteling in Cognitive Search. Zie voor meer informatie [voorlopig verwijderen](../key-vault/general/soft-delete-overview.md) en beveiligings overzichten [opschonen](../key-vault/general/soft-delete-overview.md#purge-protection) .

U kunt beide eigenschappen instellen met behulp van de portal-, Power shell-of Azure CLI-opdrachten.

### <a name="using-azure-portal"></a>Azure Portal gebruiken

1. [Meld u aan bij Azure Portal](https://portal.azure.com) en open de overzichts pagina van uw sleutel kluis.

1. Schakel op de pagina **overzicht** onder **Essentials** de optie **zacht verwijderen** en **beveiliging opschonen** in.

### <a name="using-powershell"></a>PowerShell gebruiken

1. Voer uit `Connect-AzAccount` om uw Azure-referenties in te stellen.

1. Voer de volgende opdracht uit om verbinding te maken met uw sleutel kluis en te vervangen `<vault_name>` door een geldige naam:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault is gemaakt met zacht verwijderen ingeschakeld. Als deze is uitgeschakeld op uw kluis, voert u de volgende opdracht uit:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Leegmaken van beveiliging inschakelen:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Sla uw updates op:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Azure CLI gebruiken

+ Als u een [installatie van Azure cli](/cli/azure/install-azure-cli)hebt, kunt u de volgende opdracht uitvoeren om de vereiste eigenschappen in te scha kelen.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2-een sleutel maken in Key Vault

Sla deze stap over als u al een sleutel in Azure Key Vault hebt.

1. [Meld u aan bij Azure Portal](https://portal.azure.com) en open de overzichts pagina van uw sleutel kluis.

1. Selecteer **sleutels** aan de linkerkant en selecteer **+ genereren/importeren**.

1. Kies in het deel venster **een sleutel maken** in de lijst met **Opties** de methode die u wilt gebruiken om een sleutel te maken. U kunt een nieuwe sleutel **genereren** , een bestaande sleutel **uploaden** of **back-up terugzetten** gebruiken om een back-up van een sleutel te selecteren.

1. Voer een **naam** in voor uw sleutel en selecteer desgewenst andere sleutel eigenschappen.

1. Selecteer **maken** om de implementatie te starten.

1. Noteer de sleutel-id: deze bestaat uit de **sleutel waarde-URI**, de naam van de **sleutel** en de **sleutel versie**. U hebt de id nodig om een versleutelde index in azure Cognitive Search te definiëren.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Een nieuwe sleutel kluis sleutel maken":::

## <a name="3---register-an-app-in-active-directory"></a>3: een app registreren in Active Directory

1. Zoek in [Azure Portal](https://portal.azure.com)de Azure Active Directory resource voor uw abonnement.

1. Selecteer aan de linkerkant onder **beheren** de optie **app-registraties** en selecteer vervolgens **nieuwe registratie**.

1. Geef de registratie een naam, mogelijk een naam die overeenkomt met de naam van de zoek toepassing. Selecteer **Registreren**.

1. Wanneer de app-registratie is gemaakt, kopieert u de toepassings-ID. U moet deze teken reeks opgeven voor uw toepassing. 

   Als u de [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)stapsgewijs doorloopt, plakt u deze waarde in de **appsettings.jsin** het bestand.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Toepassings-ID in de sectie Essentials":::

1. Selecteer vervolgens **certificaten & geheimen** aan de linkerkant.

1. Selecteer **Nieuw clientgeheim**. Geef het geheim een weergave naam en selecteer **toevoegen**.

1. Kopieer het toepassings geheim. Als u het voor beeld stapsgewijs doorloopt, plakt u deze waarde in het **appsettings.js** bestand.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Toepassingsgeheim":::

## <a name="4---grant-key-access-permissions"></a>4-sleutel toegangs machtigingen verlenen

In deze stap maakt u een toegangs beleid in Key Vault. Dit beleid geeft de toepassing die u hebt geregistreerd met Active Directory toestemming om uw door de klant beheerde sleutel te gebruiken.

Toegangs machtigingen kunnen op elk gewenst moment worden ingetrokken. Na intrekking worden alle zoek service-indexen of synoniemen toewijzingen die gebruikmaken van die sleutel kluis, onbruikbaar. Als u de toegangs machtigingen voor de sleutel kluis op een later tijdstip herstelt, wordt de toegang tot index\synonym-kaarten hersteld. Zie [Secure Access to a key kluis](../key-vault/general/secure-your-key-vault.md)(Engelstalig) voor meer informatie.

1. Open de pagina **overzicht** van sleutel kluis op de Azure Portal. 

1. Selecteer het **toegangs beleid** aan de linkerkant en selecteer **+ toegangs beleid toevoegen**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Nieuw sleutel kluis toegangs beleid toevoegen":::

1. Kies **Principal selecteren** en selecteer de toepassing die u hebt geregistreerd bij Active Directory. U kunt zoeken op naam.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Sleutel kluis toegangs beleid-Principal selecteren":::

1. In **sleutel machtigingen** kiest u *ophalen*, de *toets indrukken* en de *ingepakte sleutel*.

1. Selecteer in **geheime machtigingen** *ophalen*.

1. Selecteer in **certificaat machtigingen** *ophalen*.

1. Selecteer **toevoegen** en vervolgens **Opslaan**.

> [!Important]
> Versleutelde inhoud in azure Cognitive Search is geconfigureerd voor het gebruik van een specifieke Azure Key Vault sleutel met een specifieke **versie**. Als u de sleutel of versie wijzigt, moet de toewijzing van de index of het synoniem worden bijgewerkt voor gebruik van de nieuwe key\version **voordat** u de vorige key\version. verwijdert Als u dit niet doet, wordt de toewijzing van de index of het synoniem onbruikbaar. u kunt de inhoud niet ontsleutelen wanneer de sleutel toegang is verbroken.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5-inhoud versleutelen

Als u een door de klant beheerde sleutel wilt toevoegen aan een index, gegevens bron, vaardig heden, Indexeer functie of synoniemen toewijzing, moet u de [zoek rest API](/rest/api/searchservice/) of een SDK gebruiken. Synoniemen kaarten of versleutelings eigenschappen worden niet weer gegeven in de portal. Wanneer u een geldige API-index gebruikt, ondersteunen gegevens bronnen, vaardig heden, Indexeer functies en synoniemen kaarten een **encryptionKey** -eigenschap op het hoogste niveau.

In dit voor beeld wordt de REST API gebruikt met waarden voor Azure Key Vault en Azure Active Directory:

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
> Geen van deze sleutel kluis Details worden beschouwd als geheim en kunnen gemakkelijk worden opgehaald door te bladeren naar de relevante Azure Key Vault sleutel pagina in Azure Portal.

## <a name="example-index-encryption"></a>Voor beeld: index versleuteling

Maak een versleutelde index met behulp van de [rest API Create Index Azure Cognitive Search](/rest/api/searchservice/create-index). Gebruik de `encryptionKey` eigenschap om op te geven welke versleutelings sleutel moet worden gebruikt.
> [!Note]
> Geen van deze sleutel kluis Details worden beschouwd als geheim en kunnen gemakkelijk worden opgehaald door te bladeren naar de relevante Azure Key Vault sleutel pagina in Azure Portal.

## <a name="rest-examples"></a>REST-voor beelden

Deze sectie toont de volledige JSON voor een versleutelde index en synoniemen toewijzing

### <a name="index-encryption"></a>Index versleuteling

De details van het maken van een nieuwe index via de REST API zijn te vinden in [Create Index (rest API)](/rest/api/searchservice/create-index), waarbij het enige verschil hier de details van de versleutelings sleutel opgeeft als onderdeel van de index definitie:

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

U kunt de aanvraag voor het maken van de index nu verzenden en de index vervolgens normaal gaan gebruiken.

### <a name="synonym-map-encryption"></a>Synoniemen kaart versleuteling

Maak een versleutelde synoniemen toewijzing met behulp van de [toewijzing Create synoniem Azure Cognitive Search rest API](/rest/api/searchservice/create-synonym-map). Gebruik de `encryptionKey` eigenschap om op te geven welke versleutelings sleutel moet worden gebruikt.

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

U kunt de aanvraag voor het maken van de synoniemen kaart nu verzenden en vervolgens op de normale manier gebruiken.

## <a name="example-data-source-encryption"></a>Voor beeld: versleuteling van gegevens bron

Maak een versleutelde gegevens bron met behulp van de [gegevens bron maken (Azure Cognitive Search rest API)](/rest/api/searchservice/create-data-source). Gebruik de `encryptionKey` eigenschap om op te geven welke versleutelings sleutel moet worden gebruikt.

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

U kunt de aanvraag voor het maken van de gegevens bron nu verzenden en vervolgens op de normale manier gebruiken.

## <a name="example-skillset-encryption"></a>Voor beeld: Vaardigheidset-versleuteling

Maak een versleutelde vaardig heden met behulp van de [rest API Create vaardig heden Azure Cognitive Search](/rest/api/searchservice/create-skillset). Gebruik de `encryptionKey` eigenschap om op te geven welke versleutelings sleutel moet worden gebruikt.

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

U kunt nu de aanvraag voor het maken van vaardig heden verzenden en deze vervolgens op de normale manier gebruiken.

## <a name="example-indexer-encryption"></a>Voor beeld: indexer versleuteling

Maak een versleutelde Indexeer functie met behulp van de [rest API Create Indexeer functie Azure Cognitive Search](/rest/api/searchservice/create-indexer). Gebruik de `encryptionKey` eigenschap om op te geven welke versleutelings sleutel moet worden gebruikt.

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

U kunt de aanvraag voor het maken van de Indexeer functie nu verzenden en vervolgens op de normale manier gebruiken.

>[!Important]
> Hoewel het `encryptionKey` niet kan worden toegevoegd aan bestaande zoek indexen of synoniemen toewijzingen, kan deze worden bijgewerkt door andere waarden op te geven voor een van de drie sleutel kluis Details (bijvoorbeeld het bijwerken van de sleutel versie). Wanneer u overstapt naar een nieuwe Key Vault sleutel of een nieuwe sleutel versie, moet een zoek index of synoniem toewijzing die de sleutel gebruikt eerst worden bijgewerkt om de nieuwe key\version te gebruiken **voordat** u de vorige key\version. verwijdert. Als u dit niet doet, wordt de toewijzing van de index of het synoniem onbruikbaar, omdat het niet mogelijk is om de inhoud te ontsleutelen wanneer de sleutel toegang verloren is gegaan. Hoewel de toegangs machtigingen voor de sleutel kluis op een later tijdstip worden teruggezet, wordt de toegang tot inhoud hersteld.

## <a name="simpler-alternative-trusted-service"></a>Eenvoudiger alternatief: vertrouwde service

Afhankelijk van de configuratie-en verificatie vereisten van de Tenant, kunt u een eenvoudigere benadering implementeren om toegang te krijgen tot een sleutel kluis sleutel. In plaats van een Active Directory toepassing te maken en te gebruiken, kunt u een zoek service een vertrouwde service maken door een door het systeem beheerde identiteit in te scha kelen. Vervolgens gebruikt u de vertrouwde zoek service als beveiligings principe, in plaats van een AD-geregistreerde toepassing, om toegang te krijgen tot de sleutel kluis sleutel.

Met deze aanpak kunt u de stappen voor het registreren van toepassingen en toepassings geheimen weglaten en wordt de definitie van een versleutelings sleutel vereenvoudigd tot alleen de sleutel kluis onderdelen (URI, kluis naam, sleutel versie).

In het algemeen zorgt een beheerde identiteit ervoor dat uw zoek service kan worden geverifieerd bij Azure Key Vault zonder dat u referenties (ApplicationID of ApplicationSecret) in code hoeft op te slaan. De levens cyclus van dit type beheerde identiteit is gekoppeld aan de levens cyclus van uw zoek service, maar kan slechts één beheerde identiteit hebben. Zie [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over de werking van beheerde identiteiten.

1. Uw zoek service een vertrouwde service maken.
   ![Door het systeem toegewezen beheerde identiteit inschakelen](./media/search-managed-identities/turn-on-system-assigned-identity.png "Door het systeem toegewezen beheerde identiteit inschakelen")

1. Wanneer u een toegangs beleid instelt in Azure Key Vault, kiest u de vertrouwde zoek service als principe (in plaats van de AD-geregistreerde toepassing). Wijs dezelfde machtigingen toe (meerdere ophalen, TERUGLOPEN, uitpakken), zoals wordt geïnstrueerd in de stap machtigingen voor toegangs sleutel verlenen.

1. Gebruik een vereenvoudigde constructie van de `encryptionKey` die de Active Directory eigenschappen weglaat.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Voor waarden die voor komen dat u deze vereenvoudigde benadering kunt aannemen, zijn:

+ U kunt de toegangs machtigingen van de zoek service niet rechtstreeks verlenen aan de sleutel kluis (bijvoorbeeld als de zoek service zich in een andere Active Directory Tenant bevindt dan de Azure Key Vault).

+ Er is één zoek service vereist voor het hosten van meerdere versleutelde indexes\synonym-kaarten, elk met een andere sleutel van een andere sleutel kluis, waarbij elke sleutel kluis **een andere identiteit** voor verificatie moet gebruiken. Omdat een zoek service slechts één beheerde identiteit kan hebben, wordt de vereenvoudigde benadering voor uw scenario niet door een van de vereisten voor meerdere identiteiten in aanmerking komt.  

## <a name="work-with-encrypted-content"></a>Werken met versleutelde inhoud

Met door de klant beheerde sleutel versleuteling ziet u latentie voor zowel indexering als query's als gevolg van het extra versleutelen/ontsleutelen. Met Azure Cognitive Search worden geen versleutelings activiteiten geregistreerd, maar u kunt de toegang tot sleutels controleren via de sleutel kluis logboek registratie. Het is raadzaam [logboek registratie in te scha kelen](../key-vault/general/logging.md) als onderdeel van de sleutel kluis configuratie.

Er wordt naar verwachting over een bepaalde periode gedraaid. Wanneer u sleutels roteert, is het belang rijk dat u deze volg orde volgt:

1. [Bepaal de sleutel die wordt gebruikt door een index of synoniemen kaart](search-security-get-encryption-keys.md).
1. [Maak een nieuwe sleutel in de sleutel kluis](../key-vault/keys/quick-create-portal.md), maar behoud de oorspronkelijke sleutel beschikbaar.
1. [Werk de eigenschappen van encryptionKey](/rest/api/searchservice/update-index) op een index of synoniemen kaart bij om de nieuwe waarden te gebruiken. Alleen objecten die oorspronkelijk zijn gemaakt met deze eigenschap kunnen worden bijgewerkt om een andere waarde te gebruiken.
1. Schakel de vorige sleutel in de sleutel kluis uit of verwijder deze. Controleer de sleutel toegang om te controleren of de nieuwe sleutel wordt gebruikt.

Uit prestatie overwegingen slaat de zoek service de sleutel tot enkele uren in de cache op. Als u de sleutel uitschakelt of verwijdert zonder dat er een nieuwe wordt opgegeven, blijven query's tijdelijk werken totdat de cache is verlopen. Als de zoek service echter inhoud niet kan ontsleutelen, ontvangt u dit bericht: "toegang geweigerd. De gebruikte query sleutel is mogelijk ingetrokken. Probeer het opnieuw. 

## <a name="next-steps"></a>Volgende stappen

Als u niet bekend bent met de Azure-beveiligings architectuur, raadpleegt u de [documentatie voor Azure-beveiliging](../security/index.yml), met name dit artikel:

> [!div class="nextstepaction"]
> [Gegevensversleuteling in rust](../security/fundamentals/encryption-atrest.md)
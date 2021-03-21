---
title: Zoeken in versleutelde inhoud van Azure Blob-opslag
titleSuffix: Azure Cognitive Search
description: Meer informatie over het indexeren en extra heren van tekst uit versleutelde documenten in Azure Blob Storage met Azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96530932"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Versleutelde blobs indexeren met Blob-Indexeer functies en vaardig heden in azure Cognitive Search

In dit artikel wordt beschreven hoe u [Azure Cognitive Search](search-what-is-azure-search.md) gebruikt voor het indexeren van documenten die eerder zijn versleuteld in [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) met behulp van [Azure Key Vault](../key-vault/general/overview.md). Normaal gesp roken kan een Indexeer functie geen inhoud extra heren van versleutelde bestanden omdat deze geen toegang heeft tot de versleutelings sleutel. Door gebruik te maken van de aangepaste [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) -vaardigheid, gevolgd door de [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md), kunt u echter beheerde toegang bieden tot de sleutel om de bestanden te ontsleutelen en vervolgens de inhoud ervan uit te pakken. Hiermee ontgrendelt u de mogelijkheid om deze documenten te indexeren zonder de versleutelings status van uw opgeslagen documenten in gevaar te brengen.

Als u begint met het eerder versleutelde hele documenten (ongestructureerde tekst) zoals PDF, HTML, DOCX en PPTX in Azure Blob-opslag, gebruikt deze hand leiding postman en de zoek REST-Api's om de volgende taken uit te voeren:

> [!div class="checklist"]
> * Een pijp lijn definiëren waarmee de documenten worden ontsleuteld en er tekst uit wordt opgehaald.
> * Definieer een index om de uitvoer op te slaan.
> * Voer de pijp lijn uit om de index te maken en te laden.
> * Bekijk de resultaten met zoeken in volledige tekst en een uitgebreide query-syntaxis.

Als u geen abonnement op Azure hebt, opent u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

In dit voor beeld wordt ervan uitgegaan dat u uw bestanden al naar Azure Blob Storage hebt geüpload en deze in het proces hebt versleuteld. Als u hulp nodig hebt bij het ophalen van uw bestanden die in eerste instantie zijn geüpload en versleuteld, raadpleegt u [deze zelf studie](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) voor informatie over hoe u dit doet.

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) in hetzelfde abonnement als Azure Cognitive Search. De sleutel kluis moet de beveiliging **voorlopig verwijderen** en **leegmaken** hebben ingeschakeld.
+ [Azure-Cognitive Search](search-create-service-portal.md) op een [factureer bare laag](search-sku-tier.md#tier-descriptions) (Basic of hoger, in een wille keurige regio)
+ [Azure-functie](https://azure.microsoft.com/services/functions/)
+ [Postman bureaublad-app](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1-services maken en referenties verzamelen

### <a name="set-up-the-custom-skill"></a>De aangepaste vaardigheid instellen

In dit voor beeld wordt het voor beeld- [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) -project gebruikt uit de GitHub-opslag plaats [Azure Search Power skills](https://github.com/Azure-Samples/azure-search-power-skills) . In deze sectie implementeert u de vaardigheid van een Azure-functie zodat deze kan worden gebruikt in een vakkennisset. Een ingebouwd implementatie script maakt een Azure-functie resource met de naam die begint met **psdbf-function-app** en laadt de vaardigheid. U wordt gevraagd om een abonnement en een resource groep op te geven. Zorg ervoor dat u hetzelfde abonnement kiest als uw Azure Key Vault-exemplaar in het leven.

In de DecryptBlobFile-vaardigheid wordt de URL en het SAS-token voor elke BLOB als invoer gebruikt en wordt het gedecodeerde, ontsleutelde bestand uitgevoerd met het bestand referentie contract dat Azure Cognitive Search verwacht. De DecryptBlobFile moet de versleutelings sleutel hebben om de versleuteling uit te voeren. Als onderdeel van het instellen maakt u ook een toegangs beleid waarmee de DecryptBlobFile-functie toegang krijgt tot de versleutelings sleutel in Azure Key Vault.

1. Klik op de knop **implementeren naar Azure** op de [landings pagina voor DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment). Hiermee wordt de meegeleverde Resource Manager-sjabloon in de Azure portal geopend.

1. Selecteer **het abonnement waar uw Azure Key Vault-exemplaar bestaat** (deze hand leiding werkt niet als u een ander abonnement selecteert) en selecteer een bestaande resource groep of maak een nieuwe. Als u een nieuwe wilt maken, moet u ook een regio selecteren waarop u wilt implementeren.

1. Selecteer **controleren + maken**, zorg ervoor dat u akkoord gaat met de voor waarden en selecteer vervolgens **maken** om de Azure-functie te implementeren.

    ![ARM-sjabloon in Portal](media/indexing-encrypted-blob-files/arm-template.jpg "ARM-sjabloon in Portal")

1. Wacht tot de implementatie is voltooid.

1. Navigeer naar uw Azure Key Vault-exemplaar in de portal. [Maak een toegangs beleid](../key-vault/general/assign-access-policy-portal.md) in de Azure Key Vault dat belang rijke toegang verleent aan de aangepaste vaardigheid.
 
    1. Selecteer onder **instellingen** **toegangs beleid** en selecteer vervolgens **toegangs beleid toevoegen** .
     
       ![Toegangs beleid voor sleutel kluis toevoegen](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Toegangs beleid voor de sleutel kluis")

    1. Onder **configureren vanaf sjabloon** selecteert u **Azure data Lake Storage of Azure Storage**.

    1. Selecteer voor de principal het Azure-functie-exemplaar dat u hebt geïmplementeerd. U kunt ernaar zoeken met het resource-voor voegsel dat is gebruikt om het te maken in stap 2, dat een standaard voorvoegsel waarde heeft van **psdbf-function-app**.

    1. Selecteer niets voor de optie voor de geautoriseerde toepassing.
     
        ![Sjabloon voor het toegangs beleid voor de sleutel kluis toevoegen](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Sjabloon voor het toegangs beleid voor de sleutel kluis")

    1. Zorg ervoor dat u op de pagina toegangs beleid op **Opslaan** klikt voordat u het toegangs beleid daad werkelijk gaat toevoegen.
     
         ![Het toegangs beleid voor de sleutel kluis opslaan](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Toegangs beleid voor de sleutel kluis opslaan")

1. Ga naar de functie **psdbf-function-app** in de portal en noteer de volgende eigenschappen, zoals u ze later in de hand leiding nodig hebt:

    1. De functie-URL, die u kunt vinden onder **Essentials** op de hoofd pagina voor de functie.
    
        ![Functie-URL](media/indexing-encrypted-blob-files/function-uri.jpg "Waar vind ik de URL van de Azure-functie?")

    1. De host-sleutel code, die u kunt vinden door te navigeren naar **app-sleutels**, te klikken om de **standaard** sleutel weer te geven en de waarde te kopiëren.
     
        ![Functie host-sleutel code](media/indexing-encrypted-blob-files/function-host-key.jpg "Waar vind ik de Azure function host-sleutel code")

### <a name="cognitive-services"></a>Cognitive Services

AI-verrijking en vaardig heden-uitvoering worden ondersteund door Cognitive Services, met inbegrip van Text Analytics en Computer Vision voor natuurlijke taal en afbeeldings verwerking. Als u een echt prototype of project wilt voltooien, moet u op dit punt Cognitive Services (in dezelfde regio als Azure Cognitive Search) inrichten, zodat u het kunt koppelen aan indexbewerkingen.

Voor deze oefening kunt u echter het inrichten van resources overslaan omdat Azure Cognitive Search verbinding kan maken met Cognitive Services achter de schermen en u 20 gratis transacties per uitvoering van indexeerfunctie geeft. Na het verwerken van 20 documenten, mislukt de Indexeer functie, tenzij een Cognitive Services sleutel is gekoppeld aan de vaardig heden. Voor grotere projecten plant u het inrichten van Cognitive Services op de S0-laag voor betalen per gebruik. Zie voor meer informatie [Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md). Houd er rekening mee dat een Cognitive Services sleutel vereist is voor het uitvoeren van een vaardig heden met meer dan 20 documenten, zelfs als geen van de geselecteerde cognitieve vaardig heden verbinding maakt met Cognitive Services (zoals met de opgegeven vakkennis als er geen vaardig heden aan zijn toegevoegd).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Het laatste onderdeel is Azure Cognitive Search, dat u [in de portal kunt maken](search-create-service-portal.md). U kunt de gratis laag gebruiken om deze hand leiding te volt ooien. 

Neem even de tijd om de beheer sleutel te verzamelen, net als bij de Azure-functie. Daarnaast moet u, wanneer u begint met het structureren van aanvragen, het eindpunt en de beheer-API-sleutel opgeven die worden gebruikt om elke aanvraag te verifiëren.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Een beheer-API-sleutel en URL voor Azure Cognitive Search ophalen

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/) en haal op de pagina **Overzicht** de naam van uw zoekservice op. U kunt uw servicenaam bevestigen door de URL van het eindpunt te controleren. Als uw eindpunt-URL `https://mydemo.search.windows.net` is, wordt uw service naam `mydemo`.

2. Haal onder **Instellingen** > **Sleutels** een beheersleutel op voor volledige rechten op de service. Er zijn twee uitwisselbare beheersleutels die voor bedrijfscontinuïteit worden verstrekt voor het geval u een moet overschakelen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   ![De naam van de service en de querysleutels voor beheer ophalen](media/search-get-started-javascript/service-name-and-keys.png)

Voor alle aanvragen is een API-sleutel vereist in de header die naar uw service wordt verzonden. Me een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="2---set-up-postman"></a>2 - Postman instellen

Postman installeren en instellen.

### <a name="download-and-install-postman"></a>Postman downloaden en installeren

1. Download de [broncode van de Postman-verzameling](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. Selecteer **Bestand** > **Importeren** om de broncode te importeren in Postman.
1. Selecteer het tabblad **Verzamelingen** en selecteer vervolgens de knop met het beletselteken **...** .
1. Selecteer **Bewerken**. 
   
   ![Postman-app met navigatie](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Ga naar het menu Bewerken in Postman")
1. Selecteer in het dialoogvenster **Bewerken** het tabblad **Variabelen**. 

U kunt op het tabblad **Variabelen** waarden toevoegen die Postman steeds verwisselt wanneer een specifieke variabele wordt aangetroffen binnen dubbele accolades. Postman vervangt bijvoorbeeld het symbool `{{admin-key}}` door de huidige waarde die u hebt ingesteld voor `admin-key`. Postman vervangt de URL's, kopteksten, de hoofdtekst van de aanvraag, enzovoort. 

Als u de waarde voor wilt ophalen `admin-key` , gebruikt u de Azure Cognitive Search admin API-sleutel die u eerder hebt genoteerd. Stel `search-service-name` in op de naam van de Azure Cognitive Search-service die u gebruikt. Stel in `storage-connection-string` met behulp van de waarde op het tabblad **toegangs sleutels** van uw opslag account en stel `storage-container-name` in op de naam van de BLOB-container op het opslag account waarin de versleutelde bestanden worden opgeslagen. Stel `function-uri` in op de URL van de Azure-functie die u eerder hebt genoteerd en stel `function-code` in op de Azure function host-sleutel code die u eerder hebt genoteerd. U kunt de standaard waarde voor de andere waarden laten staan.

![Het tabblad met app-variabelen in Postman](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Het venster met variabelen in Postman")

| Variabele    | Waar te verkrijgen? |
|-------------|-----------------|
| `admin-key` | Op de pagina **Sleutels** van de Azure Cognitive Search-service.  |
| `search-service-name` | De naam van de Azure Cognitive Search-service. De URL is `https://{{search-service-name}}.search.windows.net`. |
| `storage-connection-string` | Selecteer in het opslagaccount op het tabblad **Toegangssleutels** de **key1** >  **-verbindingstekenreeks**. |
| `storage-container-name` | De naam van de BLOB-container met de versleutelde bestanden die moeten worden geïndexeerd. |
| `function-uri` |  In de Azure-functie onder **Essentials** op de hoofd pagina. |
| `function-code` | Klik in de functie Azure, door te navigeren naar **app-sleutels**, op om de **standaard** sleutel weer te geven en de waarde te kopiëren. |
| `api-version` | Laat deze staan op **2020-06-30**. |
| `datasource-name` | Als **versleutelde blobs-DS** laten. |
| `index-name` | **Versleuteld-blobs-idx** laten staan. |
| `skillset-name` | **Versleuteld-blobs-SS** laten staan. |
| `indexer-name` | Zorg ervoor dat **versleutelde blobs-IXR**. |

### <a name="review-the-request-collection-in-postman"></a>De verzameling aanvragen weergeven in Postman

Wanneer u deze hand leiding uitvoert, moet u vier HTTP-aanvragen doen:

- **PUT-aanvraag om de index te maken**: Deze index bevat de gegevens die door Azure Cognitive Search worden gebruikt en geretourneerd.
- **Post-aanvraag voor het maken van de gegevens bron**: deze gegevens bron verbindt uw Azure Cognitive Search-service met uw opslag account en daarom versleutelde BLOB-bestanden. 
- De **aanvraag voor het maken van de vaardig heden plaatsen: in** de vaardig heden wordt de aangepaste vaardigheids definitie opgegeven voor de Azure-functie waarmee de BLOB-bestands gegevens worden ontsleuteld, en een [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) om de tekst uit elk document te extra heren nadat deze is ontsleuteld.
- **PUT-aanvraag om de indexeerfunctie te maken**: Als de indexeerfunctie wordt uitgevoerd, worden de gegevens gelezen, wordt de vaardighedenset toegepast en worden de resultaten opgeslagen. U moet deze aanvraag als laatste uitvoeren.

De [bron code](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) bevat een postman-verzameling met de vier aanvragen, evenals een aantal nuttige opvolgings aanvragen. Als u de aanvragen wilt uitgeven, selecteert u in postman het tabblad voor de aanvragen en selecteert u **verzenden** voor elke aanvraag.

## <a name="3---monitor-indexing"></a>3: indexeren controleren

Het indexeren en verrijken begint zodra u de aanvraag Indexeerfunctie maken hebt verzonden. Het indexeren kan enige tijd duren, afhankelijk van het aantal documenten in uw opslag account. Als u wilt weten of de Indexeer functie nog steeds wordt uitgevoerd, gebruikt u de aanvraag voor het ophalen van de **Indexeer functie** , die deel uitmaakt van de Postman-verzameling en bekijkt u het antwoord om te zien of de Indexeer functie wordt uitgevoerd, of om fout-en waarschuwings gegevens weer te geven.  

Als u de gratis laag gebruikt, wordt het volgende bericht verwacht: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Dit bericht wordt weer gegeven omdat het indexeren van blobs in de gratis laag een [limiet van 32 KB voor teken extractie](search-limits-quotas-capacity.md#indexer-limits)heeft. Bij hogere lagen wordt dit bericht niet voor deze gegevensset weergegeven. 

## <a name="4---search"></a>4-zoeken

Nadat de uitvoering van de Indexeer functie is voltooid, kunt u enkele query's uitvoeren om te controleren of de gegevens zijn ontsleuteld en geïndexeerd. Navigeer naar uw Azure Cognitive Search-service in de portal en gebruik de [Search Explorer](search-explorer.md) om query's uit te voeren op de geïndexeerde gegevens.

## <a name="next-steps"></a>Volgende stappen

Nu u de versleutelde bestanden hebt geïndexeerd, kunt u [de pijp lijn herhalen door meer cognitieve vaardig heden toe te voegen](cognitive-search-defining-skillset.md). Zo kunt u uw gegevens verrijken en extra inzichten krijgen.

Als u met dubbele versleutelde gegevens werkt, wilt u mogelijk de index versleutelings functies in azure Cognitive Search onderzoeken. Hoewel de Indexeer functie gegevens voor indexerings doeleinden nodig heeft, kan deze worden versleuteld met een door de klant beheerde sleutel als de index bestaat. Dit zorgt ervoor dat uw gegevens altijd worden versleuteld wanneer de rest. Zie door de [klant beheerde sleutels configureren voor gegevens versleuteling in Azure Cognitive Search](search-security-manage-encryption-keys.md)voor meer informatie.
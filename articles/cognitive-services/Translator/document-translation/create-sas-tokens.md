---
title: Een SAS-token (Shared Access Signature) maken voor containers en blobs met micro soft Storage Explorer
description: Een Shared Access token (SAS) maken voor containers en blobs met micro soft Storage Explorer
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 49813a29009e04c81dae59a7d4da2bae411e07b2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100642386"
---
# <a name="create-sas-tokens-for-document-translation"></a>SAS-tokens maken voor document vertalingen

In dit artikel vindt u informatie over het maken van SAS-tokens (Shared Access Signature) met behulp van de Azure Storage Explorer of Azure Portal. Een SAS-token biedt beveiligde, gedelegeerde toegang tot resources in uw Azure-opslag account.

## <a name="create-sas-tokens-with-azure-storage-explorer"></a>SAS-tokens met Azure Storage Explorer maken

### <a name="prerequisites"></a>Vereisten

* U hebt een [**Azure Storage Explorer**](/azure/vs-azure-tools-storage-manage-with-storage-explorer) -app nodig die is geïnstalleerd in uw Windows-, macOS-of Linux-ontwikkel omgeving. Azure Storage Explorer is een gratis hulp programma waarmee u eenvoudig uw Azure Cloud Storage-resources kunt beheren.
* Nadat de Azure Storage Explorer-app is geïnstalleerd, [verbindt u deze met het opslag account](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#connect-to-a-storage-account-or-service) dat u gebruikt voor document vertalingen.

### <a name="create-your-tokens"></a>Uw tokens maken

### <a name="sas-tokens-for-containers"></a>[SAS-tokens voor containers](#tab/Containers)

1. Open de Azure Storage Explorer-app op uw lokale machine en navigeer naar uw verbonden **opslag accounts**.
1. Vouw het knoop punt opslag accounts uit en selecteer **BLOB-containers**.
1. Vouw het knoop punt BLOB containers uit en klik met de rechter muisknop op een opslag **container** knooppunt of om het menu opties weer te geven.
1. Selecteer **Shared Access Signature ophalen..** . in het menu opties.
1. In het venster **Shared Access Signature** maakt u de volgende opties:
    * Selecteer uw **toegangs beleid** (de standaard waarde is geen).
    * Geef de **begin** -en **verval** datum en tijd van de ondertekende sleutel op. Een korte levens duur wordt aanbevolen omdat een SAS eenmaal is gegenereerd en niet kan worden ingetrokken.
    * Selecteer de **tijd zone** voor de begin-en verval datum en-tijd (de standaard instelling is lokaal).
    * Definieer uw container **machtigingen** door het juiste selectie vakje in en uit te scha kelen.
    * Controleren en selecteer **maken**.

1. Er wordt een nieuw venster weer gegeven met de **container** naam, **URI** en **query teken reeks** voor uw container.  
1. **Kopieer en plak de container-, URI-en query teken reeks waarden op een veilige locatie. Ze worden slechts één keer weer gegeven en kunnen niet meer worden opgehaald zodra het venster is gesloten.**
1. Als u een SAS-URL wilt maken, voegt u de SAS-token (URI) toe aan de URL voor een opslag service.

### <a name="sas-tokens-for-blobs"></a>[SAS-tokens voor blobs](#tab/blobs)

1. Open de Azure Storage Explorer-app op uw lokale machine en navigeer naar uw verbonden **opslag accounts**.
1. Vouw uw opslag knooppunt uit en selecteer **BLOB-containers**.
1. Vouw het knoop punt BLOB containers uit en selecteer een **container** knooppunt om de inhoud in het hoofd venster weer te geven.
1. Selecteer de BLOB waarnaar u de SAS-toegang wilt delegeren en klik met de rechter muisknop om het menu opties weer te geven.
1. Selecteer **Shared Access Signature ophalen..** . in het menu opties.
1. In het venster **Shared Access Signature** maakt u de volgende opties:
    * Selecteer uw **toegangs beleid** (de standaard waarde is geen).
    * Geef de **begin** -en **verval** datum en tijd van de ondertekende sleutel op. Een korte levens duur wordt aanbevolen omdat een SAS eenmaal is gegenereerd en niet kan worden ingetrokken.
    * Selecteer de **tijd zone** voor de begin-en verval datum en-tijd (de standaard instelling is lokaal).
    * Definieer uw container **machtigingen** door het juiste selectie vakje in en uit te scha kelen.
    * Controleren en selecteer **maken**.
1. Er wordt een nieuw venster weer gegeven met de **blobnaam** , de **URI** en de **query teken reeks** voor uw blob.  
1. **Kopieer en plak de waarden van de blob, URI en query teken reeks op een veilige locatie. Ze worden slechts één keer weer gegeven en kunnen niet meer worden opgehaald zodra het venster is gesloten.**
1. Als u een SAS-URL wilt maken, voegt u de SAS-token (URI) toe aan de URL voor een opslag service.

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>SAS-tokens maken voor blobs in de Azure Portal

> [!NOTE]
> Het maken van SAS-tokens voor containers rechtstreeks in het Azure Portal wordt momenteel niet ondersteund. U kunt echter een SAS-token met [**Azure Storage Explorer**](#create-sas-tokens-with-azure-storage-explorer) maken of de taak [via een programma](/azure/storage/blobs/sas-service-create)volt ooien.

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een actief [**Azure-account**](https://azure.microsoft.com/free/cognitive-services/).  Als u er nog geen hebt, kunt u [**een gratis account maken**](https://azure.microsoft.com/free/).
* Een [**Translator**](https://ms.portal.azure.com/#create/Microsoft) -service resource (**niet** een Cognitive Services meerdere service bronnen.  *Zie* [een nieuwe Azure-resource maken](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Een [**Azure Blob-opslag account**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Alle toegang tot Azure Storage vindt plaats via een opslagaccount.

### <a name="create-your-tokens"></a>Uw tokens maken

Ga naar het [Azure Portal](https://ms.portal.azure.com/#home) en navigeert u als volgt:  

 **Uw opslag account** → **containers** → **uw container** → **uw BLOB**

1. Selecteer **SAS genereren** in het menu aan de bovenkant van de pagina.

1. Selecteer **ondertekening methode** → **gebruikers delegering sleutel**.

1. Definieer **machtigingen** door te controleren en/of het juiste selectie vakje uit te scha kelen.

1. Geef de begin-en **verval** tijd van de ondertekende sleutel **op** .

1. Het veld **toegestane IP-adressen** is optioneel en geeft een IP-adres of een bereik van IP-adressen op waaruit aanvragen moeten worden geaccepteerd. Als het IP-adres van de aanvraag niet overeenkomt met het IP-adres of adres bereik dat is opgegeven voor het SAS-token, wordt het niet geautoriseerd.

1. Het veld **toegestane protocollen** is optioneel en geeft het protocol aan dat is toegestaan voor een aanvraag met de SAS. De standaard waarde is HTTPS.

1. Bekijk vervolgens de optie **SAS-token en URL genereren**.

1. De **BLOB SAS-token** query teken reeks en **BLOB SAS-URL** worden weer gegeven in het onderste gedeelte van het venster.  

1. **Kopieer en plak de waarden van de BLOB SAS-token en-URL op een veilige locatie. Ze worden slechts één keer weer gegeven en kunnen niet meer worden opgehaald zodra het venster is gesloten.**

1. Als u een SAS-URL wilt maken, voegt u de SAS-token (URI) toe aan de URL voor een opslag service.

## <a name="learn-more"></a>Lees meer

* [SAS-tokens maken voor blobs of containers via een programma](/azure/storage/blobs/sas-service-create)
* [Machtigingen voor een map, container of BLOB](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met document vertalingen](get-started-with-document-translation.md)
>
>

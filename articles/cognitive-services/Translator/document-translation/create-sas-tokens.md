---
title: Een SAS-token (Shared Access Signature) maken voor containers en blobs met micro soft Storage Explorer
description: Het maken van een Shared Access token (SAS) voor containers en blobs met micro soft Storage Explorer en de Azure Portal
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102489630"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>SAS-tokens maken voor het verwerken van document vertalingen

In dit artikel leert u hoe u SAS-tokens (Shared Access Signature) kunt maken met behulp van de Azure Storage Explorer of de Azure Portal. Een SAS-token biedt beveiligde, gedelegeerde toegang tot resources in uw Azure-opslag account.

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Uw SAS-tokens maken met Azure Storage Explorer

### <a name="prerequisites"></a>Vereisten

* U hebt een [**Azure Storage Explorer**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) -app nodig die is geïnstalleerd in uw Windows-, macOS-of Linux-ontwikkel omgeving. Azure Storage Explorer is een gratis hulp programma waarmee u eenvoudig uw Azure Cloud Storage-resources kunt beheren.
* Nadat de Azure Storage Explorer-app is geïnstalleerd, [verbindt u deze met het opslag account](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service) dat u gebruikt voor document vertalingen.

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
> Het maken van SAS-tokens voor containers rechtstreeks in het Azure Portal wordt momenteel niet ondersteund. U kunt echter een SAS-token met [**Azure Storage Explorer**](#create-your-sas-tokens-with-azure-storage-explorer) maken of de taak [via een programma](../../../storage/blobs/sas-service-create.md)volt ooien.

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een actief [**Azure-account**](https://azure.microsoft.com/free/cognitive-services/).  Als u er nog geen hebt, kunt u [**een gratis account maken**](https://azure.microsoft.com/free/).
* Een [**Translator**](https://ms.portal.azure.com/#create/Microsoft) -service resource (**niet** een Cognitive Services meerdere service bronnen.  *Zie* [een nieuwe Azure-resource maken](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource).  
* Een [**Azure Blob-opslag account**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). U maakt containers om uw BLOB-gegevens binnen uw opslag account op te slaan en te organiseren.

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

* [SAS-tokens maken voor blobs of containers via een programma](../../../storage/blobs/sas-service-create.md)
* [Machtigingen voor een map, container of BLOB](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met document vertalingen](get-started-with-document-translation.md)
>
>
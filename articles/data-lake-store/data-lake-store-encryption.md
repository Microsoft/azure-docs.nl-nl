---
title: Versleuteling in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Versleuteling in Azure Data Lake Storage Gen1 helpt u bij het beveiligen van uw gegevens, het implementeren van ENTER prise Security-beleid en voldoen aan nalevings vereisten. In dit artikel vindt u een overzicht van het ontwerp en worden een aantal technische aspecten van de implementatie besproken.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: 51c9f924c6fe3ac6db86d60e26749d35bc4ab733
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108941"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Versleuteling van gegevens in Azure Data Lake Storage Gen1

Versleuteling in Azure Data Lake Storage Gen1 helpt u bij het beveiligen van uw gegevens, het implementeren van ENTER prise Security-beleid en voldoen aan nalevings vereisten. In dit artikel vindt u een overzicht van het ontwerp en worden een aantal technische aspecten van de implementatie besproken.

Data Lake Storage Gen1 ondersteunt versleuteling van gegevens in rust en tijdens de overdracht. Voor Data-at-rest wordt met Data Lake Storage Gen1 de transparante versleuteling ' op standaard ' ondersteund. Hier wordt iets gedetailleerder uitgelegd wat deze termen betekenen:

* **Standaard ingeschakeld**: wanneer u een nieuw data Lake Storage gen1-account maakt, wordt versleuteling door de standaard instelling ingeschakeld. Daarna worden gegevens die zijn opgeslagen in Data Lake Storage Gen1 altijd versleuteld voordat ze worden opgeslagen op de permanente media. Dit is het gedrag voor alle gegevens en kan niet worden gewijzigd nadat u een account hebt gemaakt.
* **Transparant**: data Lake Storage gen1 gegevens worden automatisch versleuteld voordat ze persistent worden gemaakt en gegevens worden ontsleuteld voordat ze worden opgehaald. De versleuteling wordt door een beheerder geconfigureerd en beheerd op het niveau van Data Lake Storage Gen1-account. Er worden geen wijzigingen aangebracht aan de API's voor gegevenstoegang. Er zijn dus geen wijzigingen vereist in toepassingen en services die communiceren met Data Lake Storage Gen1 vanwege versleuteling.

Gegevens in transit (ook wel gegevens in beweging genoemd) worden ook altijd versleuteld in Data Lake Storage Gen1. Naast het versleutelen van gegevens voorafgaand aan het opslaan op permanente media, worden de gegevens tijdens overdracht ook altijd beveiligd met behulp van HTTPS. HTTPS is het enige protocol dat wordt ondersteund voor de Data Lake Storage Gen1 REST-interfaces. In het volgende diagram ziet u hoe gegevens versleuteld worden in Data Lake Storage Gen1:

![Diagram van gegevens versleuteling in Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Versleuteling instellen met Data Lake Storage Gen1

Versleuteling voor Data Lake Storage Gen1 wordt ingesteld tijdens het maken van het account en is standaard altijd ingeschakeld. U kunt de sleutels zelf beheren of toestaan dat Data Lake Storage Gen1 deze voor u beheert (dit is de standaard instelling).

Zie de [Introductie](./data-lake-store-get-started-portal.md) voor meer informatie.

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Hoe versleuteling werkt in Data Lake Storage Gen1

De volgende informatie is van toepassing op het beheren van hoofd versleutelings sleutels en bevat uitleg over de drie verschillende typen sleutels die u kunt gebruiken in gegevens versleuteling voor Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Hoofdversleutelingssleutels

Data Lake Storage Gen1 biedt twee modi voor het beheer van hoofd versleutelings sleutels (Mek's). Op dit moment wordt ervan uitgegaan dat de hoofdversleutelingssleutel de sleutel op het hoogste niveau is. Toegang tot de hoofd versleutelings sleutel is vereist voor het ontsleutelen van gegevens die zijn opgeslagen in Data Lake Storage Gen1.

De twee modi voor het beheer van de hoofdversleutelingssleutel zijn als volgt:

*   Door service beheerde sleutels
*   Door klant beheerde sleutels

In beide modi wordt de hoofdversleutelingssleutel beveiligd door opslag in Azure Key Vault. Key Vault is een volledig beheerde, zeer veilige service in Azure die kan worden gebruikt ter bescherming van de cryptografische sleutels. Bekijk [Key Vault](https://azure.microsoft.com/services/key-vault) voor meer informatie.

Hier volgt een korte vergelijking van de mogelijkheden van de twee modi voor het beheren van de MEK's.

| Vraag | Door service beheerde sleutels | Door klant beheerde sleutels |
| -------- | -------------------- | --------------------- |
|Hoe worden gegevens opgeslagen?|Altijd versleuteld voordat deze worden opgeslagen.|Altijd versleuteld voordat deze worden opgeslagen.|
|Waar wordt de hoofdversleutelingssleutel opgeslagen?|Key Vault|Key Vault|
|Worden versleutelingssleutels opgeslagen buiten Key Vault? |Nee|Nee|
|Kan de MEK worden opgehaald uit Key Vault?|Nee. Eenmaal opgeslagen in de Key Vault kan de MEK alleen worden gebruikt voor versleuteling en ontsleuteling.|Nee. Eenmaal opgeslagen in de Key Vault kan de MEK alleen worden gebruikt voor versleuteling en ontsleuteling.|
|Wie is eigenaar van de Key Vault-instantie en de MEK?|De Data Lake Storage Gen1-service|U bent eigenaar van de Key Vault-instantie in uw eigen Azure-abonnement. De MEK in de Key Vault kan worden beheerd door software of hardware.|
|Kunt u de toegang tot de MEK voor de Data Lake Storage Gen1-service intrekken?|Nee|Ja. U kunt toegangs beheer lijsten beheren in Key Vault en toegangs beheer vermeldingen verwijderen voor de service-identiteit voor de Data Lake Storage Gen1-service.|
|Kunt u de MEK permanent verwijderen?|Nee|Ja. Als u de MEK van Key Vault verwijdert, kunnen de gegevens in het Data Lake Storage Gen1-account niet door iedereen worden ontsleuteld, met inbegrip van de Data Lake Storage Gen1-service. <br><br> Als u een expliciete back-up van de MEK maakt voordat u deze uit de Key Vault verwijdert, kan deze worden hersteld en kunnen de gegevens vervolgens worden hersteld. Als u echter geen back-up hebt gemaakt van de MEK voordat u deze verwijdert uit Key Vault, kunnen de gegevens in het Data Lake Storage Gen1 account later nooit worden ontsleuteld.|


Afgezien van het verschil wat betreft wie de MEK en de Key Vault-instantie waarin deze zich bevindt beheert, is de rest van het ontwerp hetzelfde voor beide modi.

Het is belangrijk het volgende te onthouden wanneer u de modus voor de hoofdversleutelingssleutels kiest:

*   U kunt kiezen of u door de klant beheerde sleutels of door de service beheerde sleutels wilt gebruiken wanneer u een Data Lake Storage Gen1-account inricht.
*   Nadat een Data Lake Storage Gen1 account is ingericht, kan de modus niet meer worden gewijzigd.

### <a name="encryption-and-decryption-of-data"></a>Versleuteling en ontsleuteling van gegevens

Er zijn drie soorten sleutels die worden gebruikt in het ontwerp van gegevensversleuteling. De volgende tabel geeft een samenvatting:

| Sleutel                   | Afkorting | Gekoppeld aan | Opslaglocatie                             | Type       | Opmerkingen                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Hoofdversleutelingssleutel | MEK          | Een Data Lake Storage Gen1-account | Key Vault                              | Asymmetrisch | Het kan worden beheerd door Data Lake Storage Gen1 of u.                                                              |
| Gegevensversleutelingssleutel   | DEK          | Een Data Lake Storage Gen1-account | Permanente opslag, beheerd door de Data Lake Storage Gen1-service | Symmetrisch  | De DEK is versleuteld door de MEK. De versleutelde DEK is hetgeen wat wordt opgeslagen op persistente media. |
| Blokversleutelingssleutel  | BEK          | Een gegevensblok | Geen                                         | Symmetrisch  | De BEK wordt afgeleid van de DEK en het gegevensblok.                                                      |

Het volgende diagram illustreert deze concepten:

![Sleutels bij gegevensversleuteling](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritme wanneer een bestand moet worden ontsleuteld:
1.  Controleer of de DEK voor het Data Lake Storage Gen1-account in de cache is opgeslagen en gereed is voor gebruik.
    - Als dat niet het geval is, leest u de versleutelde DEK uit de permanente opslag en stuurt u deze naar de Key Vault voor ontsleuteling. Sla de ontsleutelde DEK op in het cachegeheugen. Deze is nu gereed voor gebruik.
2.  Voor elk gegevensblok in het bestand:
    - Lees het versleutelde gegevensblok uit de permanente opslag.
    - Genereer de BEK uit de DEK en het versleutelde gegevensblok.
    - Gebruik de BEK om gegevens te ontsleutelen.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritme wanneer een gegevensblok moeten worden versleuteld:
1.  Controleer of de DEK voor het Data Lake Storage Gen1-account in de cache is opgeslagen en gereed is voor gebruik.
    - Als dat niet het geval is, leest u de versleutelde DEK uit de permanente opslag en stuurt u deze naar de Key Vault voor ontsleuteling. Sla de ontsleutelde DEK op in het cachegeheugen. Deze is nu gereed voor gebruik.
2.  Genereer een unieke BEK voor het gegevensblok uit de DEK.
3.  Versleutel het gegevensblok met de BEK met behulp van AES-256-codering.
4.  Sla het versleutelde gegevensblok op in permanente opslag.

> [!NOTE] 
> De DEK wordt altijd versleuteld door de MEK opgeslagen, op permanente media of in het cachegeheugen.

## <a name="key-rotation"></a>Sleutelroulatie

Wanneer u door de klant beheerde sleutels gebruikt, kunt u de MEK rouleren. [Zie aan de slag voor](./data-lake-store-get-started-portal.md)meer informatie over het instellen van een Data Lake Storage gen1-account met door de klant beheerde sleutels.

### <a name="prerequisites"></a>Vereisten

Wanneer u het Data Lake Storage Gen1-account instelt, hebt u ervoor gekozen om uw eigen sleutels te gebruiken. Deze optie kan niet worden gewijzigd nadat het account is gemaakt. In de volgende stappen wordt ervan uitgegaan dat u door de klant beheerde sleutels gebruikt (u hebt uw eigen sleutels uit Key Vault gekozen).

Houd er rekening mee dat als u de standaard opties voor versleuteling gebruikt, uw gegevens altijd worden versleuteld met sleutels die worden beheerd door Data Lake Storage Gen1. In deze optie is het niet mogelijk om sleutels te draaien, omdat deze door Data Lake Storage Gen1 worden beheerd.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>De MEK draaien in Data Lake Storage Gen1

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Blader naar het Key Vault-exemplaar waarin uw sleutels zijn opgeslagen die zijn gekoppeld aan uw Data Lake Storage Gen1-account. Selecteer **Sleutels**.

    ![Schermafdruk van Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Selecteer de sleutel die is gekoppeld aan uw Data Lake Storage Gen1-account en maak een nieuwe versie van deze sleutel. Houd er rekening mee dat Data Lake Storage Gen1 momenteel alleen ondersteuning biedt voor het draaien van sleutels naar een nieuwe versie van een sleutel. Roulatie naar een andere sleutel wordt niet ondersteund.

   ![Schermafdruk van het venster Sleutels met de nieuwe versie gemarkeerd](./media/data-lake-store-encryption/keynewversion.png)

4. Blader naar het Data Lake Storage Gen1-account en selecteer **versleuteling**.

   ![Scherm opname van het venster Data Lake Storage Gen1 account, met de code ring gemarkeerd](./media/data-lake-store-encryption/select-encryption.png)

5. Er wordt een bericht weergegeven dat een nieuwe sleutelversie van de sleutel beschikbaar is. Klik op **Sleutel rouleren** om de sleutel naar de nieuwe versie bij te werken.

   ![Scherm afbeelding van Data Lake Storage Gen1 venster met bericht en toets draaien gemarkeerd](./media/data-lake-store-encryption/rotatekey.png)

Deze bewerking duurt minder dan twee minuten en er is geen verwachte uitvaltijd vanwege het rouleren van de sleutel. Nadat de bewerking is voltooid, wordt de nieuwe versie van de sleutel gebruikt.

> [!IMPORTANT]
> Nadat de bewerking voor het wijzigen van de sleutel is voltooid, wordt de oude versie van de sleutel niet meer actief gebruikt voor het versleutelen van nieuwe gegevens. Het kan echter voor komen dat voor het openen van oudere gegevens mogelijk de oude sleutel is vereist. Als u dergelijke oudere gegevens wilt lezen, moet u de oude sleutel niet verwijderen
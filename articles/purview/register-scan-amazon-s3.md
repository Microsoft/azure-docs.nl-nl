---
title: Amazon S3-buckets scannen
description: In deze hand leiding vindt u informatie over het scannen van Amazon S3-buckets.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/19/2021
ms.custom: references_regions
ms.openlocfilehash: 7d3fd0b1ffb87a84772000702b958c52ed1cc47c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679899"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Azure controle sfeer liggen-connector voor Amazon S3

Deze hand leiding bevat uitleg over het gebruik van Azure controle sfeer liggen voor het scannen van uw ongestructureerde gegevens die momenteel zijn opgeslagen in de standaard buckets van Amazon S3 en detecteert welke soorten gevoelige informatie in uw gegevens voor komt. In deze hand leiding wordt ook beschreven hoe u de Amazon S3-buckets kunt identificeren waarbij de gegevens op dit moment worden opgeslagen voor eenvoudige informatie bescherming en gegevens naleving.

Voor deze service gebruikt u controle sfeer liggen om een Microsoft-account te bieden met beveiligde toegang tot AWS, waarbij de controle sfeer liggen-scanner wordt uitgevoerd. De controle sfeer liggen-scanner gebruikt deze toegang tot uw Amazon S3-buckets om uw gegevens te lezen en vervolgens rapporteert de scan resultaten, met inbegrip van de meta gegevens en de classificatie, terug naar Azure. Gebruik de controle sfeer liggen-classificatie en label rapporten om de resultaten van uw gegevens scan te analyseren en te controleren.

In deze hand leiding vindt u informatie over het toevoegen van Amazon S3-buckets als controle sfeer liggen-resources en het maken van een scan voor uw Amazon S3-gegevens.

## <a name="purview-scope-for-amazon-s3"></a>Controle sfeer liggen-bereik voor Amazon S3

Het volgende bereik is specifiek voor het registreren en scannen van Amazon S3-buckets als controle sfeer liggen-gegevens bronnen.

|Bereik  |Beschrijving  |
|---------|---------|
|**Gegevenslimieten**     |    De controle sfeer liggen-scanner service biedt momenteel ondersteuning voor het scannen van Amazon S3-buckets voor Maxi maal 100 GB aan gegevens per Tenant.     |
|**Bestands typen**     | De controle sfeer liggen-scanner service ondersteunt momenteel de volgende bestands typen: <br><br>. AVRO,. CSV,. doc,. DOCM,. docx,. dot,. json,. ODP,. ODS,. ODT,. Orc,. Parquet,. PDF,. pot,. PPS,. PPSX,. ppt,. PPTM,. PPTX,. PSV,. SSV,. tsv,. txt,. XLC,. xls,. xlsb,. xlsm,. xlsx,. xlt,. XML        |
|**Regio's**     | De controle sfeer liggen-connector voor de Amazon S3-service wordt momenteel alleen geïmplementeerd in de regio's **AWS VS-Oost (Ohio)** en **Europa (Frankfurt)** . <br><br>Zie [opslag-en scan regio's](#storage-and-scanning-regions)voor meer informatie.   |
|     |         |

Voor meer informatie raadpleegt u de gedocumenteerde controle sfeer liggen-limieten op:

- [Quota's voor resources beheren en verg Roten met Azure controle sfeer liggen](how-to-manage-quotas.md)
- [Ondersteunde gegevens bronnen en bestands typen in azure controle sfeer liggen](sources-and-scans.md)
### <a name="storage-and-scanning-regions"></a>Opslag-en scan regio's

De volgende tabel bevat de regio's waar u gegevens opslaat in de regio waar deze zouden worden gescand door Azure controle sfeer liggen.

> [!IMPORTANT]
> Klanten worden in rekening gebracht voor alle gerelateerde kosten voor gegevens overdracht op basis van de regio van hun Bucket.
>

| Opslag regio | Scan regio |
| ------------------------------- | ------------------------------------- |
| VS Oost (Ohio)                  | VS Oost (Ohio)                        |
| VS Oost (N. Virginia           | VS Oost (Ohio)                        |
| VS West (N. Californië         | VS Oost (Ohio)                        |
| US - west (Oregon)                | VS Oost (Ohio)                        |
| Afrika (Kaap stad)              | Europa (Frankfurt)                    |
| Azië en Stille Oceaan (Hongkong)        | Europa (Frankfurt)                    |
| Azië en Stille Oceaan (Mumbai)           | Europa (Frankfurt)                    |
| Azië en Stille Oceaan (Osaka-lokaal)      | Europa (Frankfurt)                    |
| Azië en Stille Oceaan (Seoul)            | Europa (Frankfurt)                    |
| Azië en Stille Oceaan (Singapore)        | Europa (Frankfurt)                    |
| Azië en Stille Oceaan (Sydney)           | Europa (Frankfurt)                    |
| Azië en Stille Oceaan (Tokio)            | Europa (Frankfurt)                    |
| Canada (centraal)                | VS Oost (Ohio)                        |
| China (Peking)                 | Europa (Frankfurt)                    |
| China (Ningxia)                 | Europa (Frankfurt)                    |
| Europa (Frankfurt)              | Europa (Frankfurt)                    |
| Europa (Ierland)                | Europa (Frankfurt)                    |
| Europa (Londen)                 | Europa (Frankfurt)                    |
| Europa (Milaan)                  | Europa (Frankfurt)                    |
| Europa (Parijs)                  | Europa (Frankfurt)                    |
| Europa (Stockholm)              | Europa (Frankfurt)                    |
| Midden-Oosten (Bahrein)           | Europa (Frankfurt)                    |
| Zuid-Amerika (Sao Paulo)       | VS Oost (Ohio)                        |
| | |
## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende vereisten hebt uitgevoerd voordat u uw Amazon S3-buckets als controle sfeer liggen-gegevens bronnen toevoegt en uw S3-gegevens scant.

- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn.

- Wanneer u uw buckets als controle sfeer liggen-resources toevoegt, hebt u de waarden van uw [AWS Arn](#retrieve-your-new-role-arn), [Bucket naam](#retrieve-your-amazon-s3-bucket-name)en soms uw [AWS-account-id](#locate-your-aws-account-id)nodig.

### <a name="create-a-purview-account"></a>Een controle sfeer liggen-account maken

- **Als u al een controle sfeer liggen-account hebt,** kunt u door gaan met de configuraties die vereist zijn voor de ondersteuning van AWS S3. Begin met het [maken van een controle sfeer liggen-referentie voor uw AWS Bucket-scan](#create-a-purview-credential-for-your-aws-bucket-scan).

- **Als u een controle sfeer liggen-account moet maken, volgt u** de instructies in [een Azure controle sfeer liggen-account exemplaar maken](create-catalog-portal.md). Nadat u uw account hebt gemaakt, keert u terug om de configuratie te volt ooien en gaat u controle sfeer liggen connector gebruiken voor Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Een controle sfeer liggen-referentie maken voor uw AWS Bucket-scan

In deze procedure wordt beschreven hoe u een nieuwe controle sfeer liggen-referentie maakt die u kunt gebruiken bij het scannen van uw AWS-buckets.

> [!TIP]
> U kunt ook in het midden van het proces een nieuwe referentie maken en [de scan configureren](#create-a-scan-for-your-amazon-s3-bucket). In dat geval selecteert u in het veld **referentie** de optie **Nieuw**.
>

1. Ga in controle sfeer liggen naar het **Management Center** en selecteer onder **beveiliging en toegang** de optie **referenties**.

1. Selecteer **Nieuw** en gebruik in het deel venster **nieuwe referentie** aan de rechter kant de volgende velden om uw controle sfeer liggen-referentie te maken:

    |Veld |Beschrijving  |
    |---------|---------|
    |**Naam**     |Voer een beschrijvende naam in voor deze referentie of gebruik de standaard waarde.        |
    |**Beschrijving**     |Voer een optionele beschrijving in voor deze referentie, zoals `Used to scan the tutorial S3 buckets`         |
    |**Verificatiemethode**     |Selecteer **Role Arn**, omdat u een rol Arn gebruikt om toegang te krijgen tot uw Bucket.         |
    |**Microsoft-account-ID**     |Klik om deze waarde naar het klem bord te kopiëren. Gebruik deze waarde als de **Microsoft-account-id** bij het [maken van uw rol ARN in AWS](#create-a-new-aws-role-for-purview).           |
    |**Externe ID**     |Klik om deze waarde naar het klem bord te kopiëren. Gebruik deze waarde als de **externe ID** bij [het maken van uw rol ARN in AWS.](#create-a-new-aws-role-for-purview)        |
    |**ARN rol**     | Wanneer u [uw Amazon iam-rol hebt gemaakt](#create-a-new-aws-role-for-purview), navigeert u naar uw rol in het IAM-gebied, kopieert u de waarde van de **rol Arn** en voert u deze hier in. Bijvoorbeeld: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Zie [uw nieuwe rol Arn ophalen](#retrieve-your-new-role-arn)voor meer informatie. |
    | | |

    Selecteer **maken** als u klaar bent om de referentie te maken.

Zie de [documentatie van Azure controle sfeer liggen Public Preview](manage-credentials.md)voor meer informatie over controle sfeer liggen-referenties.

### <a name="create-a-new-aws-role-for-purview"></a>Een nieuwe AWS-rol maken voor controle sfeer liggen

1.  Open uw **Amazon Web Services** -console en selecteer onder **beveiliging, identiteit en naleving** de optie **iam**.

1. Selecteer **rollen** en vervolgens **rol maken**.

1. Selecteer **een ander AWS-account** en voer de volgende waarden in:

    |Veld  |Beschrijving  |
    |---------|---------|
    |**Account-id**     |    Voer uw micro soft-account-ID in. Bijvoorbeeld: `615019938638`     |
    |**Externe ID**     |   Onder Opties selecteert u **externe ID vereisen...** en voert u vervolgens uw externe ID in het desbetreffende veld in. <br>Bijvoorbeeld: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>U kunt deze externe ID vinden als u.  |
    | | |

    > [!NOTE]
    > U kunt de waarden voor de **micro soft-account-id** en de **externe ID** vinden in het controle sfeer liggen van het **beheer centrum**  >   , waar u [uw controle sfeer liggen-referenties hebt gemaakt](#create-a-purview-credential-for-your-aws-bucket-scan).
    >

    Bijvoorbeeld:

    ![Voeg de ID van het micro soft-account toe aan uw AWS-account.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. In het gebied **rol maken > machtigingen beleid koppelen** , filtert u de machtigingen die worden weer gegeven voor **S3**. Selecteer **AmazonS3ReadOnlyAccess** en selecteer vervolgens **volgende: Tags**.

    ![Selecteer het ReadOnlyAccess-beleid voor de nieuwe rol Amazon S3-scan.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

1. In het gebied **labels toevoegen (optioneel)** kunt u desgewenst een zinvolle tag maken voor deze nieuwe rol. Met handige Tags kunt u de toegang indelen, bijhouden en beheren voor elke rol die u maakt.

    Voer indien nodig een nieuwe sleutel en waarde voor de tag in. Als u klaar bent, of als u deze stap wilt overs Laan, selecteert u **volgende: controleren** om de functie gegevens te controleren en de functie maken te volt ooien.

    ![Voeg een zinvolle tag toe om de toegang voor uw nieuwe rol in te delen, bij te houden of te beheren.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. Ga als volgt te werk in het **beoordelings** gebied:

    - Voer in het veld **rolnaam** een duidelijke naam in voor uw rol
    - Voer in het vak **Beschrijving van rol** een optionele beschrijving in om het doel van de functie te identificeren
    - Controleer in de sectie **beleid** of het juiste beleid (**AmazonS3ReadOnlyAccess**) aan de rol is gekoppeld.

    Selecteer vervolgens **rol maken** om het proces te volt ooien.

    Bijvoorbeeld:

    ![Bekijk de details voordat u uw rol maakt.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Scans voor versleutelde Amazon S3-buckets configureren

AWS buckets ondersteunen meerdere versleutelings typen. Voor buckets die gebruikmaken van **AWS-KMS-** code ring is speciale configuratie vereist om het scannen mogelijk te maken.

> [!NOTE]
> Voor buckets die geen versleuteling, AES-256 of AWS-KMS S3-versleuteling gebruiken, kunt u deze sectie overs Laan en door gaan met [het ophalen van de naam van uw Amazon S3-Bucket](#retrieve-your-amazon-s3-bucket-name).
>

**Controleren welk type versleuteling wordt gebruikt in uw Amazon S3-buckets:**

1. Ga in AWS naar **Storage**  >  **S3** > en selecteer **buckets** in het menu aan de linkerkant.

    ![Selecteer het tabblad Amazon S3-buckets.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Selecteer de Bucket die u wilt controleren. Selecteer op de pagina Details van Bucket het tabblad **Eigenschappen** en schuif omlaag naar het gebied **standaard versleuteling** .

    - Als de Bucket die u hebt geselecteerd voor een wille keurige, maar **AWS-KMS-** code ring is geconfigureerd, bijvoorbeeld als de standaard versleuteling voor uw Bucket is **uitgeschakeld**, slaat u de rest van deze procedure over en gaat u verder met [de naam van uw Amazon S3-Bucket](#retrieve-your-amazon-s3-bucket-name)

    - Als de Bucket die u hebt geselecteerd, is geconfigureerd voor **AWS-KMS-** versleuteling, gaat u verder met de onderstaande stappen om een nieuw beleid toe te voegen waarmee u een Bucket kunt scannen met aangepaste **AWS-KMS-** code ring.

    Bijvoorbeeld:

    ![Een Amazon S3-Bucket weer geven die is geconfigureerd met AWS-KMS-code ring](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Een nieuw beleid toevoegen om een Bucket met aangepaste AWS-KMS-code ring te kunnen scannen:**

1. Navigeer in AWS naar **Services**  >   **iam**  >   -**beleid** en selecteer **beleid maken**.

1. Definieer uw beleid op het tabblad **beleid maken** van de  >  **Visual Editor** met de volgende waarden:

    |Veld  |Beschrijving  |
    |---------|---------|
    |**Service**     |  Voer **KMS** in en selecteer deze.       |
    |**Acties**     | Onder **toegangs niveau**, selecteer **schrijven** om de sectie **schrijven** uit te vouwen.<br>Als u hebt uitgebreid, selecteert u alleen de optie **ontsleutelen** .        |
    |**Bronnen**     |Selecteer een specifieke resource of **alle resources**.         |
    | | |

    Wanneer u klaar bent, selecteert u **beleid controleren** om door te gaan.

    ![Een beleid maken voor het scannen van een Bucket met AWS-KMS-code ring.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Voer op de pagina **beleid controleren** een beschrijvende naam in voor uw beleid en een optionele beschrijving en selecteer vervolgens **beleid maken**.

    Het zojuist gemaakte beleid wordt toegevoegd aan uw lijst met beleids regels.

1. Koppel uw nieuwe beleid aan de rol die u hebt toegevoegd voor het scannen.

    1. Ga terug naar de pagina **iam**  >  -**rollen** en selecteer de rol die u [eerder](#create-a-new-aws-role-for-purview)hebt toegevoegd.

    1. Selecteer **beleid koppelen** op het tabblad **machtigingen** .

        ![Selecteer beleid koppelen op het tabblad Machtigingen van uw rol.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Op de pagina **machtigingen koppelen** zoekt en selecteert u het nieuwe beleid dat u hierboven hebt gemaakt. Selecteer **beleid koppelen** om uw beleid aan de rol toe te voegen.

        De **overzichts** pagina wordt bijgewerkt, waarbij uw nieuwe beleid aan uw rol is gekoppeld.

        ![Bekijk een bijgewerkte overzichts pagina met het nieuwe beleid dat aan uw rol is gekoppeld.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>De nieuwe functie ARN ophalen

U moet de ARN van uw AWS-functie vastleggen en deze naar controle sfeer liggen kopiëren bij het [maken van een scan voor uw Amazon S3-Bucket](#create-a-scan-for-your-amazon-s3-bucket).

**Uw rol ophalen ARN:**

1. Zoek en selecteer in het gebied rollen voor AWS **Identity and Access Management (IAM)**  >   de nieuwe rol die u hebt [gemaakt voor controle sfeer liggen](#create-a-purview-credential-for-your-aws-bucket-scan).

1. Selecteer op de pagina **overzicht** van de functie de knop **kopiëren naar klem bord** rechts van de waarde van de **rol Arn** .

    ![Kopieer de waarde van de rol ARN naar het klem bord.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Plak deze waarde op een veilige locatie, die u kunt gebruiken bij het [maken van een scan voor uw Amazon S3-Bucket](#create-a-scan-for-your-amazon-s3-bucket).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>De naam van de Amazon S3-Bucket ophalen

U hebt de naam van uw Amazon S3-Bucket nodig om deze naar controle sfeer liggen te kopiëren bij het [maken van een scan voor uw Amazon S3-Bucket](#create-a-scan-for-your-amazon-s3-bucket)

**De Bucket-naam ophalen:**

1. Ga in AWS naar **Storage**  >  **S3** > en selecteer **buckets** in het menu aan de linkerkant.

    ![Bekijk het tabblad Amazon S3-buckets.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Zoek en selecteer de Bucket om de pagina Details van de Bucket weer te geven en kopieer de Bucket naam vervolgens naar het klem bord.

    Bijvoorbeeld:

    ![De URL van de S3 Bucket ophalen en kopiëren.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Plak de Bucket naam in een beveiligd bestand en voeg er een `s3://` voor voegsel aan toe om de waarde te maken die u moet invoeren bij het configureren van de Bucket als een controle sfeer liggen-resource.

    Bijvoorbeeld: `s3://purview-tutorial-bucket`

> [!NOTE]
> Alleen het hoofd niveau van de Bucket wordt ondersteund als een controle sfeer liggen-gegevens bron. De volgende URL, die bijvoorbeeld een submap bevat, wordt *niet* ondersteund: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Zoek uw AWS-account-ID

U hebt uw AWS-account-ID nodig om uw AWS-account te registreren als een controle sfeer liggen-gegevens bron, samen met alle buckets.

Uw AWS-account-ID is de ID die u gebruikt om u aan te melden bij de AWS-console. U kunt deze ook vinden zodra u bent aangemeld bij het IAM-dash board, aan de linkerkant onder de navigatie opties en bovenaan, als het numerieke deel van uw aanmeldings-URL:

Bijvoorbeeld:

![Haal de AWS-account-ID op.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Eén Amazon S3-Bucket toevoegen als een controle sfeer liggen-resource

Gebruik deze procedure als u slechts één S3-Bucket hebt die u wilt registreren bij controle sfeer liggen als gegevens bron, of als u meerdere buckets hebt in uw AWS-account, maar niet al deze wilt registreren bij controle sfeer liggen.

1. Start de controle sfeer liggen-Portal met de speciale controle sfeer liggen-connector voor de Amazon S3-URL. Deze URL is door gegeven aan het team van de Amazon S3 controle sfeer liggen-connector.

    ![Start de controle sfeer liggen-Portal.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Ga naar de pagina Azure controle sfeer liggen **Sources** en selecteer registreren pictogram **registreren** ![ .](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Door gaan**.

    ![Voeg een Amazon AWS-Bucket toe als een controle sfeer liggen-gegevens bron.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Als u meerdere [verzamelingen](manage-data-sources.md#manage-collections) hebt en uw Amazon S3 wilt toevoegen aan een specifieke verzameling, selecteert u in de rechter bovenhoek de **kaart weergave** en selecteert u vervolgens het pictogram **registreren** registreren ![ .](./media/register-scan-amazon-s3/register-button.png) in uw verzameling.
    >

1. In het deel venster **register bronnen (Amazon S3)** dat wordt geopend, voert u de volgende gegevens in:

    |Veld  |Beschrijving  |
    |---------|---------|
    |**Naam**     |Voer een beschrijvende naam in of gebruik de standaard waarde.         |
    |**Bucket-URL**     | Voer de URL van uw AWS Bucket in met behulp van de volgende syntaxis:   `s3://<bucketName>`     <br><br>**Opmerking**: Zorg ervoor dat u alleen het hoofd niveau van de Bucket, zonder submappen, gebruikt. Zie [de naam van uw Amazon S3-Bucket ophalen](#retrieve-your-amazon-s3-bucket-name)voor meer informatie. |
    |**Een verzameling selecteren** |Als u hebt opgegeven dat u een gegevens bron wilt registreren in een verzameling, wordt die verzameling al vermeld. <br><br>Selecteer een andere verzameling als dat nodig **is, geen om geen** verzameling toe te wijzen of **Nieuw** om nu een nieuwe verzameling te maken. <br><br>Zie voor meer informatie over controle sfeer liggen-verzamelingen [gegevens bronnen beheren in azure controle sfeer liggen](manage-data-sources.md#manage-collections).|
    | | |

    Wanneer u klaar bent, selecteert u **volt ooien** om de registratie te volt ooien.

Ga door met het [maken van een scan voor uw Amazon S3-Bucket.](#create-a-scan-for-your-amazon-s3-bucket)..

## <a name="add-all-of-your-amazon-s3-buckets-as-purview-resources"></a>Voeg al uw Amazon S3-buckets toe als controle sfeer liggen-resources

Gebruik deze procedure als u meerdere S3-buckets hebt in uw Amazon-account en u alle controle sfeer liggen-gegevens bronnen wilt registreren.

1. Start de controle sfeer liggen-Portal met de speciale controle sfeer liggen-connector voor de Amazon S3-URL. Deze URL is door gegeven aan het team van de Amazon S3 controle sfeer liggen-connector.

    ![Connector starten voor Amazon S3 dedicated controle sfeer liggen-Portal](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Ga naar de pagina Azure controle sfeer liggen **Sources** en selecteer registreren pictogram **registreren** ![ .](./media/register-scan-amazon-s3/register-button.png) > **Amazon-accounts**  >  **Door gaan**.

    ![Voeg een Amazon-account toe als een controle sfeer liggen-gegevens bron.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Als u meerdere [verzamelingen](manage-data-sources.md#manage-collections) hebt en uw Amazon S3 wilt toevoegen aan een specifieke verzameling, selecteert u in de rechter bovenhoek de **kaart weergave** en selecteert u vervolgens het pictogram **registreren** registreren ![ .](./media/register-scan-amazon-s3/register-button.png) in uw verzameling.
    >

1. In het deel venster **register bronnen (Amazon S3)** dat wordt geopend, voert u de volgende gegevens in:

    |Veld  |Beschrijving  |
    |---------|---------|
    |**Naam**     |Voer een beschrijvende naam in of gebruik de standaard waarde.         |
    |**AWS-account-ID**     | Voer uw AWS-account-ID in. Zie [uw AWS-account-ID zoeken](#locate-your-aws-account-id) voor meer informatie.|
    |**Een verzameling selecteren** |Als u hebt opgegeven dat u een gegevens bron wilt registreren in een verzameling, wordt die verzameling al vermeld. <br><br>Selecteer een andere verzameling als dat nodig **is, geen om geen** verzameling toe te wijzen of **Nieuw** om nu een nieuwe verzameling te maken. <br><br>Zie voor meer informatie over controle sfeer liggen-verzamelingen [gegevens bronnen beheren in azure controle sfeer liggen](manage-data-sources.md#manage-collections).|
    | | |

    Wanneer u klaar bent, selecteert u **volt ooien** om de registratie te volt ooien.

Ga door met het [maken van een scan voor uw Amazon S3-Bucket](#create-a-scan-for-your-amazon-s3-bucket).

## <a name="create-a-scan-for-your-amazon-s3-bucket"></a>Een scan voor uw Amazon S3-Bucket maken

Wanneer u uw buckets hebt toegevoegd als controle sfeer liggen-gegevens bronnen, kunt u een scan configureren om op geplande intervallen of onmiddellijk te worden uitgevoerd.

1. Ga naar het gebied Azure controle sfeer liggen **Sources** en voer een van de volgende handelingen uit:

    - Selecteer in de **kaart weergave** **nieuwe** scan pictogram nieuwe scan ![ .](./media/register-scan-amazon-s3/new-scan-button.png) in het vak gegevens bron.
    - Beweeg de muis aanwijzer in de **lijst weergave** over de rij voor de gegevens bron en selecteer **nieuw pictogram scan** ![ Nieuw scannen. ](./media/register-scan-amazon-s3/new-scan-button.png) ..

1. In het deel venster **scannen...** dat aan de rechter kant wordt geopend, definieert u de volgende velden en selecteert u **door gaan**:

    |Veld  |Beschrijving  |
    |---------|---------|
    |**Naam**     |  Voer een beschrijvende naam in voor de scan of gebruik de standaard waarde.       |
    |**Type** |Wordt alleen weer gegeven als u uw AWS-account hebt toegevoegd, waarbij alle buckets zijn opgenomen. <br><br>De huidige opties omvatten alleen **alle**  >  **Amazon S3**. Blijf op de hoogte als u meer opties wilt selecteren om de ondersteunings matrix van controle sfeer liggen uit te vouwen. |
    |**Referentie**     |  Selecteer een controle sfeer liggen-referentie met uw rol ARN. <br><br>**Tip**: als u op dit moment een nieuwe referentie wilt maken, selecteert u **Nieuw**. Zie [een controle sfeer liggen-referentie maken voor uw AWS Bucket-scan](#create-a-purview-credential-for-your-aws-bucket-scan)voor meer informatie.     |
    |     |         |

    Controle sfeer liggen controleert automatisch of de ARN van de rol geldig is en of de Bucket en het object in de Bucket toegankelijk zijn, en gaat vervolgens verder als de verbinding slaagt.

    > [!TIP]
    > Als u andere waarden wilt opgeven en de verbinding zelf wilt testen voordat u doorgaat, selecteert u **verbinding testen** onder aan de rechter kant voordat u **door gaan** selecteert.
    >

1. Selecteer in het deel venster **een regel voor de scanset selecteren** de standaard regelset **AmazonS3** of selecteer een **nieuwe set scan Rule** om een nieuwe aangepaste regelset te maken. Wanneer u de regelset hebt geselecteerd, selecteert u **door gaan**.

    Als u een nieuwe set met aangepaste scan regels wilt maken, gebruikt u de wizard om de volgende instellingen te definiëren:

    |Deelvenster  |Beschrijving  |
    |---------|---------|
    |**Nieuwe regelset voor scan** /<br>**Beschrijving van de scan regel**    |   Voer een duidelijke naam en een optionele beschrijving in voor de regelset      |
    |**Bestands typen selecteren**     | Selecteer alle bestands typen die u in de scan wilt gebruiken en selecteer vervolgens **door gaan**.<br><br>Als u een nieuw bestands type wilt toevoegen, selecteert u **Nieuw bestands type** en definieert u het volgende: <br>-De bestands extensie die u wilt toevoegen <br>-Een optionele beschrijving  <br>-Of de bestands inhoud een aangepast scheidings teken of een systeem bestands type is. Voer vervolgens uw aangepaste scheidings teken in of selecteer het type van het systeem bestand. <br><br>Selecteer **maken** om uw aangepaste bestands type te maken.     |
    |**Classificatie regels selecteren**     |   Navigeer naar en selecteer de classificatie regels die u wilt uitvoeren op uw gegevensset.      |
    |     |         |

    Selecteer **maken** wanneer u klaar bent om de regelset te maken.

1. Selecteer een van de volgende opties in het deel venster **een scan trigger instellen** en selecteer vervolgens **door gaan**:

    - **Terugkerend** voor het configureren van een schema voor een terugkerende scan
    - **Eenmaal** om een scan te configureren die onmiddellijk wordt gestart

1. Controleer in het deel venster **Scan controleren** of de scan gegevens juist zijn en selecteer vervolgens **Opslaan** of **opslaan en uitvoeren** als u **één keer** hebt geselecteerd in het vorige deel venster.

    > [!NOTE]
    > Na het starten kan het tot 24 uur duren voordat de scan is voltooid. U kunt uw **inzicht rapporten** bekijken en de catalogus 24 uur na het starten van elke scan doorzoeken.
    >

Zie voor meer informatie [verkennen controle sfeer liggen scan resultaten](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>Scan resultaten van controle sfeer liggen verkennen

Zodra een controle sfeer liggen-scan is voltooid op uw Amazon S3-buckets, zoomt u in het gebied controle sfeer liggen- **bronnen** om de scan geschiedenis weer te geven.

Selecteer een gegevens bron om de details ervan weer te geven en selecteer vervolgens het tabblad **scans** om actieve of voltooide scans weer te geven.
Als u een AWS-account met meerdere buckets hebt toegevoegd, wordt de scan geschiedenis voor elke Bucket onder het account weer gegeven.

Bijvoorbeeld:

![De AWS S3-Bucket scans weer geven onder uw AWS-account bron.](./media/register-scan-amazon-s3/account-scan-history.png)

Gebruik de andere gebieden van controle sfeer liggen om informatie te krijgen over de inhoud van uw gegevens, inclusief uw Amazon S3-buckets:

- **Zoek in de controle sfeer liggen Data Catalog** en filter op een specifieke Bucket. Bijvoorbeeld:

    ![Zoek in de catalogus naar AWS S3-assets.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Bekijk inzicht rapporten** om statistieken weer te geven voor de classificatie, gevoeligheids labels, bestands typen en meer informatie over uw inhoud.

    Alle controle sfeer liggen Insight-rapporten bevatten de Amazon S3-scan resultaten, samen met de rest van de resultaten van uw Azure-gegevens bronnen. Als dat relevant is, is er een extra **Amazon S3** -Asset type toegevoegd aan de rapport filterings opties.

    Zie [inzichten begrijpen in azure controle sfeer liggen](concept-insights.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure controle sfeer liggen Insight-rapporten:

> [!div class="nextstepaction"]
> [Inzichten in Azure Purview begrijpen](concept-insights.md)

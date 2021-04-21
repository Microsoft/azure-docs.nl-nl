---
title: Amazon S3-buckets scannen
description: In deze handleiding wordt beschreven hoe u Amazon S3-buckets scant.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/21/2021
ms.custom: references_regions
ms.openlocfilehash: 75a7cba1e47509e3186ab519d0d8ca82dd315373
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815518"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Azure Purview-connector voor Amazon S3

Deze handleiding bevat een uitleg over het gebruik van Azure Purview om uw ongestructureerde gegevens te scannen die momenteel zijn opgeslagen in standaard-buckets van Amazon S3 en om te ontdekken welke typen gevoelige informatie er in uw gegevens bestaan. In deze handleiding wordt ook beschreven hoe u de Amazon S3-buckets identificeert waarin de gegevens momenteel zijn opgeslagen voor eenvoudige gegevensbeveiliging en gegevens compliance.

Voor deze service gebruikt u Purview om een Microsoft-account met beveiligde toegang tot AWS, waar de Purview-scanner wordt uitgevoerd. De Purview-scanner gebruikt deze toegang tot uw Amazon S3-buckets om uw gegevens te lezen en rapporteert vervolgens de scanresultaten, inclusief alleen de metagegevens en classificatie, terug naar Azure. Gebruik de purview-classificatie en het labelen van rapporten om de resultaten van uw gegevensscan te analyseren en te controleren.

In deze handleiding leert u hoe u Amazon S3-buckets als Purview-resources toevoegt en een scan maakt voor uw Amazon S3-gegevens.

## <a name="purview-scope-for-amazon-s3"></a>Bereik van de purview voor Amazon S3

Het volgende bereik is specifiek voor het registreren en scannen van Amazon S3-buckets als Purview-gegevensbronnen.

|Bereik  |Beschrijving  |
|---------|---------|
|**Gegevenslimieten**     |    De Purview-scannerservice ondersteunt momenteel het scannen van Amazon S3-buckets voor maximaal 100 GB aan gegevens per tenant.     |
|**Bestandstypen**     | De Purview-scannerservice ondersteunt momenteel de volgende bestandstypen: <br><br>.avro, .csv, .doc, .docm, .docx, .dot, .json, .odp, .ods, .odt, .orc, .parquet, .pdf, .pot, .pps, .ppsx, .ppt, .pptm, .pptx, .psv, .ssv, .tsv, .txt, .xlc, .xls, .xlsb, .xlsm, .xlsx, .xlt, .xlt, .xml        |
|**Regio's**     | De Purview-connector voor de Amazon S3-service is momenteel alleen geïmplementeerd in de regio's AWS US - oost **(Ohio)** en **Europa (Region).** <br><br>Zie Opslag- en [scanregio's voor meer informatie.](#storage-and-scanning-regions)   |
|     |         |

Zie voor meer informatie de gedocumenteerde limieten voor purview op:

- [Quota voor resources beheren en verhogen met Azure Purview](how-to-manage-quotas.md)
- [Ondersteunde gegevensbronnen en bestandstypen in Azure Purview](sources-and-scans.md)
- [Privé-eindpunten gebruiken voor uw Purview-account](catalog-private-link.md)
### <a name="storage-and-scanning-regions"></a>Opslag- en scanregio's

In de volgende tabel worden de regio's waar uw gegevens worden opgeslagen, weergegeven in de regio waar deze worden gescand door Azure Purview.

> [!IMPORTANT]
> Klanten worden in rekening gebracht voor alle gerelateerde kosten voor gegevensoverdracht, afhankelijk van de regio van hun bucket.
>

| Opslagregio | Scanregio |
| ------------------------------- | ------------------------------------- |
| US - oost (Ohio)                  | US - oost (Ohio)                        |
| US - oost (N. Virginia)           | US - oost (N. Virginia)                       |
| US - west (N. Californië)         | US - oost (Ohio)                        |
| US - west (Oregon)                | US - oost (Ohio)                        |
| Afrika (Cape Town)              | Europa (Parijs)                    |
| Azië en Stille Oceaan (Hongkong)        | Azië en Stille Oceaan (Sydney)                   |
| Azië en Stille Oceaan (Mumbai)           | Azië en Stille Oceaan (Sydney)                   |
| Azië en Stille Oceaan (Osaka-Local)      | Azië en Stille Oceaan (Sydney)                   |
| Azië en Stille Oceaan (Azië en Stille Oceaan)            | Azië en Stille Oceaan (Sydney)                   |
| Azië en Stille Oceaan (Singapore)        | Azië en Stille Oceaan (Sydney)                   |
| Azië en Stille Oceaan (Sydney)           | Azië en Stille Oceaan (Sydney)                  |
| Azië en Stille Oceaan (Tokio)            | Azië en Stille Oceaan (Sydney)                 |
| Canada (centraal)                | US - oost (Ohio)                        |
| China (China) (China)                 | Niet ondersteund                    |
| China (Ningxia)                 | Niet ondersteund                   |
| Europa (Parijs)              | Europa (Parijs)                    |
| Europa (Ierland)                | Europa (Ierland)                   |
| Europa (Londen)                 | Europa (Ierland)                   |
| Europa (Europe)                  | Europa (Parijs)                    |
| Europa (Parijs)                  | Europa (Parijs)                    |
| Europa (Maand)              | Europa (Parijs)                    |
| Midden-Oosten (Midden-Oosten)           | Europa (Parijs)                    |
| Zuid-Amerika (Sño Paulo)       | US - oost (Ohio)                        |
| | |

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u de volgende vereisten hebt uitgevoerd voordat u uw Amazon S3-buckets toevoegt als Gegevensbronnen opsviewen en uw S3-gegevens scant.

> [!div class="checklist"]
> * U moet een Azure Purview-gegevensbronbeheerder zijn.
> * [Maak een Purview-account](#create-a-purview-account) als u er nog geen hebt
> * [Een Purview-referentie maken voor uw AWS-bucketscan](#create-a-purview-credential-for-your-aws-bucket-scan)
> * [Een nieuwe AWS-rol maken voor gebruik met Purview](#create-a-new-aws-role-for-purview)
> * [Scannen configureren voor versleutelde Amazon S3-buckets,](#configure-scanning-for-encrypted-amazon-s3-buckets)indien relevant
> * Wanneer u uw buckets toevoegt als Resources opsviewen, hebt u de waarden van uw [AWS ARN,](#retrieve-your-new-role-arn) [bucketnaam](#retrieve-your-amazon-s3-bucket-name)en soms uw [AWS-account-id nodig.](#locate-your-aws-account-id)

### <a name="create-a-purview-account"></a>Een Purview-account maken

- **Als u al een Purview-account hebt,** kunt u doorgaan met de configuraties die vereist zijn voor AWS S3-ondersteuning. Begin met Create a Purview credential for your AWS bucket scan ( Een [Purview-referentie maken voor uw AWS-bucketscan).](#create-a-purview-credential-for-your-aws-bucket-scan)

- **Als u een Purview-account moet maken, volgt** u de instructies in [Een Azure Purview-accountinstructies maken.](create-catalog-portal.md) Nadat u uw account hebt gemaakt, keert u hier terug om de configuratie te voltooien en begint u met het gebruik van de Purview-connector voor Amazon S3.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Een Purview-referentie maken voor uw AWS-bucketscan

In deze procedure wordt beschreven hoe u een nieuwe Purview-referentie maakt om te gebruiken bij het scannen van uw AWS-buckets.

> [!TIP]
> U kunt ook een nieuwe referentie maken tijdens het configureren [van de scan.](#create-a-scan-for-one-or-more-amazon-s3-buckets) In dat geval selecteert u in **het veld Referentie** de optie **Nieuw.**
>

1. Navigeer in Purview naar **het Beheercentrum** en selecteer onder **Beveiliging en toegang** de optie **Referenties.**

1. Selecteer **Nieuw** en gebruik in het **deelvenster** Nieuwe referentie dat aan de rechterkant wordt weergegeven de volgende velden om uw purview-referentie te maken:

    |Veld |Beschrijving  |
    |---------|---------|
    |**Naam**     |Voer een betekenisvolle naam in voor deze referentie of gebruik de standaardwaarde.        |
    |**Beschrijving**     |Voer een optionele beschrijving in voor deze referentie, zoals `Used to scan the tutorial S3 buckets`         |
    |**Verificatiemethode**     |Selecteer **Role ARN,** omdat u een rol-ARN gebruikt om toegang te krijgen tot uw bucket.         |
    |**Microsoft-account-id**     |Klik om deze waarde naar het klembord te kopiëren. Gebruik deze waarde als de Microsoft-account **id bij** het maken van uw Role ARN [in AWS](#create-a-new-aws-role-for-purview).           |
    |**Externe id**     |Klik om deze waarde naar het klembord te kopiëren. Gebruik deze waarde als de externe **id bij het** maken van uw Role ARN in [AWS.](#create-a-new-aws-role-for-purview)        |
    |**Role ARN**     | Nadat u uw [Amazon IAM-rol](#create-a-new-aws-role-for-purview)hebt gemaakt, gaat u naar uw rol in het gebied IAM, kopieert u de waarde role **ARN** en voert u deze hier in. Bijvoorbeeld: `arn:aws:iam::284759281674:role/S3Role`. <br><br>Zie Retrieve [your new Role ARN (Uw nieuwe role ARN ophalen) voor meer informatie.](#retrieve-your-new-role-arn) |
    | | |

    Selecteer **Maken** wanneer u klaar bent met het maken van de referentie.

1. Als u dat nog niet hebt gedaan, kopieert en plakt u de waarden **Microsoft-account ID** en **External ID** voor gebruik bij het maken van een nieuwe AWS-rol voor [Purview.](#create-a-new-aws-role-for-purview)Dit is de volgende stap.

Zie Credentials for source authentication in Azure Purview (Referenties voor bronverificatie in Azure Purview) voor meer informatie [over purview-referenties.](manage-credentials.md)

### <a name="create-a-new-aws-role-for-purview"></a>Een nieuwe AWS-rol maken voor Purview

Voor deze procedure moet u de waarden voor uw Azure-account-id en externe id invoeren bij het maken van uw AWS-rol.

Als u deze waarden niet hebt, zoekt u deze eerst in uw [Purview-referentie](#create-a-purview-credential-for-your-aws-bucket-scan).

**Uw Microsoft-account-id en externe id zoeken:**

1. Navigeer in Purview naar **De beveiliging van het beheercentrum** en krijg toegang  >    >  **tot Referenties**.

1. Selecteer de referentie die u hebt [gemaakt voor uw AWS-bucketscan](#create-a-purview-credential-for-your-aws-bucket-scan)en selecteer bewerken op de **werkbalk.**

1. In het **deelvenster** Referentie bewerken dat aan de rechterkant wordt  weergegeven, kopieert u de **waarden van Microsoft-account ID** en Externe id naar een afzonderlijk bestand of hebt u deze bij de hand om ze in het relevante veld in AWS te kopiëren.

    Bijvoorbeeld:

    [![Zoek de waarden Microsoft-account id en externe id. ](./media/register-scan-amazon-s3/locate-account-id-external-id.png)](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Uw AWS-rol voor Purview maken:**

1.  Open uw **Amazon Web Services console** en selecteer IAM onder **Beveiliging, Identiteit en** **Naleving.**

1. Selecteer **Rollen** en vervolgens **Rol maken.**

1. Selecteer **Een ander AWS-account** en voer de volgende waarden in:

    |Veld  |Description  |
    |---------|---------|
    |**Account-id**     |    Voer uw Microsoft-account-id in. Bijvoorbeeld: `615019938638`     |
    |**Externe id**     |   Selecteer onder Opties **de optie Externe id vereisen...** en voer vervolgens uw externe id in het aangewezen veld in. <br>Bijvoorbeeld: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Bijvoorbeeld:

    ![Voeg de Microsoft-account-id toe aan uw AWS-account.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. Filter in **het gebied > machtigingenbeleid** voor koppelen de machtigingen die worden weergegeven op **S3.** Selecteer **AmazonS3ReadOnlyAccess** en selecteer vervolgens **Volgende: Tags.**

    ![Selecteer het readOnlyAccess-beleid voor de nieuwe Amazon S3-scanrol.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > Het **AmazonS3ReadOnlyAccess-beleid** biedt minimale machtigingen die vereist zijn voor het scannen van uw S3-buckets en kan ook andere machtigingen bevatten.
    >
    >Als u alleen de minimale machtigingen wilt toepassen die vereist zijn voor het scannen van uw buckets, maakt u een nieuw beleid met de machtigingen die worden vermeld in Minimale machtigingen voor uw [AWS-beleid,](#minimum-permissions-for-your-aws-policy)afhankelijk van of u één bucket of alle buckets in uw account wilt scannen. 
    >
    >Pas uw nieuwe beleid toe op de rol in plaats **van AmazonS3ReadOnlyAccess.**

1. In het **gebied Tags toevoegen (optioneel)** kunt u desgewenst een betekenisvolle tag voor deze nieuwe rol maken. Met handige tags kunt u de toegang organiseren, bijhouden en controleren voor elke rol die u maakt.

    Voer waar nodig een nieuwe sleutel en waarde voor uw tag in. Wanneer u klaar bent of als u deze stap wilt overslaan, selecteert u **Volgende: Controleren** om de roldetails te controleren en het maken van de rol te voltooien.

    ![Voeg een zinvolle tag toe om de toegang voor uw nieuwe rol te organiseren, bij te houden of te controleren.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. Ga als **volgt te** werk in het gebied Beoordeling:

    - Voer in **het veld Rolnaam** een betekenisvolle naam in voor uw rol
    - Voer in **het vak Beschrijving** van rol een optionele beschrijving in om het doel van de rol te identificeren
    - Controleer in **de** sectie Beleidsregels of het juiste beleid (**AmazonS3ReadOnlyAccess)** is gekoppeld aan de rol.

    Selecteer vervolgens **Rol maken om** het proces te voltooien.

    Bijvoorbeeld:

    ![Bekijk de details voordat u uw rol maakt.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Scannen voor versleutelde Amazon S3-buckets configureren

AWS-buckets ondersteunen meerdere versleutelingstypen. Voor buckets die gebruikmaken van **AWS-KMS-versleuteling,** is speciale configuratie vereist om scannen mogelijk te maken.

> [!NOTE]
> Voor buckets die geen versleuteling gebruiken, AES-256- of AWS-KMS S3-versleuteling, slaat u deze sectie over en gaat u verder met De naam van uw [Amazon S3-bucket ophalen.](#retrieve-your-amazon-s3-bucket-name)
>

**Het type versleuteling controleren dat wordt gebruikt in uw Amazon S3-buckets:**

1. Navigeer in AWS naar **Storage**  >  **S3** > selecteer **Buckets** in het menu aan de linkerkant.

    ![Selecteer het tabblad Amazon S3 Buckets.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Selecteer de bucket die u wilt controleren. Selecteer op de pagina met details van de bucket het **tabblad Eigenschappen** en schuif omlaag naar het **gebied Standaardversleuteling.**

    - Als de bucket die u hebt geselecteerd, is geconfigureerd voor alles behalve **AWS-KMS-versleuteling,** inclusief als de standaardversleuteling voor uw bucket **Uitgeschakeld** is, slaat u de rest van deze procedure over en gaat u verder met De naam van uw [Amazon S3-bucket ophalen.](#retrieve-your-amazon-s3-bucket-name)

    - Als de bucket die u hebt geselecteerd is geconfigureerd voor **AWS-KMS-versleuteling,** gaat u verder zoals hieronder wordt beschreven om een nieuw beleid toe te voegen waarmee een bucket met aangepaste **AWS-KMS-versleuteling kan worden** gescand.

    Bijvoorbeeld:

    ![Een Amazon S3-bucket weergeven die is geconfigureerd met AWS-KMS-versleuteling](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Een nieuw beleid toevoegen om het scannen van een bucket met aangepaste AWS-KMS-versleuteling toe te staan:**

1. Navigeer in AWS naar **Services**  >   **IAM-beleid**  >   en selecteer **Beleid maken.**

1. Definieer **uw beleid op** het tabblad Visual-editor voor beleid maken met de volgende  >   waarden:

    |Veld  |Description  |
    |---------|---------|
    |**Service**     |  Voer **KMS in en selecteer deze.**       |
    |**Acties**     | Selecteer **onder Toegangsniveau** de optie **Schrijven om** de sectie Schrijven uit **te** vouwen.<br>Nadat u het bestand uitvloog, selecteert u **alleen de optie Ontsleutelen.**        |
    |**Bronnen**     |Selecteer een specifieke resource of **Alle resources.**         |
    | | |

    Wanneer u klaar bent, selecteert u **Controlebeleid om** door te gaan.

    ![Maak een beleid voor het scannen van een bucket met AWS-KMS-versleuteling.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. Voer op **de pagina** Beleid controleren een beschrijvende naam voor uw beleid en een optionele beschrijving in en selecteer vervolgens **Beleid maken.**

    Het zojuist gemaakte beleid wordt toegevoegd aan uw lijst met beleidsregels.

1. Koppel uw nieuwe beleid aan de rol die u hebt toegevoegd voor scannen.

    1. Ga terug naar de **pagina IAM-rollen**  >   en selecteer de rol die u eerder [hebt toegevoegd.](#create-a-new-aws-role-for-purview)

    1. Selecteer op **het tabblad Machtigingen** de optie **Beleid koppelen.**

        ![Selecteer op het tabblad Machtigingen van uw rol de optie Beleid koppelen.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Zoek en **selecteer op de** pagina Machtigingen koppelen het nieuwe beleid dat u hierboven hebt gemaakt. Selecteer **Beleid koppelen om** uw beleid aan de rol te koppelen.

        De **pagina** Samenvatting wordt bijgewerkt, met uw nieuwe beleid gekoppeld aan uw rol.

        ![Bekijk een bijgewerkte overzichtspagina met het nieuwe beleid dat is gekoppeld aan uw rol.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Uw nieuwe Role ARN ophalen

U moet uw AWS Role ARN opnemen en deze kopiëren naar Purview wanneer u een scan voor uw [Amazon S3-bucket maakt.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**Uw rol ARN ophalen:**

1. Zoek en selecteer in het gebied **AWS Identity and Access Management (IAM)** Roles de nieuwe rol die u hebt gemaakt  >   [voor Purview](#create-a-purview-credential-for-your-aws-bucket-scan).

1. Selecteer op de pagina Samenvatting **van** de rol de knop Kopiëren naar **klembord** rechts van de **waarde Role ARN.**

    ![Kopieer de waarde van role ARN naar het klembord.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Plak deze waarde op een veilige locatie, klaar voor gebruik bij het maken van [een scan voor uw Amazon S3-bucket.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

### <a name="retrieve-your-amazon-s3-bucket-name"></a>De naam van uw Amazon S3-bucket ophalen

U hebt de naam van uw Amazon S3-bucket nodig om deze naar Purview te kopiëren wanneer u een scan voor [uw Amazon S3-bucket maakt](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**De naam van uw bucket ophalen:**

1. Navigeer in AWS naar **Storage**  >  **S3** > selecteer **Buckets** in het menu aan de linkerkant.

    ![Bekijk het tabblad Amazon S3-buckets.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Zoek en selecteer uw bucket om de pagina met bucketdetails weer te geven en kopieer vervolgens de bucketnaam naar het klembord.

    Bijvoorbeeld:

    ![Haal de URL van de S3-bucket op en kopieer deze.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Plak de naam van uw bucket in een beveiligd bestand en voeg er een voorvoegsel aan toe om de waarde te maken die u moet invoeren bij het configureren van uw bucket als een `s3://` Purview-resource.

    Bijvoorbeeld: `s3://purview-tutorial-bucket`

> [!NOTE]
> Alleen het hoofdniveau van uw bucket wordt ondersteund als een gegevensbron voor het opsmmeren van gegevens. De volgende URL, die een submap bevat, wordt *bijvoorbeeld niet* ondersteund: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Zoek uw AWS-account-id

U hebt uw AWS-account-id nodig om uw AWS-account te registreren als een Purview-gegevensbron, samen met alle buckets.

Uw AWS-account-id is de id die u gebruikt om u aan te melden bij de AWS-console. U kunt deze ook vinden wanneer u bent aangemeld op het IAM-dashboard, links onder de navigatieopties en bovenaan als het numerieke onderdeel van uw aanmeldings-URL:

Bijvoorbeeld:

![Haal uw AWS-account-id op.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Eén Amazon S3-bucket toevoegen als een Purview-resource

Gebruik deze procedure als u slechts één S3-bucket hebt die u wilt registreren bij Purview als gegevensbron, of als u meerdere buckets in uw AWS-account hebt, maar deze niet allemaal wilt registreren bij Purview.

**Uw bucket toevoegen:** 

1. Start de Purview-portal met behulp van de speciale Purview-connector voor Amazon S3 URL. Deze URL is aan u verstrekt door het productbeheerteam van amazon S3 Purview Connector.

    ![Start de Purview-portal.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Navigeer naar de pagina Azure Purview **Sources** en selecteer  ![ registerpictogram.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Ga door.**

    ![Voeg een Amazon AWS-bucket toe als een Purview-gegevensbron.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Als u meerdere [verzamelingen hebt](manage-data-sources.md#manage-collections) en uw Amazon S3  wilt toevoegen aan een specifieke verzameling, selecteert u de kaartweergave rechtsboven en selecteert u vervolgens het **pictogram** ![ Registreren registreren.](./media/register-scan-amazon-s3/register-button.png) in uw verzameling.
    >

1. Voer in **het deelvenster Bronnen registreren (Amazon S3)** dat wordt geopend de volgende gegevens in:

    |Veld  |Beschrijving  |
    |---------|---------|
    |**Naam**     |Voer een betekenisvolle naam in of gebruik de standaardwaarde.         |
    |**Bucket-URL**     | Voer de URL van uw AWS-bucket in met behulp van de volgende syntaxis:   `s3://<bucketName>`     <br><br>**Opmerking:** zorg ervoor dat u alleen het hoofdniveau van uw bucket gebruikt, zonder submappen. Zie Uw [Amazon S3-bucketnaam](#retrieve-your-amazon-s3-bucket-name)ophalen voor meer informatie. |
    |**Een verzameling selecteren** |Als u ervoor hebt gekozen om een gegevensbron te registreren vanuit een verzameling, wordt die verzameling al vermeld. <br><br>Selecteer zo nodig een andere verzameling, **Geen om** geen verzameling toe te wijzen of **Nieuw om** nu een nieuwe verzameling te maken. <br><br>Zie Gegevensbronnen beheren in Azure Purview voor meer informatie over [Purview-verzamelingen.](manage-data-sources.md#manage-collections)|
    | | |

    Wanneer u klaar bent, selecteert u **Voltooien om** de registratie te voltooien.

Ga door [met Een scan maken voor een of meer Amazon S3-buckets.](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Een Amazon-account toevoegen als een Purview-resource

Gebruik deze procedure als u meerdere S3-buckets in uw Amazon-account hebt en u ze allemaal wilt registreren als Gegevensbronnen opsmaken.

Wanneer [u de scan configureert,](#create-a-scan-for-one-or-more-amazon-s3-buckets)kunt u de specifieke buckets selecteren die u wilt scannen, als u niet alle buckets samen wilt scannen.

**Uw Amazon-account toevoegen:**
1. Start de Purview-portal met behulp van de speciale Purview-connector voor Amazon S3 URL. Deze URL is verstrekt door het productbeheerteam van amazon S3 Purview Connector.

    ![Connector starten voor speciale Amazon S3 Purview-portal](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Navigeer naar de pagina Azure Purview **Sources** en selecteer  ![ registerpictogram.](./media/register-scan-amazon-s3/register-button.png) > **Amazon-accounts**  >  **Ga door.**

    ![Voeg een Amazon-account toe als een Purview-gegevensbron.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Als u meerdere verzamelingen [hebt](manage-data-sources.md#manage-collections) en uw Amazon S3  wilt toevoegen aan een specifieke verzameling, selecteert u de weergave Kaart rechtsboven en selecteert u vervolgens het **pictogram** ![ Registreren registreren.](./media/register-scan-amazon-s3/register-button.png) in uw verzameling.
    >

1. Voer in **het deelvenster Bronnen registreren (Amazon S3)** dat wordt geopend de volgende gegevens in:

    |Veld  |Beschrijving  |
    |---------|---------|
    |**Naam**     |Voer een betekenisvolle naam in of gebruik de standaardwaarde.         |
    |**AWS-account-id**     | Voer uw AWS-account-id in. Zie Uw [AWS-account-id zoeken voor meer informatie](#locate-your-aws-account-id)|
    |**Een verzameling selecteren** |Als u ervoor hebt gekozen om een gegevensbron te registreren vanuit een verzameling, wordt die verzameling al vermeld. <br><br>Selecteer een andere verzameling als dat nodig is, **Geen** om geen verzameling toe te wijzen of **Nieuw om** nu een nieuwe verzameling te maken. <br><br>Zie Gegevensbronnen beheren in Azure Purview voor meer informatie over [Purview-verzamelingen.](manage-data-sources.md#manage-collections)|
    | | |

    Wanneer u klaar bent, selecteert u **Voltooien om** de registratie te voltooien.

Ga door [met Een scan maken voor een of meer Amazon S3-buckets.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Een scan maken voor een of meer Amazon S3-buckets

Nadat u uw buckets hebt toegevoegd als Gegevensbronnen opsviewen, kunt u een scan configureren om te worden uitgevoerd met geplande intervallen of onmiddellijk.

1. Navigeer naar het gebied Azure Purview **Sources** en doe het volgende:

    - Selecteer in **de kaartweergave** Nieuw **scanpictogram** ![ Nieuwe scan.](./media/register-scan-amazon-s3/new-scan-button.png) in uw gegevensbronvak.
    - Beweeg in **de lijstweergave** de muisaanwijzer over de rij voor uw gegevensbron en selecteer **Nieuwe scan** Pictogram ![ Nieuwe scan. ](./media/register-scan-amazon-s3/new-scan-button.png) .

1. **Definieer in** het deelvenster Scannen... dat aan de rechterkant wordt geopend de volgende velden en selecteer vervolgens **Doorgaan:**

    |Veld  |Beschrijving  |
    |---------|---------|
    |**Naam**     |  Voer een betekenisvolle naam in voor de scan of gebruik de standaardnaam.       |
    |**Type** |Wordt alleen weergegeven als u uw AWS-account hebt toegevoegd, inclusief alle buckets. <br><br>De huidige opties omvatten alleen **Alle**  >  **Amazon S3.** Blijf op de hoogte voor meer opties om te selecteren wanneer de ondersteuningsmatrix van Purview wordt uitgebreid. |
    |**Referentie**     |  Selecteer een Purview-referentie met uw rol ARN. <br><br>**Tip:** als u op dit moment een nieuwe referentie wilt maken, selecteert u **Nieuw.** Zie Create [a Purview credential for your AWS bucket scan (Een purview-referentie maken voor uw AWS-bucketscan) voor meer informatie.](#create-a-purview-credential-for-your-aws-bucket-scan)     |
    | **Amazon S3**    |   Wordt alleen weergegeven als u uw AWS-account hebt toegevoegd, inclusief alle buckets. <br><br>Selecteer een of meer buckets om te scannen of Alles **selecteren om** alle buckets in uw account te scannen.      |
    | | |

    Purview controleert automatisch of de rol-ARN geldig is, of de buckets en objecten binnen de buckets toegankelijk zijn en gaat door als de verbinding slaagt.

    > [!TIP]
    > Als u verschillende waarden wilt invoeren en de verbinding zelf wilt testen voordat u doorgaat, selecteert u **Verbinding** testen rechtsonder voordat u **Doorgaan selecteert.**
    >

1. Selecteer in **het deelvenster Een scanregelset** selecteren de standaardregelset **AmazonS3** of selecteer Nieuwe **scanregelset** om een nieuwe aangepaste regelset te maken. Zodra u de regelset hebt geselecteerd, selecteert u **Doorgaan.**

    Als u een nieuwe aangepaste scanregelset wilt maken, gebruikt u de wizard om de volgende instellingen te definiëren:

    |Deelvenster  |Description  |
    |---------|---------|
    |**Nieuwe regelset voor scannen** /<br>**Beschrijving van scanregel**    |   Voer een beschrijvende naam en een optionele beschrijving in voor uw regelset      |
    |**Bestandstypen selecteren**     | Selecteer alle bestandstypen die u wilt opnemen in de scan en selecteer vervolgens **Doorgaan.**<br><br>Als u een nieuw bestandstype wilt toevoegen, selecteert **u Nieuw bestandstype** en definieert u het volgende: <br>- De bestandsextensie die u wilt toevoegen <br>- Een optionele beschrijving  <br>- Of de inhoud van het bestand een aangepast scheidingsteken heeft of een systeembestandstype is. Voer vervolgens uw aangepaste scheidingsteken in of selecteer uw systeembestandstype. <br><br>Selecteer **Maken om** uw aangepaste bestandstype te maken.     |
    |**Classificatieregels selecteren**     |   Navigeer naar en selecteer de classificatieregels die u wilt uitvoeren op uw gegevensset.      |
    |     |         |

    Selecteer **Maken** wanneer u klaar bent om de regelset te maken.

1. Selecteer in **het deelvenster Een scantrigger** instellen een van de volgende opties en selecteer vervolgens **Doorgaan:**

    - **Terugkerend** om een planning voor een terugkerende scan te configureren
    - **Eenmaal** om een scan te configureren die onmiddellijk wordt gestart

1. Controleer in het **deelvenster Uw scan** controleren uw scandetails om  te  controleren of ze juist zijn. Selecteer vervolgens Opslaan of Opslaan en uitvoeren als u Eenmaal **hebt** geselecteerd in het vorige deelvenster.

    > [!NOTE]
    > Zodra het scannen is gestart, kan het tot 24 uur duren voordat het scannen is voltooid. U kunt uw Inzichtrapporten  bekijken en 24 uur nadat u de scan hebt gestart, in de catalogus zoeken.
    >

Zie Scanresultaten van [Purview verkennen voor meer informatie.](#explore-purview-scanning-results)

## <a name="explore-purview-scanning-results"></a>Scanresultaten van Purview verkennen

Zodra een Scan op beeld is voltooid voor uw Amazon S3-buckets, zoomt u in op het gebied Purview **Sources** om de scangeschiedenis te bekijken.

Selecteer een gegevensbron om de details ervan weer te geven en selecteer vervolgens het **tabblad Scans** om alle scans weer te geven die momenteel worden uitgevoerd of voltooid.
Als u een AWS-account met meerdere buckets hebt toegevoegd, wordt de scangeschiedenis voor elke bucket weergegeven onder het account.

Bijvoorbeeld:

![De AWS S3-bucketscans onder de bron van uw AWS-account tonen.](./media/register-scan-amazon-s3/account-scan-history.png)

Gebruik de andere gebieden van Purview voor meer informatie over de inhoud in uw gegevensruimte, waaronder uw Amazon S3-buckets:

- **Zoek de gegevenscatalogus Purview en** filter op een specifieke bucket. Bijvoorbeeld:

    ![Zoek in de catalogus naar AWS S3-assets.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Bekijk Inzichtrapporten om** statistieken weer te geven voor de classificatie, gevoeligheidslabels, bestandstypen en meer informatie over uw inhoud.

    Alle Purview Insight-rapporten bevatten de Amazon S3-scanresultaten, samen met de rest van de resultaten van uw Azure-gegevensbronnen. Indien relevant is er een extra **Amazon S3-assettype** toegevoegd aan de filteropties voor het rapport.

    Zie Inzicht in [Azure Purview](concept-insights.md)begrijpen voor meer informatie.

## <a name="minimum-permissions-for-your-aws-policy"></a>Minimale machtigingen voor uw AWS-beleid

De standaardprocedure voor het maken van een [AWS-rol](#create-a-new-aws-role-for-purview) voor Purview die moet worden gebruikt bij het scannen van uw S3-buckets, maakt gebruik van het **beleid AmazonS3ReadOnlyAccess.**

Het **AmazonS3ReadOnlyAccess-beleid** biedt minimale machtigingen die vereist zijn voor het scannen van uw S3-buckets en kan ook andere machtigingen bevatten.

Als u alleen de minimale machtigingen wilt toepassen die nodig zijn voor het scannen van uw buckets, maakt u een nieuw beleid met de machtigingen die in de volgende secties worden vermeld, afhankelijk van of u één bucket of alle buckets in uw account wilt scannen.

Pas uw nieuwe beleid toe op de rol in plaats **van AmazonS3ReadOnlyAccess.**

### <a name="individual-buckets"></a>Afzonderlijke buckets

Bij het scannen van afzonderlijke S3-buckets zijn de minimale AWS-machtigingen:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Zorg ervoor dat u uw resource definieert met de naam van de specifieke bucket. Bijvoorbeeld:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>Alle buckets in uw account

Bij het scannen van alle buckets in uw AWS-account zijn de minimale AWS-machtigingen:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Zorg ervoor dat u uw resource met een jokerteken definieert. Bijvoorbeeld:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Purview Insight-rapporten:

> [!div class="nextstepaction"]
> [Inzichten in Azure Purview begrijpen](concept-insights.md)

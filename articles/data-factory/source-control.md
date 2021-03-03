---
title: Broncodebeheer
description: Meer informatie over het configureren van broncode beheer in Azure Data Factory
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2021
ms.openlocfilehash: 7691c285bcc1c490878f5055468b0a57b6248679
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719379"
---
# <a name="source-control-in-azure-data-factory"></a>Broncode beheer in Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Standaard Azure Data Factory gebruikers interface-schrijvers (UX) direct voor de data factory-service. Deze ervaring heeft de volgende beperkingen:

- De Data Factory-service bevat geen opslag plaats voor het opslaan van de JSON-entiteiten voor uw wijzigingen. De enige manier om wijzigingen op te slaan is via de knop **Alles publiceren** en alle wijzigingen worden rechtstreeks naar de Data Factory-service gepubliceerd.
- De Data Factory-service is niet geoptimaliseerd voor samen werking en versie beheer.
- De Azure Resource Manager sjabloon die is vereist voor de implementatie van Data Factory zelf, is niet opgenomen.

Azure Data Factory kunt u een Git-opslag plaats met behulp van Azure opslag plaatsen of GitHub configureren om een betere ontwerp ervaring te bieden. Git is een versie beheersysteem waarmee u eenvoudiger wijzigingen kunt bijhouden en samen werken. In dit artikel wordt beschreven hoe u in een Git-opslag plaats kunt configureren en gebruiken, samen met het markeren van aanbevolen procedures en een probleemoplossings handleiding.

> [!NOTE]
> Voor Azure Government Cloud is alleen GitHub Enter prise beschikbaar.

Voor meer informatie over hoe Azure Data Factory integreert met git, raadpleegt u de video over 15 minuten zelf studie:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Voordelen van Git-integratie

Hieronder vindt u een lijst met een aantal voor delen Git-integratie voor de ontwerp ervaring:

-   **Broncode beheer:** Als uw data factory-workloads cruciaal worden, wilt u uw fabriek met git integreren om diverse voor delen van broncode beheer te benutten, zoals in het volgende:
    -   De mogelijkheid om wijzigingen bij te houden/te controleren.
    -   De mogelijkheid om wijzigingen die fouten hebben geïntroduceerd te herstellen.
-   **Gedeeltelijk opgeslagen:** Wanneer u een ontwerp uitvoert voor de data factory-service, kunt u geen wijzigingen opslaan als concept en moeten alle publicaties data factory validatie door geven. Of uw pijp lijnen niet zijn voltooid of dat u de wijzigingen niet wilt kwijt raken als uw computer vastloopt, kunt u met git-integratie incrementele wijzigingen van data factory resources toestaan, ongeacht de status van deze apparaten. Als u een Git-opslag plaats configureert, kunt u de wijzigingen opslaan, zodat u alleen publiceert wanneer u uw wijzigingen in uw tevredenheid hebt getest.
-   **Samen werking en beheer:** Als u meerdere team leden hebt die bijdragen aan dezelfde Factory, kunt u uw team genoten met elkaar laten samen werken via een code controle proces. U kunt ook uw fabriek zodanig instellen dat niet elke Inzender dezelfde machtigingen heeft. Sommige team leden mogen alleen wijzigingen aanbrengen via git en alleen bepaalde personen in het team mogen de wijzigingen in de fabriek publiceren.
-   **Betere CI/CD:**  Als u implementeert in meerdere omgevingen met een [doorlopend leverings proces](continuous-integration-deployment.md), worden bepaalde acties eenvoudiger gemaakt door git-integratie. Enkele van deze acties zijn:
    -   Configureer de release pijplijn zo dat deze automatisch wordt geactiveerd zodra er wijzigingen zijn aangebracht in de Factory van de ontwikkelaar.
    -   Pas de eigenschappen in uw fabriek aan die beschikbaar zijn als para meters in de Resource Manager-sjabloon. Het kan handig zijn om alleen de vereiste set eigenschappen als para meters te gebruiken en alle andere gegevens vast te maken.
-   **Betere prestaties:** Een gemiddelde fabriek met git-integratie laadt tien keer sneller dan een ontwerp voor de data factory service. Deze verbetering van prestaties is omdat resources worden gedownload via git.

> [!NOTE]
> Als u een Git-opslag plaats configureert, wordt de Data Factory-service direct in de Azure Data Factory UX gemaakt. Wijzigingen die zijn aangebracht via Power shell of een SDK worden rechtstreeks naar de Data Factory-service gepubliceerd en worden niet in Git ingevoerd.

## <a name="connect-to-a-git-repository"></a>Verbinding maken met een Git-opslag plaats

Er zijn vier verschillende manieren om een Git-opslag plaats te koppelen aan uw data factory voor zowel Azure opslag plaatsen als GitHub. Nadat u verbinding hebt gemaakt met een Git-opslag plaats, kunt u uw configuratie weer geven en beheren in de [beheer hub](author-management-hub.md) onder **Git-configuratie** in de sectie **bron beheer**

### <a name="configuration-method-1-home-page"></a>Configuratie methode 1: start pagina

Selecteer op de start pagina van Azure Data Factory de optie **code opslagplaats instellen**.

![Een code opslagplaats configureren vanaf de start pagina](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>Configuratie methode 2: canvas ontwerpen

Selecteer in het Azure Data Factory UX-bewerkings canvas de vervolg keuzelijst **Data Factory** en selecteer vervolgens **code opslagplaats instellen**.

![De instellingen van de code opslagplaats configureren vanuit ontwerpen](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>Configuratie methode 3: Management hub

Ga naar de beheer hub in de ADF-UX. Selecteer **Git-configuratie** in de sectie **bron beheer** . Als u geen opslag plaats hebt verbonden, klikt u op **configureren**.

![De instellingen van de code opslagplaats van de Management hub configureren](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>Configuratie methode 4: tijdens het maken van de fabriek

Wanneer u een nieuwe data factory in het Azure Portal maakt, kunt u Git-opslagplaats gegevens configureren op het tabblad **Git-configuratie** .

> [!NOTE]
> Bij het configureren van git in azure Portal, moeten instellingen zoals project naam en opslag plaats naam hand matig worden ingevoerd in plaats daarvan deel uit te maken van een vervolg keuzelijst.

![De instellingen van de code opslagplaats configureren vanuit Azure Portal](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Ontwerpen met Git-integratie van Azure-opslagplaatsen

Visual authoring met Azure opslag plaatsen Git-integratie ondersteunt broncode beheer en samen werking voor werk op uw data factory-pijp lijnen. U kunt een data factory koppelen aan een Azure opslag plaatsen Git-opslag plaats voor broncode beheer, samen werking, versies, enzovoort. Eén Azure opslag plaatsen Git-organisatie kan meerdere opslag plaatsen hebben, maar een Azure opslag plaatsen Git-opslag plaats kan slechts worden gekoppeld aan één data factory. Als u geen Azure opslag plaatsen-organisatie of-opslag plaats hebt, volgt u [deze instructies](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) om uw resources te maken.

> [!NOTE]
> U kunt script-en gegevens bestanden opslaan in een Azure opslag plaatsen Git-opslag plaats. U moet de bestanden echter hand matig uploaden naar Azure Storage. Een data factory pijp lijn uploadt geen script of gegevens bestanden die zijn opgeslagen in een Azure opslag plaatsen Git-opslag plaats naar Azure Storage.

### <a name="azure-repos-settings"></a>Azure opslag plaatsen-instellingen

![De instellingen voor de code opslagplaats configureren](media/author-visually/repo-settings.png)

In het deel venster configuratie worden de volgende instellingen voor Azure opslag plaatsen code opslagplaats weer gegeven:

| Instelling | Beschrijving | Waarde |
|:--- |:--- |:--- |
| **Type opslag plaats** | Het type van de Azure opslag plaatsen code-opslag plaats.<br/> | Azure DevOps git of GitHub |
| **Azure Active Directory** | De naam van uw Azure AD-Tenant. | `<your tenant name>` |
| **Azure opslag plaatsen-organisatie** | De naam van uw Azure opslag plaatsen-organisatie. U kunt de naam van uw Azure opslag plaatsen-organisatie vinden op `https://{organization name}.visualstudio.com` . U kunt [zich aanmelden bij uw Azure opslag plaatsen-organisatie](https://www.visualstudio.com/team-services/git/) om toegang te krijgen tot uw Visual Studio-profiel en uw opslag plaatsen en projecten te bekijken. | `<your organization name>` |
| **ProjectName** | De naam van uw Azure opslag plaatsen-project. U kunt de naam van uw Azure opslag plaatsen-project vinden op `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **Opslagplaats** | De naam van de opslag plaats van uw Azure opslag plaatsen-code. Azure opslag plaatsen-projecten bevatten Git-opslag plaatsen om uw bron code te beheren naarmate uw project groeit. U kunt een nieuwe opslag plaats maken of een bestaande opslag plaats gebruiken die al in uw project voor komt. | `<your Azure Repos code repository name>` |
| **Collaboration Branch** | Uw Azure opslag plaatsen Collaboration-vertakking die wordt gebruikt voor het publiceren. Standaard is dit `main` . Wijzig deze instelling als u resources wilt publiceren vanuit een andere vertakking. | `<your collaboration branch name>` |
| **Hoofdmap** | Uw hoofdmap in uw Azure opslag plaatsen Collaboration-vertakking. | `<your root folder name>` |
| **Bestaande Data Factory-resources importeren in opslag plaats** | Hiermee geeft u op of bestaande data factory resources van het UX- **ontwerp canvas** in een Azure opslag plaatsen Git-opslag plaats moeten worden geïmporteerd. Schakel het selectie vakje in om uw data factory-resources te importeren in de bijbehorende Git-opslag plaats in JSON-indeling. Deze actie exporteert elke resource afzonderlijk (dat wil zeggen, de gekoppelde services en gegevens sets worden geëxporteerd naar afzonderlijke JSONs). Als dit selectie vakje niet is ingeschakeld, worden de bestaande resources niet geïmporteerd. | Geselecteerd (standaard) |
| **Vertakking waarvoor de resource moet worden geïmporteerd** | Hiermee wordt aangegeven in welke vertakking de data factory resources (pijp lijnen, gegevens sets, gekoppelde services, enzovoort) worden geïmporteerd. U kunt resources importeren in een van de volgende vertakkingen: a. Samen werking b. Nieuwe c maken. Bestaande gebruiken |  |

> [!NOTE]
> Als u micro soft Edge gebruikt en er geen waarden in de vervolg keuzelijst van uw Azure DevOps-account worden weer geven, voegt u https://*. Visual Studio. com toe aan de lijst met vertrouwde websites.

### <a name="use-a-different-azure-active-directory-tenant"></a>Een andere Azure Active Directory Tenant gebruiken

De Azure opslag plaatsen Git opslag plaats kan zich in een andere Azure Active Directory Tenant bevindt. Als u een andere Azure AD-tenant wilt opgeven, moet u beheerdersmachtigingen hebben voor het Azure-abonnement dat u gebruikt. Zie de [abonnements beheerder wijzigen](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator) voor meer informatie

> [!IMPORTANT]
> Als u verbinding wilt maken met een andere Azure Active Directory, moet de aangemelde gebruiker deel uitmaken van die Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Uw persoonlijke Microsoft-account gebruiken

Als u een persoonlijk Microsoft-account wilt gebruiken voor git-integratie, kunt u uw persoonlijke Azure-opslag plaats koppelen aan de Active Directory van uw organisatie.

1. Voeg uw persoonlijke Microsoft-account toe aan de Active Directory van uw organisatie als gast. Zie [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../active-directory/external-identities/add-users-administrator.md)voor meer informatie.

2. Meld u aan bij de Azure Portal met uw persoonlijke Microsoft-account. Schakel vervolgens over naar de Active Directory van uw organisatie.

3. Ga naar de sectie Azure DevOps, waar u nu uw persoonlijke opslag plaats ziet. Selecteer de opslag plaats en maak verbinding met Active Directory.

Na deze configuratie stappen is uw persoonlijke opslag plaats beschikbaar wanneer u Git-integratie instelt in de gebruikers interface van Data Factory.

Zie [uw Azure DevOps-organisatie verbinden met Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)voor meer informatie over het verbinden van Azure opslag plaatsen met de Active Directory van uw organisatie.

## <a name="author-with-github-integration"></a>Ontwerpen met GitHub-integratie

Visual authoring met GitHub-integratie ondersteunt broncode beheer en samen werking voor werk op uw data factory-pijp lijnen. U kunt een data factory koppelen aan een GitHub-account opslagplaats voor broncode beheer, samen werking, versies. Eén GitHub-account kan meerdere opslag plaatsen bevatten, maar een GitHub-opslag plaats kan slechts worden gekoppeld aan één data factory. Als u geen GitHub-account of-opslag plaats hebt, volgt u [deze instructies](https://github.com/join) om uw resources te maken.

De GitHub-integratie met Data Factory ondersteunt zowel open bare GitHub (dat wil zeggen [https://github.com](https://github.com) ) als github Enter prise. U kunt zowel open bare als persoonlijke GitHub-opslag plaatsen gebruiken met Data Factory zolang u lees-en schrijf machtigingen hebt voor de opslag plaats in GitHub.

Als u een GitHub opslag plaats wilt configureren, moet u over beheerders rechten beschikken voor het Azure-abonnement dat u gebruikt.

### <a name="github-settings"></a>GitHub-instellingen

![Instellingen voor GitHub-opslag plaats](media/author-visually/github-integration-image2.png)

In het deel venster configuratie worden de volgende instellingen voor de GitHub-opslag plaats weer gegeven:

| **Instelling** | **Beschrijving**  | **Waarde**  |
|:--- |:--- |:--- |
| **Type opslag plaats** | Het type van de Azure opslag plaatsen code-opslag plaats. | GitHub |
| **GitHub Enter prise gebruiken** | Selectie vakje om GitHub Enter prise te selecteren | selectie opheffen (standaard) |
| **GitHub Enter prise-URL** | De GitHub van de Enter prise-basis-URL (moet HTTPS zijn voor lokale GitHub Enter prise-server). Bijvoorbeeld: `https://github.mydomain.com`. Alleen vereist als **use github Enter prise** is geselecteerd | `<your GitHub enterprise url>` |                                                           
| **GitHub-account** | De naam van uw GitHub-account. Deze naam kan worden gevonden vanuit https: \/ /github.com/{account name}/{repository name}. Als u naar deze pagina navigeert, wordt u gevraagd om GitHub OAuth-referenties in te voeren voor uw GitHub-account. | `<your GitHub account name>` |
| **Naam van opslag plaats**  | De naam van de opslag plaats van uw GitHub-code. GitHub-accounts bevatten Git-opslag plaatsen voor het beheren van de bron code. U kunt een nieuwe opslag plaats maken of een bestaande opslag plaats gebruiken die al in uw account is. | `<your repository name>` |
| **Collaboration Branch** | Uw GitHub-samenwerkings vertakking die wordt gebruikt voor het publiceren. Het is standaard het hoofd. Wijzig deze instelling als u resources wilt publiceren vanuit een andere vertakking. | `<your collaboration branch>` |
| **Hoofdmap** | Uw hoofdmap in uw GitHub-samenwerkings vertakking. |`<your root folder name>` |
| **Bestaande Data Factory-resources importeren in opslag plaats** | Hiermee geeft u op of bestaande data factory resources van het UX-ontwerp canvas in een GitHub-opslag plaats moeten worden geïmporteerd. Schakel het selectie vakje in om uw data factory-resources te importeren in de bijbehorende Git-opslag plaats in JSON-indeling. Deze actie exporteert elke resource afzonderlijk (dat wil zeggen, de gekoppelde services en gegevens sets worden geëxporteerd naar afzonderlijke JSONs). Als dit selectie vakje niet is ingeschakeld, worden de bestaande resources niet geïmporteerd. | Geselecteerd (standaard) |
| **Vertakking waarvoor de resource moet worden geïmporteerd** | Hiermee wordt aangegeven in welke vertakking de data factory resources (pijp lijnen, gegevens sets, gekoppelde services, enzovoort) worden geïmporteerd. U kunt resources importeren in een van de volgende vertakkingen: a. Samen werking b. Nieuwe c maken. Bestaande gebruiken |  |

### <a name="github-organizations"></a>GitHub organisaties

Als u verbinding wilt maken met een GitHub-organisatie, moet de organisatie toestemming geven voor Azure Data Factory. Een gebruiker met beheerders machtigingen voor de organisatie moet de onderstaande stappen uitvoeren om data factory verbinding te maken.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>De eerste keer in Azure Data Factory verbinding maken met GitHub

Als u voor de eerste keer verbinding maakt met GitHub vanuit Azure Data Factory, voert u de volgende stappen uit om verbinding te maken met een GitHub-organisatie.

1. Voer in het deel venster Git-configuratie de naam van de organisatie in het veld *github-account* in. Er verschijnt een prompt om u aan te melden bij GitHub. 
1. Meld u aan met uw gebruikers referenties.
1. U wordt gevraagd Azure Data Factory te autoriseren als een toepassing met de naam *AzureDataFactory*. In dit scherm ziet u een optie voor het verlenen van machtigingen voor de ADF om toegang te krijgen tot de organisatie. Als u de optie om toestemming te verlenen niet ziet, vraagt u een beheerder om de machtiging hand matig via GitHub toe te kennen.

Zodra u deze stappen hebt uitgevoerd, kan uw fabriek verbinding maken met zowel open bare als privé-opslag plaatsen binnen uw organisatie. Als u geen verbinding kunt maken, probeert u de cache van de browser te wissen en opnieuw te proberen.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Al verbonden met GitHub met een persoonlijk account

Als u al verbinding hebt gemaakt met GitHub en alleen machtigingen hebt verleend voor toegang tot een persoonlijk account, volgt u de onderstaande stappen om machtigingen toe te kennen aan een organisatie. 

1. Ga naar GitHub en open **instellingen**.

    ![Instellingen voor GitHub openen](media/author-visually/github-settings.png)

1. Selecteer **toepassingen**. Op het tabblad **geautoriseerde OAuth-apps** ziet u *AzureDataFactory*.

    ![OAuth-apps selecteren](media/author-visually/github-organization-select-application.png)

1. Selecteer de toepassing en verleen de toepassing toegang tot uw organisatie.

    ![Toegang verlenen](media/author-visually/github-organization-grant.png)

Zodra u deze stappen hebt uitgevoerd, kan uw fabriek verbinding maken met zowel open bare als privé-opslag plaatsen binnen uw organisatie. 

### <a name="known-github-limitations"></a>Bekende GitHub-beperkingen

- U kunt script-en gegevens bestanden opslaan in een GitHub-opslag plaats. U moet de bestanden echter hand matig uploaden naar Azure Storage. Een Data Factory pijp lijn uploadt niet automatisch script-of gegevens bestanden die zijn opgeslagen in een GitHub-opslag plaats naar Azure Storage.

- GitHub Enter prise met een oudere versie dan 2.14.0 werkt niet in de micro soft Edge-browser.

- GitHub-integratie met de Data Factory Visual authoring-hulpprogram ma's werkt alleen in de algemeen beschik bare versie van Data Factory.


- Er kunnen Maxi maal 1.000 entiteiten per resource type (zoals pijp lijnen en gegevens sets) worden opgehaald uit één GitHub-vertakking. Als deze limiet is bereikt, wordt u geadviseerd om uw resources te splitsen in afzonderlijke fabrieken. Deze beperking is niet aanwezig in Azure DevOps Git.

## <a name="version-control"></a>Versiebeheer

Met versie besturings systemen (ook wel bekend als _broncode beheer_) kunnen ontwikkel aars samen werken aan code en wijzigingen bijhouden die zijn aangebracht in de code basis. Broncode beheer is een essentieel hulp programma voor projecten met meerdere ontwikkel aars.

### <a name="creating-feature-branches"></a>Functie vertakkingen maken

Elke Azure opslag plaatsen Git-opslag plaats die is gekoppeld aan een data factory heeft een Collaboration Branch. ( `main` ) is de standaard-Collaboration Branch). Gebruikers kunnen ook onderdeel vertakkingen maken door op **+ nieuwe vertakking** in de vervolg keuzelijst vertakking te klikken. Zodra het deel venster nieuwe vertakking wordt weer gegeven, voert u de naam van uw functie vertakking in.

![Een nieuwe vertakking maken](media/author-visually/new-branch.png)

Wanneer u klaar bent om de wijzigingen van uw functie vertakking samen te voegen met uw vertakking voor samen werking, klikt u op de vervolg keuzelijst vertakking en selecteert u **pull-aanvraag maken**. Met deze actie gaat u naar Azure opslag plaatsen Git waar u pull-aanvragen kunt genereren, code beoordelingen moet uitvoeren en wijzigingen kunt samen voegen in uw samenwerkings vertakking. ( `main` is de standaard instelling). U mag alleen publiceren naar de Data Factory-service vanuit uw vertakking voor samen werking. 

![Een nieuwe pull-aanvraag maken](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Publicatie-instellingen configureren

Data factory genereert standaard de Resource Manager-sjablonen van de gepubliceerde Factory en slaat ze op in een vertakking met de naam `adf_publish` . Als u een aangepaste publicatie vertakking wilt configureren, voegt `publish_config.json` u een bestand toe aan de hoofdmap in de vertakking voor samen werking. Bij het publiceren wordt dit bestand door ADF gelezen, wordt gezocht naar het veld `publishBranch` en worden alle Resource Manager-sjablonen op de opgegeven locatie opgeslagen. Als de vertakking niet bestaat, wordt deze automatisch door data factory gemaakt. Hieronder ziet u een voor beeld van hoe dit bestand eruitziet:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory kan slechts één Publiceer vertakking tegelijk hebben. Wanneer u een nieuwe publicatie vertakking opgeeft, wordt Data Factory de vorige Publish-vertakking niet verwijderd. Als u de vorige Publish-vertakking wilt verwijderen, moet u deze hand matig verwijderen.

> [!NOTE]
> Data Factory leest alleen het `publish_config.json` bestand wanneer de fabriek wordt geladen. Als u de fabriek al hebt geladen in de portal, vernieuwt u de browser om uw wijzigingen van kracht te laten worden.

### <a name="publish-code-changes"></a>Code wijzigingen publiceren

Nadat u de wijzigingen hebt samengevoegd in de collaboration Branch ( `main` is de standaard instelling), klikt u op **publiceren** om de wijzigingen in de code in de hoofd vertakking hand matig naar de Data Factory-service te publiceren.

![Wijzigingen publiceren in de Data Factory-Service](media/author-visually/publish-changes.png)

Er wordt een deel venster geopend waarin u bevestigt dat de publicatie vertakking en in behandeling zijnde wijzigingen juist zijn. Nadat u uw wijzigingen hebt gecontroleerd, klikt u op **OK** om de publicatie te bevestigen.

![De juiste publicatie vertakking bevestigen](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> De hoofd vertakking is niet representatief voor wat er in de Data Factory-service is geïmplementeerd. De hoofd vertakking *moet* hand matig naar de Data Factory-service worden gepubliceerd.

## <a name="best-practices-for-git-integration"></a>Aanbevolen procedures voor git-integratie

### <a name="permissions"></a>Machtigingen

Normaal gesp roken wilt u niet dat elk teamlid gemachtigd is om de Data Factory bij te werken. De volgende machtigings instellingen worden aanbevolen:

*   Alle team leden moeten lees machtigingen hebben voor de Data Factory.
*   Alleen een select-set met personen mag publiceren naar het Data Factory. Hiervoor moeten ze beschikken over de Data Factory rol **Inzender** voor de **resource groep** die de Data Factory bevat. Zie [rollen en machtigingen voor Azure Data Factory](concepts-roles-permissions.md)voor meer informatie over machtigingen.

Het is raadzaam om directe incheckers niet toe te staan voor de vertakking voor samen werking. Deze beperking kan helpen te voor komen dat er fouten optreden bij elke check-in om een pull-aanvraag beoordelings proces te door lopen dat wordt beschreven in [functie vertakkingen maken](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Wacht woorden van Azure Key Vault gebruiken

Het is raadzaam om Azure Key Vault te gebruiken voor het opslaan van verbindings reeksen of wacht woorden of beheerde identiteits verificatie voor Data Factory gekoppelde services. Uit veiligheids overwegingen slaat data factory geen geheimen op in Git. Eventuele wijzigingen in gekoppelde services met geheimen, zoals wacht woorden, worden direct gepubliceerd in de Azure Data Factory-service.

Het gebruik van Key Vault-of MSI-verificatie zorgt ook voor continue integratie en implementatie, omdat u deze geheimen niet hoeft op te geven tijdens de implementatie van Resource Manager-sjablonen.

## <a name="troubleshooting-git-integration"></a>Problemen met Git-integratie oplossen

### <a name="stale-publish-branch"></a>Verouderde publicatie vertakking

Als de publicatie vertakking niet is gesynchroniseerd met de hoofd vertakking en verouderde bronnen bevat ondanks een recente publicatie, voert u de volgende stappen uit:

1. Uw huidige Git-opslag plaats verwijderen
1. Configureer Git opnieuw met dezelfde instellingen, maar zorg ervoor dat **bestaande Data Factory resources importeren in opslag plaats** is geselecteerd en kies **nieuwe vertakking**
1. Een pull-aanvraag maken om de wijzigingen in de samenwerkingsvertakking samen te voegen 

Hieronder ziet u enkele voor beelden van situaties die een verouderde publicatie vertakking kunnen veroorzaken:
- Een gebruiker heeft meerdere vertakkingen. In één functie vertakking hebben ze een gekoppelde service verwijderd die niet Azure gekoppeld is (niet Azure gekoppelde services onmiddellijk worden gepubliceerd, ongeacht of ze in Git of niet zijn) en nooit de functie vertakking in de vertakking samen te voegen.
- Een gebruiker heeft de data factory gewijzigd met de SDK of Power shell
- Een gebruiker heeft alle resources naar een nieuwe vertakking verplaatst en heeft geprobeerd om de eerste keer te publiceren. Gekoppelde services moeten hand matig worden gemaakt bij het importeren van resources.
- Een gebruiker uploadt een niet-Azure gekoppelde service of een Integration Runtime JSON hand matig. Ze verwijzen naar die resource vanuit een andere resource, zoals een gegevensset, een gekoppelde service of een pijp lijn. Een niet-Azure gekoppelde service die via de UX is gemaakt, wordt onmiddellijk gepubliceerd, omdat de referenties moeten worden versleuteld. Als u een gegevensset uploadt die verwijst naar die gekoppelde service en probeert te publiceren, wordt deze door de UX toegestaan omdat deze zich in de Git-omgeving bevindt. Deze wordt op het moment van publicatie geweigerd omdat deze niet bestaat in de data factory service.

## <a name="switch-to-a-different-git-repository"></a>Overschakelen naar een andere Git-opslag plaats

Als u wilt overschakelen naar een andere Git-opslag plaats, gaat u naar de pagina Git-configuratie in de beheer hub onder **broncode beheer**. Selecteer **verbinding verbreken**. 

![Git-pictogram](media/author-visually/remove-repository.png)

Voer uw data factory naam in en klik op **bevestigen** om de Git-opslag plaats te verwijderen die is gekoppeld aan uw Data Factory.

![De koppeling met de huidige Git-opslag plaats verwijderen](media/author-visually/remove-repository-2.png)

Nadat u de koppeling met de huidige opslag plaats hebt verwijderd, kunt u uw Git-instellingen configureren voor het gebruik van een andere opslag plaats en vervolgens bestaande Data Factory resources importeren in de nieuwe opslag plaats.

> [!IMPORTANT]
> Wanneer u de Git-configuratie van een data factory verwijdert, worden niets uit de opslag plaats verwijderd. In de fabriek worden alle gepubliceerde resources opgenomen. U kunt door gaan met het rechtstreeks bewerken van de fabriek op de service.

## <a name="next-steps"></a>Volgende stappen

* Zie [pijp lijnen bewaken en beheren via een programma](monitor-programmatically.md)voor meer informatie over het controleren en beheren van pijp lijnen.
* Zie [doorlopende integratie en levering (CI/cd) in azure Data Factory](continuous-integration-deployment.md)voor het implementeren van doorlopende integratie en implementatie.

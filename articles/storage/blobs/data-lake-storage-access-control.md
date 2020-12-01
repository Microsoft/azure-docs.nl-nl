---
title: Access Control Lists in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Begrijp hoe POSIX-achtige Acl's toegangs beheer lijsten werken in Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 2418a8813e7b9de603b7e7cdc11fc756d73ac2a4
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350752"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>Toegangs beheer lijsten (Acl's) in Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementeert een model voor toegangs beheer dat zowel op rollen gebaseerd toegangs beheer (Azure RBAC) als POSIX-achtige Acl's (toegangs beheer lijsten) ondersteunt. In dit artikel worden de toegangs beheer lijsten in Data Lake Storage Gen2 beschreven. Zie het [Access Control model in azure data Lake Storage Gen2](data-lake-storage-access-control-model.md)voor meer informatie over het integreren van Azure RBAC met acl's en hoe het systeem ze evalueert om autorisatie beslissingen te nemen.

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>Over Acl's

U kunt een [beveiligingsprincipal](../../role-based-access-control/overview.md#security-principal) koppelen aan een toegangs niveau voor bestanden en mappen. Deze koppelingen worden vastgelegd in een *toegangs beheer lijst (ACL)*. Elk bestand en elke map in uw opslag account heeft een toegangs beheer lijst. Wanneer een beveiligings-principal een bewerking probeert uit te voeren op een bestand of map, bepaalt een ACL-controle of de beveiligingsprincipal (gebruiker, groep, Service-Principal of beheerde identiteit) het juiste machtigings niveau heeft om de bewerking te kunnen uitvoeren.

> [!NOTE]
> Acl's zijn alleen van toepassing op beveiligings-principals in dezelfde Tenant en zijn niet van toepassing op gebruikers die gebruikmaken van een gedeelde sleutel of SAS-token verificatie (Shared Access Signature). Dat komt doordat er geen identiteit aan de oproepende functie is gekoppeld en daarom de autorisatie op basis van machtigingen voor beveiliging niet kan worden uitgevoerd.  

## <a name="how-to-set-acls"></a>Acl's instellen

Zie een van de volgende artikelen voor het instellen van machtigingen voor bestands-en mapniveau:

| Omgeving | Artikel |
|--------|-----------|
|Azure Opslagverkenner |[Azure Storage Explorer gebruiken voor het beheren van adreslijsten, bestanden en ACL's in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[.NET gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md#manage-access-control-lists-acls)|
|Java|[Java gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md#manage-access-control-lists-acls)|
|Python|[Python gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md#manage-access-control-lists-acls)|
|PowerShell|[Power shell gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md#manage-access-control-lists-acls)|
|Azure CLI|[Azure CLI gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md#manage-access-control-lists-acls)|
|REST-API |[Pad-bijwerken](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Als de beveiligingsprincipal een *service* -principal is, is het belang rijk dat u de object-id van de Service-Principal gebruikt en niet de object-id van de gerelateerde app-registratie. Als u de object-ID van de Service-Principal wilt ophalen, opent u de Azure CLI en gebruikt u deze opdracht: `az ad sp show --id <Your App ID> --query objectId` . Zorg ervoor dat u de `<Your App ID>` tijdelijke aanduiding vervangt door de app-id van de app-registratie.

## <a name="types-of-acls"></a>Typen Acl's

Er zijn twee soorten toegangs beheer lijsten: *toegangs-acl's* en *standaard-acl's*.

Met toegangs-ACL's beheert u de toegang tot een object. Bestanden en mappen hebben beide Toegangs-ACL's.

Standaard-Acl's zijn sjablonen van Acl's die zijn gekoppeld aan een map die de toegangs-Acl's bepaalt voor alle onderliggende items die zijn gemaakt in die map. Bestanden hebben geen Standaard-ACL's.

Zowel toegangs-Acl's als standaard-Acl's hebben dezelfde structuur.

> [!NOTE]
> Het wijzigen van de standaard-ACL voor een bovenliggend item heeft geen invloed op de toegangs-ACL of de standaard-ACL van onderliggende items die al bestaan.

## <a name="levels-of-permission"></a>Machtigings niveaus

De machtigingen voor een container object zijn **lezen**, **schrijven** en **uitvoeren** en kunnen worden gebruikt voor bestanden en mappen, zoals wordt weer gegeven in de volgende tabel:

|            |    File     |   Directory |
|------------|-------------|----------|
| **Lezen (L)** | Kan de inhoud van een bestand lezen | Moet worden **gelezen** en **uitgevoerd** om de inhoud van de map weer te geven |
| **Schrijven (S)** | Kan schrijven of toevoegen aan een bestand | **Schrijven** en **uitvoeren** is vereist om onderliggende items in een map te maken |
| **Uitvoeren (U)** | Betekent niets in de context van Data Lake Storage Gen2 | Vereist om de onderliggende items van een map door te bladeren |

> [!NOTE]
> Als u machtigingen verleent met behulp van alleen Acl's (geen Azure RBAC) en vervolgens een beveiligings-principal Lees-of schrijf toegang tot een bestand verleent, moet u de beveiligings-principal **uitvoerings** machtigingen voor de container geven en aan elke map in de hiërarchie van mappen die tot het bestand leiden.

### <a name="short-forms-for-permissions"></a>Korte formulieren voor machtigingen

**LSU** wordt gebruikt om **Lezen + Schrijven + Uitvoeren** aan te geven. Er bestaat een verkorte numerieke vorm. Hierbij is **Lezen = 4**, **Schrijven = 2** en **Uitvoeren = 1**. De som van deze getallen vertegenwoordigt de machtigingen. Hier volgen enkele voorbeelden.

| Numerieke vorm | Verkorte vorm |      Wat het betekent     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lezen + Schrijven + Uitvoeren |
| 5            | `R-X`        | Lezen + Uitvoeren         |
| 4            | `R--`        | Lezen                   |
| 0            | `---`        | Geen machtigingen         |

### <a name="permissions-inheritance"></a>Overname van machtigingen

In het POSIX-stijl model dat wordt gebruikt door Data Lake Storage Gen2, worden machtigingen voor een item opgeslagen op het item zelf. Met andere woorden, machtigingen voor een item kunnen niet worden overgenomen van de bovenliggende items als de machtigingen zijn ingesteld nadat het onderliggende item al is gemaakt. Machtigingen worden alleen overgenomen als standaard machtigingen zijn ingesteld voor de bovenliggende items voordat de onderliggende items zijn gemaakt.

## <a name="common-scenarios-related-to-acl-permissions"></a>Algemene scenario's met betrekking tot ACL-machtigingen

In de volgende tabel ziet u de ACL-vermeldingen die zijn vereist voor het inschakelen van een beveiligingsprincipal voor het uitvoeren van de bewerkingen die worden vermeld in de kolom **bewerking** . 

In deze tabel ziet u een kolom van elk niveau van een fictieve Directory-hiërarchie. Er is een kolom voor de hoofd directory van de container ( `\` ), een submap met de naam **Oregon**, een submap van de Oregon-map met de naam **Rotterdam** en een tekst bestand in de map Rotterdam met de naam **Data.txt**. 

> [!IMPORTANT]
> In deze tabel wordt ervan uitgegaan dat u **alleen** acl's gebruikt zonder toewijzingen van Azure-functies. Zie [machtigingen tabel: combi neren van Azure RBAC en ACL](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl)voor een vergelijk bare tabel waarin Azure RBAC samen met acl's wordt gecombineerd.

|    Bewerking             |    /    | Oregon | Port land | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Data.txt lezen            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Toevoegen aan Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Data.txt verwijderen          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Data.txt maken          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Orderverzamellijst                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| /Oregon/weer geven           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| /Oregon/Portland/weer geven  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Schrijf machtigingen voor het bestand zijn niet vereist om het te verwijderen, zolang de vorige twee voor waarden waar zijn.

## <a name="users-and-identities"></a>Gebruikers en identiteiten

Elk bestand en elke map heeft afzonderlijke machtigingen voor deze identiteiten:

- De gebruiker die eigenaar is
- De groep die eigenaar is
- Benoemde gebruikers
- Benoemde groepen
- Benoemde service-principals
- Benoemde beheerde identiteiten
- Alle andere gebruikers

De identiteiten van gebruikers en groepen zijn Azure Active Directory-identiteiten (Azure AD). Tenzij anders vermeld, kan een *gebruiker* in de context van data Lake Storage Gen2 verwijzen naar een Azure AD-gebruiker, Service-Principal, beheerde identiteit of beveiligings groep.

### <a name="the-owning-user"></a>De gebruiker die eigenaar is

De gebruiker die het item heeft gemaakt, wordt automatisch de gebruiker die eigenaar is van het item. Een gebruiker die eigenaar is kan:

* De machtigingen wijzigen van een bestand waarvan hij eigenaar is.
* De groep die eigenaar van een bestand is wijzigen, zolang deze gebruiker ook lid is van de doelgroep.

> [!NOTE]
> De gebruiker die eigenaar is, kan de gebruiker die eigenaar is van een bestand of map *niet* wijzigen. Alleen super gebruikers kunnen de gebruiker die eigenaar is van een bestand of map wijzigen.

### <a name="the-owning-group"></a>De groep die eigenaar is

In de POSIX Acl's is elke gebruiker gekoppeld aan een *primaire groep*. Bijvoorbeeld: gebruiker Anja kan behoren tot de groep ' Finance '. Anja kan ook deel uitmaken van meerdere groepen, maar één groep wordt altijd aangeduid als de primaire groep. Wanneer Els in POSIX een bestand maakt, wordt de groep die eigenaar van het bestand is als haar hoofdgroep ingesteld. In dit geval is dit 'Financiën'. De groep die eigenaar is, gedraagt zich op dezelfde manier als toegewezen machtigingen voor andere gebruikers/groepen.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>De groep die eigenaar is toewijzen aan een nieuw bestand of nieuwe map

* Voor **Beeld 1**: de hoofdmap '/'. Deze map wordt gemaakt wanneer een Data Lake Storage Gen2-container wordt gemaakt. In dit geval wordt de groep die eigenaar is ingesteld op de gebruiker die de container heeft gemaakt als deze is uitgevoerd met behulp van OAuth. Als de container is gemaakt met behulp van gedeelde sleutel, een account-SAS of een service-SA'S, wordt de groep eigenaar en eigenaar ingesteld op **$superuser**.
* Voor **Beeld 2** (elk ander geval): wanneer een nieuw item wordt gemaakt, wordt de groep die eigenaar is gekopieerd van de bovenliggende map.

#### <a name="changing-the-owning-group"></a>De groep die eigenaar is wijzigen

De groep die eigenaar is kan worden gewijzigd door:
* Alle supergebruikers.
* De gebruiker die eigenaar is, als deze gebruiker ook lid is van de doelgroep.

> [!NOTE]
> De groep die eigenaar is, kan de Acl's van een bestand of map niet wijzigen.  Als de groep die eigenaar is, is ingesteld op de gebruiker die het account heeft gemaakt in het geval van de hoofdmap, bovenstaande **aanvraag 1** hierboven, is één gebruikers account niet geldig voor het opgeven van machtigingen via de groep die eigenaar is. U kunt deze machtiging toewijzen aan een geldige gebruikersgroep, indien van toepassing.

## <a name="access-check-algorithm"></a>Algoritme voor toegangscontrole

De volgende pseudocode vertegenwoordigt het algoritme voor toegangs controle voor opslag accounts.

```console
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
  # Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask isn't used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True 
        
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>Het masker

Zoals geïllustreerd in het algoritme voor toegangs controle, beperkt het masker de toegang voor benoemde gebruikers, de groep die eigenaar is en benoemde groepen.  

Voor een nieuwe Data Lake Storage Gen2-container wordt het masker voor de toegangs-ACL van de hoofdmap (/) standaard ingesteld op **750** voor directory's en **640** voor bestanden. In de volgende tabel ziet u de symbolische notatie van deze machtigings niveaus.

|Entiteit|Mappen|Bestanden|
|--|--|--|
|Gebruiker die eigenaar is|`rwx`|`r-w`|
|Groep die eigenaar is|`r-x`|`r--`|
|Overige|`---`|`---`|

Bestanden ontvangen niet de X-bit omdat deze niet van toepassing is op bestanden in een alleen-opslag systeem. 

Het masker kan worden opgegeven per oproep. Hierdoor kunnen verschillende verbruiks systemen, zoals clusters, verschillende efficiënte maskers hebben voor de bestands bewerkingen. Als voor een bepaalde aanvraag een masker is opgegeven, wordt het standaard masker volledig overschreven.

### <a name="the-sticky-bit"></a>De vergrendelde bit

De sticky bit is een meer geavanceerde functie van een POSIX-container. In de context van Data Lake Storage Gen2 is het niet waarschijnlijk dat de sticky-bit nodig is. Als de sticky bit is ingeschakeld in een directory, kan een onderliggend item alleen worden verwijderd of hernoemd door de gebruiker die eigenaar is van het onderliggende item.

De sticky bit wordt niet weer gegeven in de Azure Portal.

## <a name="default-permissions-on-new-files-and-directories"></a>Standaard machtigingen voor nieuwe bestanden en mappen

Wanneer een nieuw bestand of nieuwe map wordt gemaakt onder een bestaande map, bepaalt de standaard-ACL voor de bovenliggende map het volgende:

- De standaard-ACL en toegangs-ACL voor een onderliggende map.
- De toegangs-ACL van een onderliggend bestand (bestanden hebben geen standaard-ACL).

### <a name="umask"></a>umask

Bij het maken van een bestand of map wordt umask gebruikt om te wijzigen hoe de standaard-Acl's worden ingesteld voor het onderliggende item. umask is een 9-bits waarde op bovenliggende mappen met een LSU-waarde voor de **gebruiker die eigenaar** is, de groep die **eigenaar** is en **andere**.

De umask voor het Azure Data Lake Storage Gen2 van een constante waarde die is ingesteld op 007. Deze waarde wordt omgezet naar:

| onderdeel umask     | Numerieke vorm | Verkorte vorm | Betekenis |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Voor de gebruiker die eigenaar is, kopieert u de standaard-ACL van het bovenliggende object naar de toegangs-ACL van het onderliggende item | 
| umask.owning_group  |    0         |   `---`      | Kopieer voor de groep die eigenaar is de standaard-ACL van het bovenliggende object naar de toegangs-ACL van het onderliggende item | 
| umask. Overig         |    7         |   `RWX`      | Verwijder voor andere alle machtigingen voor de toegangs-ACL van het onderliggende item |

De waarde voor umask die wordt gebruikt door Azure Data Lake Storage Gen2 effectief betekent dat de waarde voor **Overige** nooit standaard wordt verzonden op nieuwe kinderen, tenzij er een standaard-ACL wordt gedefinieerd in de bovenliggende map. In dat geval wordt de umask in feite genegeerd en worden de machtigingen die zijn gedefinieerd door de standaard-ACL, toegepast op het onderliggende item. 

De volgende pseudocode laat zien hoe de umask wordt toegepast bij het maken van de Acl's voor een onderliggend item.

```console
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="faq"></a>Veelgestelde vragen

### <a name="do-i-have-to-enable-support-for-acls"></a>Moet ik ondersteuning voor ACL's inschakelen?

Nee. Toegangs beheer via Acl's is ingeschakeld voor een opslag account zolang de functie voor hiërarchische naam ruimte (HNS) is ingeschakeld.

Als HNS is uitgeschakeld, zijn de Azure-autorisatie regels voor Azure RBAC nog steeds van toepassing.

### <a name="what-is-the-best-way-to-apply-acls"></a>Wat is de beste manier om Acl's toe te passen?

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Hoe worden Azure RBAC en ACL-machtigingen geëvalueerd?

Zie [hoe machtigingen worden geëvalueerd](data-lake-storage-access-control-model.md#how-permissions-are-evaluated)voor meer informatie over hoe het systeem Azure RBAC en acl's samen evalueert om autorisatie beslissingen te nemen voor bronnen van opslag accounts.

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Wat zijn de limieten voor Azure-Roltoewijzingen en ACL-vermeldingen?

De volgende tabel bevat een samen vatting van de beperkingen waarmee u rekening moet houden tijdens het gebruik van Azure RBAC voor het beheren van ' grof gekorrelde ' machtigingen (machtigingen die van toepassing zijn op opslag accounts of containers) en het gebruik van Acl's voor het beheren van ' verfijnde ' machtigingen (machtigingen die van toepassing zijn op bestanden en mappen). Gebruik beveiligings groepen voor ACL-toewijzingen. Door groepen te gebruiken, bent u minder waarschijnlijk het maximum aantal roltoewijzingen per abonnement en het maximum aantal Acl's-vermeldingen per bestand of map te overschrijden. 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Ondersteunt Data Lake Storage Gen2 overname van Azure RBAC?

Toewijzingen van Azure-rollen nemen toe. Toewijzingen stroomt van de resources van het abonnement, de resource groep en het opslag account naar de container resource.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>Ondersteunt Data Lake Storage Gen2 overname van Acl's?

Standaard-Acl's kunnen worden gebruikt om Acl's in te stellen voor nieuwe onderliggende submappen en bestanden die zijn gemaakt in de bovenliggende map. Als u Acl's voor bestaande onderliggende items wilt bijwerken, moet u de Acl's voor de gewenste Directory hiërarchie recursief toevoegen, bijwerken of verwijderen. Zie [acl's (Access Control Lists) recursief instellen voor Azure data Lake Storage Gen2](recursive-access-control-lists.md)voor meer informatie. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Welke machtigingen zijn vereist voor het recursief verwijderen van een map en de inhoud ervan?

- De aanroeper heeft machtigingen voor super gebruiker,

of

- De bovenliggende map moet de machtigingen schrijven + uitvoeren hebben.
- Voor de map die moet worden verwijderd en voor elke map erin zijn de machtigingen lezen + schrijven + uitvoeren vereist.

> [!NOTE]
> U hebt geen schrijf machtigingen nodig voor het verwijderen van bestanden in mappen. De hoofdmap '/' kan ook nooit worden verwijderd.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Wie is de eigenaar van een bestand of map?

De maker van een bestand of map wordt de eigenaar. In het geval van de hoofdmap is dit de identiteit van de gebruiker die de container heeft gemaakt.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Welke groep wordt ingesteld als de groep die eigenaar is van een bestand of map bij het maken?

De groep die eigenaar is, wordt gekopieerd uit de groep die eigenaar is van de bovenliggende map waaronder het nieuwe bestand of de map wordt gemaakt.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Ik ben de gebruiker die eigenaar is van een bestand, maar ik heb niet de LSU-machtigingen die ik nodig heb. Wat moet ik doen?

De gebruiker die eigenaar is kan de machtigingen van het bestand wijzigen en zichzelf de vereiste LSU-machtigingen geven.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Waarom zie ik soms GUID'S in Acl's?

Er wordt een GUID weer gegeven als de vermelding een gebruiker vertegenwoordigt en die gebruiker niet meer bestaat in azure AD. Dit gebeurt doorgaans wanneer gebruikers het bedrijf verlaten of hun accounts zijn verwijderd in Azure AD. Daarnaast hebben service-principals en beveiligings groepen geen UPN (User Principal Name) om ze te identificeren en worden ze vertegenwoordigd door hun OID-kenmerk (een GUID).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Zijn de Acl's voor een Service-Principal Hoe kan ik correct ingesteld?

Wanneer u Acl's voor service-principals definieert, is het belang rijk dat u de object-ID (OID) van de *Service-Principal* gebruikt voor de app-registratie die u hebt gemaakt. Het is belang rijk te weten dat geregistreerde apps een afzonderlijke service-principal hebben in de specifieke Azure AD-Tenant. Geregistreerde apps hebben een OID die zichtbaar is in de Azure Portal, maar de *Service-Principal* heeft een andere (andere) OID.

Als u de OID wilt ophalen voor de service-principal die overeenkomt met een app-registratie, kunt u de `az ad sp show` opdracht gebruiken. Geef de toepassings-ID op als de para meter. Hier volgt een voor beeld voor het verkrijgen van de OID voor de service-principal die overeenkomt met een app-registratie met App ID = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Voer de volgende opdracht uit in de Azure CLI:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

OID wordt weer gegeven.

Wanneer u de juiste OID voor de Service-Principal hebt, gaat u naar de Storage Explorer Access-pagina **beheren** om het OID toe te voegen en de juiste machtigingen voor de OID toe te wijzen. Zorg ervoor dat u **Opslaan** selecteert.

### <a name="can-i-set-the-acl-of-a-container"></a>Kan ik de ACL van een container instellen?

Nee. Een container heeft geen ACL. U kunt echter de ACL van de hoofdmap van de container instellen. Elke container heeft een hoofdmap en deelt dezelfde naam als de container. Als de naam van de container bijvoorbeeld heet `my-container` , heet de hoofdmap `myContainer/` . 

Het Azure Storage REST API bevat een bewerking met de naam [set-container-ACL](/rest/api/storageservices/set-container-acl), maar deze bewerking kan niet worden gebruikt om de ACL van een container of de hoofdmap van een container in te stellen. In plaats daarvan wordt deze bewerking gebruikt om aan te geven of blobs in een container [openbaar mogen worden geopend](anonymous-read-access-configure.md). 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Waar kan ik meer informatie over het POSIX-model voor toegangsbeheer?

* [POSIX met behulp van toegangsbeheerlijsten op Linux](https://www.linux.com/news/posix-acls-linux)
* [De machtigings gids HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [Veelgestelde vragen over POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL in Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL met behulp van toegangs beheer lijsten op Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Zie ook

- [Access Control model in Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)

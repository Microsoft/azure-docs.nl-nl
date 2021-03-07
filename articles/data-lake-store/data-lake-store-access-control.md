---
title: Overzicht van Access Control in Data Lake Storage Gen1 | Microsoft Docs
description: Meer informatie over de basis principes van het toegangs beheer model van Azure Data Lake Storage Gen1, dat is afgeleid van HDFS.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: aa0da5721c577957b101ac8a2d9346c0536f0a88
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424135"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Toegangsbeheer in Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementeert een model voor toegangs beheer dat is afgeleid van HDFS. Dit is op zijn beurt afgeleid van het POSIX-toegangs beheer model. Dit artikel bevat een overzicht van de basis beginselen van het toegangs beheer model voor Data Lake Storage Gen1. 

## <a name="access-control-lists-on-files-and-folders"></a>Toegangsbeheerlijsten voor bestanden en mappen

Er zijn twee soorten toegangsbeheerlijsten (ACL's): **Toegangs-ACL's** en **Standaard-ACL's**.

* **Toegangs-ACL's**: deze beheren de toegang tot een object. Bestanden en mappen hebben Toegangs-ACL's.

* **Standaard-ACL's**: een 'sjabloon' van ACL's die zijn gekoppeld aan een map die de Toegangs-ACL's bepaalt voor alle onderliggende items die zijn gemaakt onder die map. Bestanden hebben geen Standaard-ACL's.


Toegangs-ACL's en Standaard-ACL's hebben dezelfde structuur.

> [!NOTE]
> Als u de Standaard-ACL voor een bovenliggend item wijzigt, heeft dit geen invloed op de Toegangs-ACL of de Standaard ACL van bestaande onderliggende items.
>
>

## <a name="permissions"></a>Machtigingen

De machtigingen voor een bestandssysteemobject zijn **Lezen**, **Schrijven** en **Uitvoeren**. Deze kunnen worden gebruikt voor bestanden en mappen zoals weergegeven in de onderstaande tabel:

|            |    File     |   Map |
|------------|-------------|----------|
| **Lezen (L)** | Kan de inhoud van een bestand lezen | Moet worden **gelezen** en **uitgevoerd** om de inhoud van de map weer te geven|
| **Schrijven (S)** | Kan schrijven of toevoegen aan een bestand | **Schrijven** en **Uitvoeren** zijn vereist om onderliggende items in een map te maken |
| **Uitvoeren (U)** | Betekent niets in de context van Data Lake Storage Gen1 | Vereist om de onderliggende items van een map door te gaan |

### <a name="short-forms-for-permissions"></a>Korte formulieren voor machtigingen

**LSU** wordt gebruikt om **Lezen + Schrijven + Uitvoeren** aan te geven. Er bestaat een verkorte numerieke vorm. Hierbij is **Lezen = 4**, **Schrijven = 2** en **Uitvoeren = 1**. De som van deze getallen vertegenwoordigt de machtigingen. Hier volgen enkele voorbeelden.

| Numerieke vorm | Verkorte vorm |      Wat het betekent     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Lezen + Schrijven + Uitvoeren |
| 5            | `R-X`        | Lezen + Uitvoeren         |
| 4            | `R--`        | Lezen                   |
| 0            | `---`        | Geen machtigingen         |


### <a name="permissions-do-not-inherit"></a>Machtigingen worden niet overgenomen

In het POSIX-stijl model dat wordt gebruikt door Data Lake Storage Gen1, worden machtigingen voor een item opgeslagen op het item zelf. Machtigingen voor een item kunnen dus niet worden overgenomen van de bovenliggende items.

## <a name="common-scenarios-related-to-permissions"></a>Algemene scenario's met machtigingen

Hieronder volgen enkele algemene scenario's die u helpen te begrijpen welke machtigingen nodig zijn om bepaalde bewerkingen uit te voeren op een Data Lake Storage Gen1-account.

| Bewerking | Object              |    /      | Den   | Port land   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Lezen      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Toevoegen aan | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `-W-`          |
| Verwijderen    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Maken    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lijst      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lijst      | Den           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lijst      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> Schrijfmachtigingen zijn niet vereist voor het verwijderen van het bestand als aan de eerder genoemde twee voorwaarden wordt voldaan.
>
>


## <a name="users-and-identities"></a>Gebruikers en identiteiten

Alle bestanden en mappen beschikken over verschillende machtigingen voor de volgende identiteiten:

* De gebruiker die eigenaar is
* De groep die eigenaar is
* Benoemde gebruikers
* Benoemde groepen
* Alle andere gebruikers

De identiteiten van gebruikers en groepen zijn Azure Active Directory-identiteiten (Azure AD). Tenzij anders vermeld, kan een ' gebruiker ' in de context van Data Lake Storage Gen1 een Azure AD-gebruiker of een Azure AD-beveiligings groep betekenen.

### <a name="the-super-user"></a>De supergebruiker

Een super gebruiker heeft de meeste rechten van alle gebruikers in het Data Lake Storage Gen1-account. Een supergebruiker:

* heeft LSU-machtigingen voor **alle** bestanden en mappen,
* kan de machtigingen voor elk bestand en elke map wijzigen,
* kan de eigenaar of groep die eigenaar is van een bestand of map wijzigen.

Alle gebruikers die deel uitmaken van de rol **eigen aren** voor een Data Lake Storage gen1 account, zijn automatisch een super gebruiker.

### <a name="the-owning-user"></a>De gebruiker die eigenaar is

De gebruiker die het item heeft gemaakt, wordt automatisch de gebruiker die eigenaar is van het item. Een gebruiker die eigenaar is kan:

* De machtigingen wijzigen van een bestand waarvan hij eigenaar is.
* De groep die eigenaar van een bestand is wijzigen, zolang deze gebruiker ook lid is van de doelgroep.

> [!NOTE]
> De gebruiker die eigenaar is, kan de gebruiker die eigenaar van een bestand of map is *niet* wijzigen. Alleen supergebruikers kunnen de gebruiker die eigenaar is van een bestand of map wijzigen.
>
>

### <a name="the-owning-group"></a>De groep die eigenaar is

**Achtergrond**

In de POSIX ACL's is elke gebruiker gekoppeld aan een 'hoofdgroep'. Gebruiker 'Els' kan bijvoorbeeld behoren tot de groep 'Financiën'. Els kan behoren tot meerdere groepen, maar één groep is altijd ingesteld als haar hoofdgroep. Wanneer Els in POSIX een bestand maakt, wordt de groep die eigenaar van het bestand is als haar hoofdgroep ingesteld. In dit geval is dit 'Financiën'. De groep die eigenaar is, gedraagt zich op dezelfde manier als toegewezen machtigingen voor andere gebruikers/groepen.

Omdat er geen ' primaire groep ' is gekoppeld aan gebruikers in Data Lake Storage Gen1, wordt de groep die eigenaar is, toegewezen als hieronder.

**De groep die eigenaar is toewijzen aan een nieuw bestand of nieuwe map**

* **Voorbeeld 1**: de hoofdmap '/'. Deze map wordt gemaakt wanneer een Data Lake Storage Gen1-account wordt gemaakt. In dit geval wordt de groep die eigenaar is ingesteld op een GUID met de waarde all-Zero.  Deze waarde staat geen toegang toe.  Het is een tijdelijke aanduiding totdat een groep is toegewezen.
* **Voorbeeld 2** (alle andere gevallen): wanneer een nieuw item wordt gemaakt, wordt de groep die eigenaar is, gekopieerd van de bovenliggende map.

**De groep die eigenaar is wijzigen**

De groep die eigenaar is kan worden gewijzigd door:
* Alle supergebruikers.
* De gebruiker die eigenaar is, als deze gebruiker ook lid is van de doelgroep.

> [!NOTE]
> De groep die eigenaar is, kan de ACL's van een bestand of map *niet* wijzigen.
>
> Voor accounts die zijn gemaakt op of vóór 1 september 2018, is de groep die eigenaar is ingesteld op de gebruiker die het account heeft gemaakt in het geval van de hoofdmap voor **Case 1** hierboven.  Eén gebruikers account is niet geldig voor het opgeven van machtigingen via de groep die eigenaar is. Daarom worden er geen machtigingen verleend door deze standaard instelling. U kunt deze machtiging toewijzen aan een geldige gebruikers groep.


## <a name="access-check-algorithm"></a>Algoritme voor toegangscontrole

De volgende pseudocode vertegenwoordigt het toegangs controle algoritme voor Data Lake Storage Gen1 accounts.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
      member_count += 1
      perms | =  entry.permissions
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>Het masker

Zoals geïllustreerd in het algoritme voor toegangs controle, beperkt het masker de toegang voor **benoemde gebruikers**, de **groep die eigenaar** is en **benoemde groepen**.  

> [!NOTE]
> Voor een nieuw Data Lake Storage Gen1-account wordt het masker voor de toegangs-ACL van de hoofdmap (/) standaard ingesteld op LSU.
>
>

### <a name="the-sticky-bit"></a>De vergrendelde bit

De vergrendelde bit is een geavanceerdere functie van een POSIX-bestandssysteem. In de context van Data Lake Storage Gen1 is het niet waarschijnlijk dat de sticky-bit nodig is. Als de sticky bit is ingeschakeld voor een map, kunt u een onderliggend item alleen verwijderen of de naam ervan wijzigen door de gebruiker die eigenaar is van het onderliggende item.

De vergrendelde bit wordt niet weergegeven in Azure Portal.

## <a name="default-permissions-on-new-files-and-folders"></a>Standaard machtigingen voor nieuwe bestanden en mappen

Wanneer een nieuw bestand of een nieuwe map wordt gemaakt onder een bestaande map, bepaalt de Standaard-ACL voor de bovenliggende map:

- De Standaard-ACL en Toegangs-ACL voor een onderliggende map.
- De Toegangs-ACL van een onderliggend bestand (bestanden beschikken niet over een Standaard-ACL).

### <a name="umask"></a>umask

Bij het maken van een bestand of map wordt umask gebruikt om te wijzigen hoe de standaard-Acl's worden ingesteld voor het onderliggende item. umask is een 9-bits waarde voor bovenliggende mappen die een LSU-waarde bevat voor de **gebruiker**, de **groep die eigenaar** is en **andere**.

De umask voor Azure Data Lake Storage Gen1 is een constante waarde die is ingesteld op 007. Deze waarde wordt omgezet in

| onderdeel umask     | Numerieke vorm | Verkorte vorm | Betekenis |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Voor de gebruiker die eigenaar is, kopieert u de standaard-ACL van het bovenliggende object naar de toegangs-ACL van het onderliggende item | 
| umask.owning_group  |    0         |   `---`      | Kopieer voor de groep die eigenaar is de standaard-ACL van het bovenliggende object naar de toegangs-ACL van het onderliggende item | 
| umask. Overig         |    7         |   `RWX`      | Verwijder voor andere alle machtigingen voor de toegangs-ACL van het onderliggende item |

De waarde voor umask die wordt gebruikt door Azure Data Lake Storage Gen1 effectief betekent dat de waarde voor overige nooit standaard wordt verzonden op nieuwe kinderen, ongeacht wat de standaard-ACL aangeeft. 

De volgende pseudocode laat zien hoe de umask wordt toegepast bij het maken van de Acl's voor een onderliggend item.

```
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

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Veelgestelde vragen over Acl's in Data Lake Storage Gen1

### <a name="do-i-have-to-enable-support-for-acls"></a>Moet ik ondersteuning voor ACL's inschakelen?

Nee. Toegangs beheer via Acl's is altijd ingeschakeld voor een Data Lake Storage Gen1-account.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Welke machtigingen zijn vereist voor het recursief verwijderen van een map en de inhoud ervan?

* De bovenliggende map moet de machtigingen **Schrijven + Uitvoeren** hebben.
* De map die wordt verwijderd en elke map daarin moeten de machtigingen **Lezen + Schrijven + Uitvoeren** hebben.

> [!NOTE]
> U hebt geen schrijfmachtigingen nodig voor het verwijderen van bestanden in mappen. De hoofdmap '/' kan ook **nooit** worden verwijderd.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Wie is de eigenaar van een bestand of map?

De maker van een bestand of map wordt de eigenaar.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Welke groep wordt ingesteld als eigenaar van een bestand of map bij het maken ervan?

De groep wordt gekopieerd uit de groep die eigenaar is van de bovenliggende map waaronder het nieuwe bestand of de nieuwe map wordt gemaakt.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Ik ben de gebruiker die eigenaar is van een bestand, maar ik heb de LSU-machtigingen die ik nodig heb niet. Wat moet ik doen?

De gebruiker die eigenaar is kan de machtigingen van het bestand wijzigen en zichzelf de vereiste LSU-machtigingen geven.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Wanneer ik ACL's bekijk in Azure Portal, zie ik gebruikersnamen, maar via API's zie ik GUID's. Hoe kan dat?

Vermeldingen worden in de ACL's opgeslagen als GUID's die zijn gekoppeld aan gebruikers in Azure AD. De API's geven de GUID's als zodanig weer. We proberen ACL's in Azure Portal eenvoudiger in gebruik te maken door de GUID's wanneer mogelijk om te zetten in beschrijvende namen.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Waarom zie ik soms GUID's in de ACL's wanneer ik Azure Portal gebruik?

Er wordt een GUID weergegeven wanneer een gebruiker niet meer bestaat in Azure AD. Dit gebeurt doorgaans wanneer gebruikers het bedrijf verlaten of hun accounts zijn verwijderd in Azure AD. Zorg er ook voor dat u de juiste ID gebruikt voor het instellen van Acl's (hieronder genoemde details).

### <a name="when-using-service-principal-what-id-should-i-use-to-set-acls"></a>Welke ID moet ik gebruiken om Acl's in te stellen wanneer de service-principal wordt gebruikt?

Ga in azure Portal naar **Azure Active Directory-> bedrijfs toepassingen** en selecteer uw toepassing. Op het tabblad **overzicht** moet een object-id worden weer gegeven. Dit is wat er moet worden gebruikt bij het toevoegen van acl's voor gegevens toegang (en geen toepassings-id).

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>Ondersteunt Data Lake Storage Gen1 overname van Acl's?

Nee, maar Standaard ACL's kunnen worden gebruikt voor het instellen van ACL's voor onderliggende bestanden en mappen die nieuw zijn gemaakt onder de bovenliggende map.

### <a name="what-are-the-limits-for-acl-entries-on-files-and-folders"></a>Wat zijn de limieten voor ACL-vermeldingen voor bestanden en mappen?

32 Acl's kunnen per bestand en per map worden ingesteld. De toegangs-en standaard-Acl's hebben elk een eigen 32 ACL-ingangs limiet. Gebruik beveiligings groepen voor ACL-toewijzingen, indien mogelijk. Door groepen te gebruiken, bent u minder waarschijnlijk het maximum aantal ACL'S-vermeldingen per bestand of map te overschrijden.

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

* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)

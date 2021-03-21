---
title: Access Control model voor Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over het configureren van toegang tot containers, mappen en bestands niveau in accounts met een hiërarchische naam ruimte.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: 9fa7f586db5a32640c16db5802b56d6481e8889d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439286"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Access Control model in Azure Data Lake Storage Gen2

Data Lake Storage Gen2 ondersteunt de volgende autorisatie mechanismen:

- Verificatie van gedeelde sleutel
- Shared Access Signature (SAS)-autorisatie
- Op rollen gebaseerd toegangs beheer (Azure RBAC)
- Toegangsbeheerlijsten (ACL)

[Gedeelde sleutel en SAS-autorisatie](#shared-key-and-shared-access-signature-sas-authorization) verlenen toegang tot een gebruiker (of toepassing) zonder dat ze een identiteit hoeven te hebben in azure Active Directory (Azure AD). Met deze twee vormen van verificatie hebben Azure RBAC en Acl's geen effect.

Voor Azure RBAC en ACL moet de gebruiker (of toepassing) een identiteit hebben in azure AD.  Met Azure RBAC kunt u ' grove ' toegang verlenen tot gegevens van een opslag account, zoals lees-of schrijf toegang tot alle gegevens in een opslag account, terwijl Acl's u **de** mogelijkheid bieden ' nauw bestemde ' toegang te verlenen, zoals schrijf toegang tot een specifieke map of bestand.  

Dit artikel is gericht op Azure RBAC en Acl's, en hoe het systeem ze samen evalueert om autorisatie beslissingen te nemen voor de resources van het opslag account.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Op rollen gebaseerd toegangs beheer (Azure RBAC)

Azure RBAC gebruikt roltoewijzingen om machtigingen sets toe te passen op [beveiligings-principals](../../role-based-access-control/overview.md#security-principal). Een beveiligingsprincipal is een object dat staat voor een gebruiker, groep, Service-Principal of beheerde identiteit die is gedefinieerd in Azure Active Directory (AD). Een machtigingenset kan een beveiligings-principal een "grof korrelf" toegangs niveau geven, zoals lees-of schrijf toegang tot **alle** gegevens in een opslag account of **alle** gegevens in een container. 

Met de volgende rollen wordt een beveiligingsprincipal toegestaan om toegang te krijgen tot gegevens in een opslag account.

|Rol|Beschrijving|
|--|--|
| [Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Volledige toegang tot Blob Storage-containers en-gegevens. Met deze toegang kan de beveiligingsprincipal de eigenaar een item instellen en de Acl's van alle items wijzigen. |
| [Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Lees-, schrijf-en verwijder toegang tot Blob Storage-containers en-blobs. Deze toegang staat de beveiligingsprincipal niet toe het eigendom van een item in te stellen, maar kan de toegangs beheer lijst van items wijzigen die eigendom zijn van de beveiligingsprincipal. |
| [Lezer voor opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Containers en blobs voor Blob-opslag lezen en weer geven. |

Rollen zoals een [eigenaar](../../role-based-access-control/built-in-roles.md#owner), [bijdrager](../../role-based-access-control/built-in-roles.md#contributor), [lezer](../../role-based-access-control/built-in-roles.md#reader)en [Inzender voor opslag accounts](../../role-based-access-control/built-in-roles.md#storage-account-contributor) geven een beveiligingsprincipal een beveiligings-principal toe om een opslag account te beheren, maar bieden geen toegang tot de gegevens in dat account. Deze rollen (exclusief **lezer**) kunnen echter wel toegang krijgen tot de opslag sleutels die kunnen worden gebruikt in verschillende client hulpprogramma's voor toegang tot de gegevens.

## <a name="access-control-lists-acls"></a>ACL’s (toegangsbeheerlijsten)

Met Acl's beschikt u over de mogelijkheid om "fijn korrelig" toegangs niveau toe te passen op mappen en bestanden. Een *ACL* is een machtigings constructie met een reeks *ACL-vermeldingen*. Elke ACL-vermelding koppelt een beveiligings-principal met een toegangs niveau.  Zie [toegangs beheer lijsten (acl's) in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie.

## <a name="how-permissions-are-evaluated"></a>Hoe machtigingen worden geëvalueerd

Tijdens autorisatie op basis van een beveiligings-principal worden de machtigingen in de volgende volg orde geëvalueerd.

: One: &nbsp; &nbsp; Azure-roltoewijzingen worden eerst geëvalueerd en krijgen voor rang op acl's-toewijzingen.

: twee: &nbsp; &nbsp; als de bewerking volledig is geautoriseerd op basis van de toewijzing van Azure-rollen, worden de acl's helemaal niet geëvalueerd.

: drie: &nbsp; &nbsp; als de bewerking niet volledig is geautoriseerd, worden de acl's geëvalueerd.

> [!div class="mx-imgBorder"]
> ![Data Lake Storage-machtigings stroom](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

Vanwege de manier waarop toegangs machtigingen worden geëvalueerd door het systeem, kunt u **geen** ACL gebruiken om de toegang te **beperken** die al is toegekend door een roltoewijzing. Dat komt doordat de toewijzingen van Azure-rollen eerst worden geëvalueerd en als de toewijzing voldoende toegangs machtigingen verleent, worden Acl's genegeerd. 

In het volgende diagram ziet u de machtigings stroom voor drie veelvoorkomende bewerkingen: Mapinhoud weer geven, een bestand lezen en een bestand schrijven.

> [!div class="mx-imgBorder"]
> ![voor beeld van data Lake Storage-machtigings stroom](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Machtigingen tabel: combi neren van Azure RBAC en ACL

In de volgende tabel ziet u hoe u Azure-rollen en ACL-vermeldingen kunt combi neren zodat een beveiligingsprincipal de bewerkingen kan uitvoeren die worden vermeld in de kolom **bewerking** . In deze tabel ziet u een kolom van elk niveau van een fictieve Directory-hiërarchie. Er is een kolom voor de hoofd directory van de container ( `/` ), een submap met de naam **Oregon**, een submap van de Oregon-map met de naam **Rotterdam** en een tekst bestand in de map Rotterdam met de naam **Data.txt**. Deze kolommen worden weer gegeven in [korte vormige](data-lake-storage-access-control.md#short-forms-for-permissions) representaties van de ACL-vermelding die is vereist om machtigingen te verlenen. **N.v.t.** (_niet van toepassing_) wordt weer gegeven in de kolom als een ACL-vermelding niet vereist is om de bewerking uit te voeren.

|    Bewerking             | Toegewezen Azure-rol               |    /        | Oregon     | Port land | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Data.txt lezen            |   Eigenaar van opslagblobgegevens        | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |  
|                          |   Inzender voor Storage Blob-gegevens  | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Lezer voor opslagblobgegevens       | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Geen                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Toevoegen aan Data.txt       |   Eigenaar van opslagblobgegevens        | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Inzender voor Storage Blob-gegevens  | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Lezer voor opslagblobgegevens       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Geen                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Data.txt verwijderen          |   Eigenaar van opslagblobgegevens        | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Inzender voor Storage Blob-gegevens  | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Lezer voor opslagblobgegevens       | `--X`    | `--X`    | `-WX`     | N.v.t.    |
|                          |   Geen                           | `--X`    | `--X`    | `-WX`     | N.v.t.    |
| Data.txt maken          |   Eigenaar van opslagblobgegevens        | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Inzender voor Storage Blob-gegevens  | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Lezer voor opslagblobgegevens       | `--X`    | `--X`    | `-WX`     | N.v.t.    |
|                          |   Geen                           | `--X`    | `--X`    | `-WX`     | N.v.t.    |
| Orderverzamellijst                   |   Eigenaar van opslagblobgegevens        | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Inzender voor Storage Blob-gegevens  | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Lezer voor opslagblobgegevens       | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Geen                           | `R-X`    | N.v.t.      | N.v.t.       | N.v.t.    |
| /Oregon/weer geven            |   Eigenaar van opslagblobgegevens        | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Inzender voor Storage Blob-gegevens  | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Lezer voor opslagblobgegevens       | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Geen                           | `--X`    | `R-X`    | N.v.t.       | N.v.t.    |
| /Oregon/Portland/weer geven   |   Eigenaar van opslagblobgegevens        | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Inzender voor Storage Blob-gegevens  | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Lezer voor opslagblobgegevens       | N.v.t.      | N.v.t.      | N.v.t.       | N.v.t.    |
|                          |   Geen                           | `--X`    | `--X`    | `R-X`     | N.v.t.    |


> [!NOTE] 
> Als u de inhoud van een container in Azure Storage Explorer wilt weer geven, moeten beveiligings-principals [zich aanmelden bij Storage Explorer met behulp van Azure AD](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)en (mini maal) Lees toegang (R--) hebben voor de hoofdmap ( `\` ) van een container. Dit machtigings niveau biedt hen de mogelijkheid om de inhoud van de hoofdmap weer te geven. Als u niet wilt dat de inhoud van de hoofdmap wordt weer gegeven, kunt u de rol van de map [lezer](../../role-based-access-control/built-in-roles.md#reader) toewijzen. Met deze rol kunnen ze de containers in het account weer geven, maar niet de inhoud van de container. Vervolgens kunt u toegang verlenen tot specifieke mappen en bestanden met behulp van Acl's.   

## <a name="security-groups"></a>Beveiligingsgroepen

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Limieten voor Azure-Roltoewijzingen en ACL-vermeldingen

Door groepen te gebruiken, bent u minder waarschijnlijk het maximum aantal roltoewijzingen per abonnement en het maximum aantal ACL'S-vermeldingen per bestand of map te overschrijden. Deze limieten worden in de volgende tabel beschreven.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Gedeelde sleutel-en Shared Access Signature-autorisatie (SAS)

Azure Data Lake Storage Gen2 biedt ook ondersteuning voor [gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key) -en [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) -methoden voor verificatie. Een kenmerk van deze verificatie methoden is dat er geen identiteit aan de oproepende functie is gekoppeld en daarom niet op basis van machtigingen voor de machtigings bevoegdheid voor beveiliging kan worden uitgevoerd.

In het geval van een gedeelde sleutel krijgt de aanroeper effectief toegang tot de supergebruikers, wat betekent dat volledige toegang tot alle bewerkingen voor alle resources, inclusief gegevens, het instellen van de eigenaar en het wijzigen van Acl's.

SAS-tokens bevatten toegestane machtigingen als onderdeel van het token. De machtigingen die zijn opgenomen in het SAS-token worden effectief toegepast op alle autorisatie beslissingen, maar er worden geen extra ACL'S-controles uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Zie  [toegangs beheer lijsten (acl's) in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangscontrole lijsten.
---
title: T-SQL-opdrachten blok keren voor het maken of wijzigen van Azure SQL-resources
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: In dit artikel vindt u informatie over een functie waarmee Azure-beheerders T-SQL-opdrachten kunnen blok keren voor het maken of wijzigen van Azure SQL-resources
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556096"
---
# <a name="what-is-block-t-sql-crud-feature"></a>Wat is de functie voor blok-T-SQL-ruw?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Met deze functie kunnen Azure-beheerders het maken of aanpassen van Azure SQL-resources via T-SQL blok keren. Dit wordt afgedwongen op abonnements niveau om T-SQL-opdrachten te blok keren om SQL-resources in een Azure SQL database of een beheerd exemplaar te beïnvloeden.

## <a name="overview"></a>Overzicht

Als u het maken of wijzigen van resources via T-SQL wilt blok keren en resource beheer wilt afdwingen via een Azure Resource Manager sjabloon (ARM-sjabloon) voor een bepaald abonnement, kunt u de preview-functies op abonnements niveau in Azure Portal gebruiken. Dit is vooral handig wanneer u Azure- [beleid](/azure/governance/policy/overview) gebruikt om organisatie standaarden af te dwingen via arm-sjablonen. Omdat T-SQL niet voldoet aan het Azure-beleid, kan een blok op T-SQL CREATE-of Modify-bewerkingen worden toegepast. De syntaxis is geblokkeerd inclusief ruwe (Create-, update-, Delete)-instructies voor data bases in Azure SQL, specifiek `CREATE DATABASE` , `ALTER DATABASE` en `DROP DATABASE` instructies. 

RUWE bewerkingen van T-SQL kunnen worden geblokkeerd via Azure Portal, [Power shell](/powershell/module/az.resources/register-azproviderfeature)of [Azure cli](/cli/azure/feature#az_feature_register).

## <a name="permissions"></a>Machtigingen

Als u deze functie wilt registreren of verwijderen, moet de Azure-gebruiker lid zijn van de rol eigenaar of Inzender van het abonnement.

## <a name="examples"></a>Voorbeelden

In de volgende sectie wordt beschreven hoe u een preview-functie kunt registreren of verwijderen bij de resource provider micro soft. SQL in Azure Portal: 

### <a name="register-block-t-sql-crud"></a>Blok keren T-SQL-ruw registreren

1. Ga naar uw abonnement op Azure Portal.
2. Selecteer op het tabblad **Preview-functies** . 
3. Selecteer **blok keren T-SQL-ruw**.
4. Nadat u op **blok T-SQL ruw** hebt geselecteerd, wordt er een nieuw venster geopend, selecteert u **registreren** om dit blok te registreren bij micro soft. SQL-resource provider.

![Selecteer "T-SQL-ruw blok keren" in de lijst met preview-functies](./media/block-tsql-crud/block-tsql-crud.png)

![Selecteer bij ' blok-T-SQL ruw ' de optie registreren](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>De resource provider micro soft. SQL opnieuw registreren 
Nadat u het blok van T-SQL ruw hebt geregistreerd bij de resource provider micro soft. SQL, moet u de resource provider micro soft. SQL opnieuw registreren om de wijzigingen van kracht te laten worden. De resource provider micro soft. SQL opnieuw registreren:

1. Ga naar uw abonnement op Azure Portal.
2. Selecteer op het tabblad **resource providers** .
3. Zoek en selecteer **micro soft. SQL** -resource provider.
4. Selecteer **opnieuw registreren**. 

> [!NOTE]
> De stap voor opnieuw registreren is verplicht voor het T-SQL-blok dat moet worden toegepast op uw abonnement. 

![De resource provider micro soft. SQL opnieuw registreren](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>Blok-T-SQL-ruw verwijderen
Als u het blok wilt verwijderen op basis van T-SQL-bewerkingen voor maken of wijzigen van uw abonnement, moet u eerst de registratie van het eerder geregistreerde T-SQL-blok opheffen. Registreer vervolgens de resource provider micro soft. SQL zoals hierboven weer gegeven om het verwijderen van T-SQL-blok van kracht te laten worden. 


## <a name="next-steps"></a>Volgende stappen

- [Een overzicht van Azure SQL Database beveiligings mogelijkheden](security-overview.md)
- [Aanbevolen procedures voor Azure SQL Database beveiliging](security-best-practice.md)

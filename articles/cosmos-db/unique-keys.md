---
title: Unieke sleutels gebruiken in Azure Cosmos DB
description: Meer informatie over het definiëren en gebruiken van unieke sleutels voor een Azure Cosmos-data base. In dit artikel wordt ook beschreven hoe unieke sleutels een laag van gegevens integriteit toevoegen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 9eb2b916bfe6c73a1535afb077b04fbb081dd5f1
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685717"
---
# <a name="unique-key-constraints-in-azure-cosmos-db"></a>Beperkingen voor unieke sleutels in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Unieke sleutels voegen een laag van gegevens integriteit toe aan een Azure Cosmos-container. U maakt een uniek sleutel beleid wanneer u een Azure Cosmos-container maakt. Met unieke sleutels, zorgt u ervoor dat een of meer waarden binnen een logische partitie uniek zijn. U kunt ook uniekheid per [partitie sleutel](partitioning-overview.md)garanderen.

Nadat u een container met een beleid met unieke sleutels hebt gemaakt, wordt het maken van een nieuwe of een update van een bestaand item dat resulteert in een duplicaat in een logische partitie, voor komen, zoals opgegeven door de beperking van de unieke sleutel. De partitie sleutel in combi natie met de unieke sleutel garandeert de uniekheid van een item binnen het bereik van de container.

Denk bijvoorbeeld aan een Azure Cosmos-container met `Email address` als de unieke sleutel beperking en `CompanyID` als de partitie sleutel. Wanneer u het e-mail adres van de gebruiker met een unieke sleutel configureert, heeft elk item een uniek e-mail adres binnen een gegeven `CompanyID` . Twee items kunnen niet worden gemaakt met dubbele e-mail adressen en met dezelfde partitie sleutel waarde. In de SQL-API (core) van Azure Cosmos DB worden items opgeslagen als JSON-waarden. Deze JSON-waarden zijn hoofdletter gevoelig. Wanneer u een eigenschap kiest als een unieke sleutel, kunt u hoofdletter gevoelige waarden voor die eigenschap invoegen. Als u bijvoorbeeld een unieke sleutel hebt gedefinieerd voor de eigenschap naam, komt ' Gaby ' niet overeen met ' Gaby ' en kunt u beide invoegen in de container.

Als u items met hetzelfde e-mail adres wilt maken, maar niet dezelfde voor naam, achternaam en e-mail adres, voegt u meer paden toe aan het beleid voor unieke sleutels. In plaats van een unieke sleutel te maken op basis van het e-mail adres, kunt u ook een unieke sleutel maken met een combi natie van de voor naam, achternaam en e-mail adres. Deze sleutel wordt ook wel een samengestelde unieke sleutel genoemd. In dit geval is elke unieke combi natie van de drie waarden binnen een opgegeven `CompanyID` toegestaan. 

De container kan bijvoorbeeld items met de volgende waarden bevatten, waarbij elk item voldoet aan de beperking van de unieke sleutel.

|CompanyID|Voornaam|Achternaam|E-mailadres|
|---|---|---|---|
|Contoso|Gaby|Duperre|gaby@contoso.com |
|Contoso|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Gaby|Duperre|gaby@fabrikam.com|
|Fabrikam|Ivan|Duperre|gaby@fabrikam.com|
|Fabrkam|   |Duperre|gaby@fabraikam.com|
|Fabrkam|   |   |gaby@fabraikam.com|

Als u probeert een ander item in te voegen met de combi naties die in de vorige tabel worden vermeld, wordt een fout bericht weer gegeven. De fout geeft aan dat er niet aan de unieke-sleutel beperking is voldaan. U ontvangt ofwel `Resource with specified ID or name already exists` `Resource with specified ID, name, or unique index already exists` een retour bericht. 

## <a name="define-a-unique-key"></a>Een unieke sleutel definiëren

U kunt unieke sleutels alleen definiëren wanneer u een Azure Cosmos-container maakt. Een unieke sleutel ligt binnen het bereik van een logische partitie. Als u in het vorige voor beeld de container partitioneert op basis van de post code, worden er dubbele items in elke logische partitie opgedeeld. Houd rekening met de volgende eigenschappen wanneer u unieke sleutels maakt:

* U kunt een bestaande container niet bijwerken om een andere unieke sleutel te gebruiken. Met andere woorden, nadat een container is gemaakt met een uniek sleutel beleid, kan het beleid niet worden gewijzigd.

* Als u een unieke sleutel voor een bestaande container wilt instellen, maakt u een nieuwe container met de beperking voor de unieke sleutel. Gebruik het juiste hulp programma voor gegevens migratie om de gegevens van de bestaande container naar de nieuwe container te verplaatsen. Voor SQL-containers gebruikt u het [hulp programma voor gegevens migratie](import-data.md) om gegevens te verplaatsen. Gebruik [mongoimport.exe of mongorestore.exe](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) om gegevens te verplaatsen voor MongoDb-containers.

* Een uniek sleutel beleid kan Maxi maal 16 padnamen bevatten. De waarden kunnen bijvoorbeeld `/firstName` , `/lastName` en zijn `/address/zipCode` . Elk uniek sleutel beleid kan Maxi maal 10 unieke sleutel beperkingen of combi Naties hebben. De gecombineerde paden voor elke unieke index beperking mogen niet groter zijn dan 60 bytes. In het vorige voor beeld is de voor naam, achternaam en e-mail adres samen één beperking. Deze beperking maakt gebruik van drie van de 16 mogelijke paden.

* Wanneer een container een uniek sleutel beleid heeft, zijn de kosten voor [aanvraag eenheden (ru)](request-units.md) om een item te maken, bij te werken en te verwijderen iets hoger.

* Sparse unieke sleutels worden niet ondersteund. Als sommige unieke padgegevens ontbreken, worden ze behandeld als Null-waarden, die deel uitmaken van de beperking van uniekheid. Daarom kan er slechts één item met een null-waarde zijn om aan deze beperking te voldoen.

* Unieke sleutel namen zijn hoofdletter gevoelig. Denk bijvoorbeeld aan een container waarbij de beperking van de unieke sleutel is ingesteld op `/address/zipcode` . Als uw gegevens een veld bevat met de naam `ZipCode` , Azure Cosmos db ' null ' als unieke sleutel invoegen, omdat dit `zipcode` niet hetzelfde is als `ZipCode` . Vanwege deze hoofdletter gevoeligheid kunnen alle andere records met ZipCode niet worden ingevoegd omdat de dubbele null-waarde de beperking van de unieke sleutel schendt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logische partities](partitioning-overview.md)
* Ontdekken [hoe u unieke sleutels kunt definiëren](how-to-define-unique-keys.md) bij het maken van een container

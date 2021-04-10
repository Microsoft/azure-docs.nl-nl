---
title: Synapse RBAC-rollen
description: In dit artikel worden de ingebouwde Synapse RBAC-rollen beschreven
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 35f66732fa9cb48b94f80bab203534c9d04b7a7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102118"
---
# <a name="synapse-rbac-roles"></a>Synapse RBAC-rollen

In het artikel worden de ingebouwde Synapse RBAC-rollen, de toegekende machtigingen en de bereiken beschreven waarop ze kunnen worden gebruikt.  

## <a name="whats-changed-since-the-preview"></a>Wat is er gewijzigd sinds de preview?
De volgende wijzigingen zijn van toepassing op gebruikers die bekend zijn met de Synapse RBAC-rollen die worden meegeleverd tijdens de preview-versie:
- De naam van de werkruimte beheerder is gewijzigd **Synapse-beheerder**
- Apache Spark beheerder heeft de naam **Synapse Apache Spark beheerder** gewijzigd en is gemachtigd om alle gepubliceerde code artefacten, inclusief SQL-scripts, te bekijken.  Deze rol biedt geen toestemming meer voor het gebruik van de werk ruimte-MSI, waarvoor de Synapse is vereist.  Deze machtiging is vereist om pijp lijnen uit te voeren. 
- De naam van de SQL- **beheerder is gewijzigd Synapse SQL-Administrator** en is gemachtigd om alle gepubliceerde code artefacten te bekijken, met inbegrip van Spark-notebooks en-taken.  Deze rol biedt geen toestemming meer voor het gebruik van de werk ruimte-MSI, waarvoor de Synapse is vereist. Deze machtiging is vereist om pijp lijnen uit te voeren.
- **Nieuwe Synapse RBAC-rollen** worden geïntroduceerd die gericht zijn op de ondersteuning van ontwikkel-en Operations personen in plaats van specifieke analyse-Runtimes.  
- **Nieuwe bereiken op een lager niveau** worden geïntroduceerd voor verschillende rollen.  Met deze bereiken kunnen rollen worden beperkt tot specifieke resources of objecten.

>[!Note]
>De **nieuwe Synapse RBAC-rollen en bereiken met een lager niveau zijn momenteel beschikbaar als preview-versie**.  U wordt aangeraden deze nieuwe rollen en bereiken te gebruiken, die volledig worden ondersteund en om feedback te geven over het gebruik ervan.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Ingebouwde Synapse RBAC-rollen en-bereiken

In de volgende tabel worden de ingebouwde rollen en de bereiken beschreven waarop ze kunnen worden gebruikt.

>[!Note]
> Gebruikers met elke Synapse RBAC-rol in elk bereik hebben automatisch de gebruikersrol Synapse in het bereik van de werk ruimte. 

|Rol |Machtigingen|Bereiken|
|---|---|-----|
|Synapse-beheerder  |Volledige Synapse toegang tot SQL-groepen zonder server, Apache Spark Pools en integratie-Runtimes.  Omvat het maken, lezen, bijwerken en verwijderen van toegang tot alle gepubliceerde code artefacten.  Bevat reken operator, gekoppelde Data Manager en referentie gebruikers machtigingen voor de werkruimte systeemidentiteits referentie.  Omvat het toewijzen van Synapse RBAC-rollen. Naast de Synapse-beheerder kunnen Azure-eigen aars ook Synapse RBAC-rollen toewijzen. Er zijn Azure-machtigingen vereist om reken resources te maken, verwijderen en beheren. </br></br>_Kunnen Lees-en schrijf artefacten </br> alle acties op Spark-activiteiten uitvoeren. </br> Kan de logboeken van Spark-groepen weer geven </br> opgeslagen notitie blok en de uitvoer van de pijp lijn kunnen </br> de geheimen gebruiken die zijn opgeslagen door gekoppelde services of referenties </br> kunnen verbinding maken met SQL serverloze eind punten met SQL `db_datareader` , `db_datawriter` , `connect` en `grant` machtigingen </br> kunnen Synapse RBAC-rollen toewijzen en intrekken in het huidige bereik_|Werkruimte </br> Spark-pool<br/>Integratie-runtime </br>Gekoppelde service</br>Referentie |
|Synapse Apache Spark-beheerder</br>|Volledige Synapse toegang tot Apache Spark groepen.  Het maken, lezen, bijwerken en verwijderen van toegang tot gepubliceerde Spark-taak definities, notitie blokken en hun uitvoer, en naar bibliotheken, gekoppelde services en referenties.  Hiermee wordt lees toegang tot alle andere gepubliceerde code artefacten opgenomen. Beschikt niet over de machtiging om referenties te gebruiken en pijp lijnen uit te voeren. Exclusief het verlenen van toegang. </br></br>_Kan alle acties op Spark-artefacten </br> alle acties voor Spark-activiteiten uitvoeren_|Werkruimte</br>Spark-pool|
|Synapse SQL-beheerder|Volledige Synapse toegang tot SQL-groepen zonder server.  Toegang tot gepubliceerde SQL-scripts,-referenties en gekoppelde services maken, lezen, bijwerken en verwijderen.  Hiermee wordt lees toegang tot alle andere gepubliceerde code artefacten opgenomen.  Beschikt niet over de machtiging om referenties te gebruiken en pijp lijnen uit te voeren. Exclusief het verlenen van toegang. </br></br>*Kan alle acties op SQL-scripts <br/> verbinding maken met SQL serverloze eind punten met SQL `db_datareader` -, `db_datawriter` ,- `connect` en- `grant` machtigingen*|Werkruimte|
|Synapse-bijdrager|Volledige Synapse toegang tot Apache Spark Pools en integratie-Runtimes. Omvat het maken, lezen, bijwerken en verwijderen van toegang tot alle gepubliceerde code artefacten en hun uitvoer, inclusief referenties en gekoppelde services.  Bevat reken operator machtigingen. Beschikt niet over de machtiging om referenties te gebruiken en pijp lijnen uit te voeren. Exclusief het verlenen van toegang. </br></br>_Kunnen Lees-en schrijf artefacten het </br> opgeslagen notitie blok en de uitvoer van de pijp lijn kunnen bekijken </br> alle acties voor Spark-activiteiten </br> kunnen de logboeken van Spark-groepen weer geven_|Werkruimte </br> Spark-pool<br/> Integratie-runtime|
|Synapse artefact Uitgever|De toegang tot gepubliceerde code artefacten en hun uitvoer maken, lezen, bijwerken en verwijderen. Bevat geen machtiging voor het uitvoeren van code of pijp lijnen of voor het verlenen van toegang. </br></br>_Kan gepubliceerde artefacten lezen en artefacten publiceren </br> kan opgeslagen notitie blok, Spark-taak en pijplijn uitvoer weer geven_|Werkruimte
|Synapse artefact gebruiker|Lees toegang tot gepubliceerde code artefacten en hun uitvoer. Kan nieuwe artefacten maken, maar kan geen wijzigingen publiceren of code uitvoeren zonder extra machtigingen.|Werkruimte
|Synapse Compute-operator |Verzend Spark-taken en-notebooks en Bekijk Logboeken.  Omvat het annuleren van Spark-taken die door een gebruiker zijn verzonden. Vereist aanvullende referenties voor het gebruiken van de identiteit van het werkruimte systeem om pijp lijnen uit te voeren, de pipeline-uitvoeringen en-uitvoer weer te geven. </br></br>_Kan taken verzenden en annuleren, met inbegrip van taken die door anderen zijn ingediend, </br> kunnen Logboeken van Spark-groepen weer geven_|Werkruimte</br>Spark-pool</br>Integratie-runtime|
|Synapse-referentie gebruiker|Runtime en configuratie-tijd voor het gebruik van geheimen binnen referenties en gekoppelde services in activiteiten als pijplijn uitvoeringen. Als u pijp lijnen wilt uitvoeren, is deze rol vereist en wordt het bereik van de identiteit van het werkruimte systeem bepaald. </br></br>_Het bereik van een referentie biedt toegang tot gegevens via een gekoppelde service die wordt beveiligd door de referentie (hiervoor is ook een machtiging voor reken gebruik vereist) </br> waarmee pijp lijnen worden uitgevoerd die worden beveiligd door de identiteits referentie van het werkruimte systeem (met extra reken bevoegdheid)_|Werkruimte </br>Gekoppelde service</br>Referentie
|Synapse gekoppeld Data Manager|Het maken en beheren van beheerde privé-eind punten, gekoppelde services en referenties. Kan beheerde privé-eind punten maken die gebruikmaken van gekoppelde services die worden beveiligd door referenties|Werkruimte|
|Synapse-gebruiker|Details van SQL-groepen, Apache Spark Pools, Integration Runtimes en gepubliceerde gekoppelde services en referenties weer geven. Andere gepubliceerde code artefacten zijn niet inbegrepen.  Kan nieuwe artefacten maken, maar kan niet worden uitgevoerd of gepubliceerd zonder extra machtigingen.</br></br>_Kan Spark-Pools en integratie-Runtimes vermelden en lezen._|Werk ruimte, Spark-pool</br>Gekoppelde service </br>Referentie|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse RBAC-rollen en de acties die ze toestaan

>[!Note]
>- Alle acties in de onderstaande tabellen worden voorafgegaan door ' micro soft. Synapse/... '</br>
>- Alle bewerkingen voor het lezen, schrijven en verwijderen van artefacten zijn met betrekking tot gepubliceerde artefacten in de Live service.  Deze machtigingen hebben geen invloed op de toegang tot artefacten in een verbonden Git-opslag plaats.  

De volgende tabel geeft een lijst van de ingebouwde rollen en de acties/machtigingen die elk ondersteunt.

Rol|Acties
--|--
Synapse-beheerder|werk ruimten/lezen</br>werk ruimten/roleAssignments/schrijven, verwijderen</br>werk ruimten/managedPrivateEndpoint/schrijven, verwijderen</br>werk ruimten/bigDataPools/useCompute/actie</br>werk ruimten/bigDataPools/viewLogs/actie</br>werk ruimten/integrationRuntimes/useCompute/actie</br>werk ruimten/artefacten/lezen</br>werk ruimten/notebooks/schrijven, verwijderen</br>werk ruimten/sparkJobDefinitions/schrijven, verwijderen</br>werk ruimten/sqlScripts/schrijven, verwijderen</br>werk ruimten/gegevens stromen/schrijven, verwijderen</br>werk ruimten/pijp lijnen/schrijven, verwijderen</br>werk ruimten/triggers/schrijven, verwijderen</br>werk ruimten/gegevens sets/schrijven, verwijderen</br>werk ruimten/bibliotheken/schrijven, verwijderen</br>werk ruimten/linkedServices/schrijven, verwijderen</br>werk ruimten/referenties/schrijven, verwijderen</br>werk ruimten/notitie blokken/viewOutputs/actie</br>werk ruimten/pijp lijnen/viewOutputs/actie</br>werk ruimten/linkedServices/useSecret/actie</br>werk ruimten/referenties/useSecret/actie|
|Synapse Apache Spark-beheerder|werk ruimten/lezen</br>werk ruimten/bigDataPools/useCompute/actie</br>werk ruimten/bigDataPools/viewLogs/actie</br>werk ruimten/notitie blokken/viewOutputs/actie</br>werk ruimten/artefacten/lezen</br>werk ruimten/notebooks/schrijven, verwijderen</br>werk ruimten/sparkJobDefinitions/schrijven, verwijderen</br>werk ruimten/bibliotheken/schrijven, verwijderen</br>werk ruimten/linkedServices/schrijven, verwijderen</br>werk ruimten/referenties/schrijven, verwijderen|
|Synapse SQL-beheerder|werk ruimten/lezen</br>werk ruimten/artefacten/lezen</br>werk ruimten/sqlScripts/schrijven, verwijderen</br>werk ruimten/linkedServices/schrijven, verwijderen</br>werk ruimten/referenties/schrijven, verwijderen|
|Synapse-bijdrager|werk ruimten/lezen</br>werk ruimten/bigDataPools/useCompute/actie</br>werk ruimten/bigDataPools/viewLogs/actie</br>werk ruimten/integrationRuntimes/useCompute/actie</br>werk ruimten/integrationRuntimes/viewLogs/actie</br>werk ruimten/artefacten/lezen</br>werk ruimten/notebooks/schrijven, verwijderen</br>werk ruimten/sparkJobDefinitions/schrijven, verwijderen</br>werk ruimten/sqlScripts/schrijven, verwijderen</br>werk ruimten/gegevens stromen/schrijven, verwijderen</br>werk ruimten/pijp lijnen/schrijven, verwijderen</br>werk ruimten/triggers/schrijven, verwijderen</br>werk ruimten/gegevens sets/schrijven, verwijderen</br>werk ruimten/bibliotheken/schrijven, verwijderen</br>werk ruimten/linkedServices/schrijven, verwijderen</br>werk ruimten/referenties/schrijven, verwijderen</br>werk ruimten/notitie blokken/viewOutputs/actie</br>werk ruimten/pijp lijnen/viewOutputs/actie|
|Synapse artefact Uitgever|werk ruimten/lezen</br>werk ruimten/artefacten/lezen</br>werk ruimten/notebooks/schrijven, verwijderen</br>werk ruimten/sparkJobDefinitions/schrijven, verwijderen</br>werk ruimten/sqlScripts/schrijven, verwijderen</br>werk ruimten/gegevens stromen/schrijven, verwijderen</br>werk ruimten/pijp lijnen/schrijven, verwijderen</br>werk ruimten/triggers/schrijven, verwijderen</br>werk ruimten/gegevens sets/schrijven, verwijderen</br>werk ruimten/bibliotheken/schrijven, verwijderen</br>werk ruimten/linkedServices/schrijven, verwijderen</br>werk ruimten/referenties/schrijven, verwijderen</br>werk ruimten/notitie blokken/viewOutputs/actie</br>werk ruimten/pijp lijnen/viewOutputs/actie|
|Synapse artefact gebruiker|werk ruimten/lezen</br>werk ruimten/artefacten/lezen</br>werk ruimten/notitie blokken/viewOutputs/actie</br>werk ruimten/pijp lijnen/viewOutputs/actie|
|Synapse Compute-operator |werk ruimten/lezen</br>werk ruimten/bigDataPools/useCompute/actie</br>werk ruimten/bigDataPools/viewLogs/actie</br>werk ruimten/integrationRuntimes/useCompute/actie</br>werk ruimten/integrationRuntimes/viewLogs/actie|
|Synapse-referentie gebruiker|werk ruimten/lezen</br>werk ruimten/linkedServices/useSecret/actie</br>werk ruimten/referenties/useSecret/actie|
|Synapse gekoppeld Data Manager|werk ruimten/lezen</br>werk ruimten/managedPrivateEndpoint/schrijven, verwijderen</br>werk ruimten/linkedServices/schrijven, verwijderen</br>werk ruimten/referenties/schrijven, verwijderen|
|Synapse-gebruiker|werk ruimten/lezen|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse RBAC-acties en de functies die ze toestaan

De volgende tabel geeft een lijst van Synapse-acties en de ingebouwde rollen die deze acties toestaan:

Bewerking|Rol
--|--
werk ruimten/lezen|Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse SQL-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever</br>Synapse artefact gebruiker</br>Synapse Compute-operator </br>Synapse-referentie gebruiker</br>Synapse gekoppeld Data Manager</br>Synapse-gebruiker 
werk ruimten/roleAssignments/schrijven, verwijderen|Synapse-beheerder
werk ruimten/managedPrivateEndpoint/schrijven, verwijderen|Synapse-beheerder</br>Synapse gekoppeld Data Manager
werk ruimten/bigDataPools/useCompute/actie|Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse-bijdrager</br>Synapse Compute-operator 
werk ruimten/bigDataPools/viewLogs/actie|Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse-bijdrager</br>Synapse Compute-operator 
werk ruimten/integrationRuntimes/useCompute/actie|Synapse-beheerder</br>Synapse-bijdrager</br>Synapse Compute-operator 
werk ruimten/artefacten/lezen|Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse SQL-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever</br>Synapse artefact gebruiker
werk ruimten/notebooks/schrijven, verwijderen|Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever
werk ruimten/sparkJobDefinitions/schrijven, verwijderen|Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever
werk ruimten/sqlScripts/schrijven, verwijderen|Synapse-beheerder</br>Synapse SQL-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever
werk ruimten/gegevens stromen/schrijven, verwijderen|Synapse-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever
werk ruimten/pijp lijnen/schrijven, verwijderen|Synapse-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever
werk ruimten/triggers/schrijven, verwijderen|Synapse-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever
werk ruimten/gegevens sets/schrijven, verwijderen|Synapse-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever
werk ruimten/bibliotheken/schrijven, verwijderen|Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever
werk ruimten/linkedServices/schrijven, verwijderen|Synapse-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever</br>Synapse gekoppeld Data Manager
werk ruimten/referenties/schrijven, verwijderen|Synapse-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever</br>Synapse gekoppeld Data Manager
werk ruimten/notitie blokken/viewOutputs/actie|Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever</br>Synapse artefact gebruiker
werk ruimten/pijp lijnen/viewOutputs/actie|Synapse-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever</br>Synapse artefact gebruiker
werk ruimten/linkedServices/useSecret/actie|Synapse-beheerder</br>Synapse-referentie gebruiker
werk ruimten/referenties/useSecret/actie|Synapse-beheerder</br>Synapse-referentie gebruiker

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse RBAC-bereiken en hun ondersteunde rollen

De volgende tabel bevat een lijst met Synapse RBAC-bereiken en de rollen die in elk bereik kunnen worden toegewezen. 

>[!note]
>Als u een object wilt maken of verwijderen, moet u machtigingen hebben op een bereik op een hoger niveau.

Bereik|Rollen
--|--
Werkruimte |Synapse-beheerder</br>Synapse Apache Spark-beheerder</br>Synapse SQL-beheerder</br>Synapse-bijdrager</br>Synapse artefact Uitgever</br>Synapse artefact gebruiker</br>Synapse Compute-operator </br>Synapse-referentie gebruiker</br>Synapse gekoppeld Data Manager</br>Synapse-gebruiker
Apache Spark-pool | Synapse-beheerder </br>Synapse-bijdrager </br> Synapse Compute-operator
Integratie-runtime | Synapse-beheerder </br>Synapse-bijdrager </br> Synapse Compute-operator
Gekoppelde service |Synapse-beheerder </br>Synapse-referentie gebruiker
Referentie |Synapse-beheerder </br>Synapse-referentie gebruiker
 
>[!note]
>Alle artefact rollen en-acties bevinden zich op het niveau van de werk ruimte. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie [over het controleren van Synapse RBAC-roltoewijzingen](./how-to-review-synapse-rbac-role-assignments.md) voor een werk ruimte.

Meer informatie [over het toewijzen van Synapse RBAC-rollen](./how-to-manage-synapse-rbac-role-assignments.md)

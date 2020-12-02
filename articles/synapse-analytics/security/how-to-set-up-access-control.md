---
title: Uw Synapse-werkruimte beveiligen
description: In dit artikel leert u hoe u rollen en toegangs beheer kunt gebruiken om activiteiten te beheren en toegang te krijgen tot gegevens in de Synapse-werk ruimte.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 25e191af919c5880045a6c4c7c79b675cf02520e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458720"
---
# <a name="secure-your-synapse-workspace"></a>Uw Synapse-werkruimte beveiligen 

In dit artikel leert u hoe u rollen en toegangs beheer kunt gebruiken om activiteiten en toegang tot gegevens te beheren. Door deze instructies te volgen, is toegangs beheer in azure Synapse Analytics vereenvoudigd. U hoeft alleen maar gebruikers toe te voegen aan of te verwijderen uit een van de drie beveiligings groepen.

## <a name="overview"></a>Overzicht

Als u een Synapse-werk ruimte wilt beveiligen, volgt u een patroon voor het configureren van de volgende items:

- Azure-rollen (zoals de ingebouwde eigenaar, Inzender, enz.)
- Synapse-rollen: deze rollen zijn uniek voor Synapse en zijn niet gebaseerd op Azure-functies. Er zijn drie rollen:
  - Synapse werkruimte beheerder
  - Synapse SQL-beheerder
  - Apache Spark voor Azure Synapse Analytics-beheerder
- Toegangs beheer voor gegevens in Azure Data Lake Storage gen 2 (ADLSGEN2).
- Toegangs beheer voor Synapse SQL-en Spark-data bases

## <a name="steps-to-secure-a-synapse-workspace"></a>Stappen voor het beveiligen van een Synapse-werk ruimte

In dit document worden standaard namen gebruikt om de instructies te vereenvoudigen. Vervang deze door een of meer namen van uw keuze.

|Instelling | Voorbeeldwaarde | Beschrijving |
| :------ | :-------------- | :---------- |
| **Synapse-werkruimte** | WS1 |  De naam die de Synapse-werk ruimte heeft. |
| **ADLSGEN2-account** | STG1 | Het ADLS-account dat moet worden gebruikt met uw werk ruimte. |
| **Container** | CNT1 | De container in STG1 die standaard door de werk ruimte wordt gebruikt. |
| **Active Directory-Tenant** | contoso | de naam van de Active Directory-Tenant.|
||||

## <a name="step-1-set-up-security-groups"></a>STAP 1: beveiligings groepen instellen

Drie beveiligings groepen maken en invullen voor uw werk ruimte:

- **WS1 \_ WSAdmins** : voor gebruikers die volledige controle over de werk ruimte moeten hebben
- **WS1 \_ SparkAdmins** : voor gebruikers die volledige controle moeten hebben over de Spark-aspecten van de werk ruimte
- **WS1 \_ SQLAdmins** : voor gebruikers die volledige controle over de SQL-aspecten van de werk ruimte moeten hebben

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>STAP 2: uw Data Lake Storage Gen2-account voorbereiden

Identificeer deze informatie over uw opslag:

- Het ADLSGEN2-account dat moet worden gebruikt voor uw werk ruimte. In dit document wordt het STG1 aangeroepen.  STG1 wordt beschouwd als het ' primaire ' opslag account voor uw werk ruimte.
- De container in WS1 die standaard door uw Synapse-werk ruimte wordt gebruikt. In dit document wordt het CNT1 aangeroepen.  Deze container wordt gebruikt voor:
  - Opslaan van de back-upgegevens bestanden voor Spark-tabellen
  - Uitvoerings logboeken voor Spark-taken

- Wijs de beveiligings groepen met behulp van de Azure Portal de volgende rollen toe aan CNT1

  - **WS1 \_ WSAdmins** toewijzen aan de rol van Gegevensbeheerderrol voor **opslag BLOB**
  - **WS1 \_ SparkAdmins** toewijzen aan de rol van Gegevensbeheerderrol voor **opslag BLOB**
  - **WS1 \_ SQLAdmins** toewijzen aan de rol van Gegevensbeheerderrol voor **opslag BLOB**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>STAP 3: uw Synapse-werk ruimte maken en configureren

 Maak in het Azure Portal een Synapse-werk ruimte:

- Selecteer uw abonnement
- Selecteer uw resource groep-u moet toegang hebben tot een resource groep waarvoor u de rol van **eigenaar** hebt gekregen.
- De werk ruimte een naam WS1
- Kies STG1 voor het opslag account-. Kies CNT1 voor de container die wordt gebruikt als het "Bestands systeem".
- Open WS1 in Synapse Studio
- Selecteer **beheren**  >  **Access Control** de beveiligings groepen toe te wijzen aan de volgende Synapse-rollen.
  - **WS1 \_ WSAdmins** toewijzen aan Synapse-werkruimte beheerders
  - **WS1 \_ SparkAdmins** aan Synapse Spark-beheerders toewijzen
  - **WS1 \_ SQLAdmins** toewijzen aan Synapse SQL-Administrators

## <a name="step-4-configure-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>STAP 4: Data Lake Storage Gen2 configureren voor gebruik door de werk ruimte Synapse

De Synapse-werk ruimte moet toegang hebben tot STG1 en CNT1, zodat er pijp lijnen kunnen worden uitgevoerd en systeem taken worden uitgevoerd.

- Open de Azure Portal
- STG1 zoeken
- Ga naar CNT1
- Zorg ervoor dat het MSI-bestand (Managed Service Identity) voor WS1 is toegewezen aan de rol van de **BLOB-gegevens bijdrager voor opslag** op CNT1
  - Als de toewijzing niet wordt weer geven, wijst u deze toe.
  - Het MSI-bestand heeft dezelfde naam als de werk ruimte. In dit geval is dit &quot; WS1 &quot; .

## <a name="step-5-configure-admin-access-for-synapse-sql"></a>STAP 5: beheerders toegang voor Synapse SQL configureren

- Open de Azure Portal
- Ga naar WS1
- Selecteer onder **instellingen** de optie **SQL Active Directory-beheerder**
- Selecteer **beheerder instellen** en kies WS1 \_ SQLAdmins

## <a name="step-6-maintain-access-control"></a>STAP 6: toegangs beheer onderhouden

De configuratie is voltooid.

Nu kunt u gebruikers toevoegen aan en verwijderen uit de drie beveiligings groepen om de toegang voor gebruikers te beheren.

Hoewel u gebruikers hand matig aan Synapse-rollen kunt toewijzen, worden de items niet consistent geconfigureerd. U kunt in plaats daarvan alleen gebruikers toevoegen aan of verwijderen uit de beveiligings groepen.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>STAP 7: de toegang voor gebruikers in de rollen controleren

Gebruikers in elke rol moeten de volgende stappen uitvoeren:

| Getal | Stap | Werkruimtebeheerders | Spark-beheerders | SQL-Administrators |
| --- | --- | --- | --- | --- |
| 1 | Een Parquet-bestand uploaden naar CNT1 | JA | JA | JA |
| 2 | Het Parquet-bestand lezen met een serverloze SQL-groep | JA | NO | JA |
| 3 | Een serverloze Apache Spark-pool maken | JA [1] | JA [1] | NO  |
| 4 | Hiermee wordt het Parquet-bestand met een notitie blok gelezen | JA | JA | NO |
| 5 | Een pijp lijn maken op basis van het notitie blok en de pijp lijn activeren om nu uit te voeren | JA | NO | NO |
| 6 | Maak een toegewezen SQL-groep en voer een SQL-script uit, zoals &quot; Select 1&quot; | JA [1] | NO | JA [1] |

> [!NOTE]
> [1] om SQL-of Spark-groepen te maken, moet de gebruiker ten minste Inzender rollen hebben in de werk ruimte Synapse.
>
 
>[!TIP]
> - Sommige stappen worden opzettelijk niet toegestaan, afhankelijk van de rol.
> - Houd er wel bij dat sommige taken mislukken als de beveiliging niet volledig is geconfigureerd. Deze taken worden in de tabel vermeld.

## <a name="step-8-network-security"></a>STAP 8: netwerk beveiliging

De werkruimte firewall, het virtuele netwerk en de [persoonlijke koppeling](../../azure-sql/database/private-endpoint-overview.md)configureren.

## <a name="step-9-completion"></a>STAP 9: voltooiing

Uw werk ruimte is nu volledig geconfigureerd en beveiligd.

## <a name="how-roles-interact-with-synapse-studio"></a>Hoe rollen communiceren met Synapse Studio

Synapse Studio werkt anders op basis van gebruikers rollen. Sommige items kunnen worden verborgen of uitgeschakeld als een gebruiker niet is toegewezen aan rollen die de juiste toegang geven. De volgende tabel bevat een overzicht van het effect op Synapse Studio.

| Taak | Werkruimte beheerders | Spark-beheerders | SQL-Administrators |
| --- | --- | --- | --- |
| Synapse Studio openen | JA | JA | JA |
| Start hub weer geven | JA | JA | JA |
| Data hub weer geven | JA | JA | JA |
| Data hub/Zie gekoppelde ADLS Gen2 accounts en containers | JA [1] | JA [1] | JA [1] |
| Data hub/Zie data bases | JA | JA | JA |
| Data hub/Zie objecten in data bases | JA | JA | JA |
| Data hub/toegang tot gegevens in Synapse SQL-data bases | JA   | NO   | JA   |
| Data hub/toegang tot gegevens in data bases van SQL-groep zonder server | JA [2]  | NO  | JA [2]  |
| Data hub/toegang tot gegevens in Spark-data bases | JA [2] | JA [2] | JA [2] |
| De ontwikkelen hub gebruiken | JA | JA | JA |
| Hub/SQL-scripts ontwikkelen | JA | NO | JA |
| Hub/auteurs Spark-taak definities ontwikkelen | JA | JA | NO |
| Hub/Auteur-notebooks ontwikkelen | JA | JA | NO |
| Hub/Auteur gegevens stromen ontwikkelen | JA | NO | NO |
| De Orchestration-hub gebruiken | JA | JA | JA |
| Hub en pijp lijnen gebruiken | JA | NO | NO |
| De hub beheren gebruiken | JA | JA | JA |
| Hub/Synapse SQL beheren | JA | NO | JA |
| Hub/Spark-groepen beheren | JA | JA | NO |
| Hub/Triggers beheren | JA | NO | NO |
| Hub/gekoppelde services beheren | JA | JA | JA |
| Hub/Access Control beheren (gebruikers toewijzen aan Synapse werkruimte rollen) | JA | NO | NO |
| Hub/integratie-Runtimes beheren | JA | JA | JA |
| De hub bewaken gebruiken | JA | JA | JA |
| Hub/indeling/pijplijn uitvoeringen bewaken  | JA | NO | NO |
| Hub/indeling/trigger-uitvoeringen bewaken  | JA | NO | NO |
| Hub/indeling/integratie-Runtimes bewaken  | JA | JA | JA |
| Hub/activiteiten/Spark-toepassingen bewaken | JA | JA | NO  |
| Hub/activiteiten/SQL-aanvragen bewaken | JA | NO | JA |
| Hub/activiteiten/Spark-Pools bewaken | JA | JA | NO  |
| Hub/triggers bewaken | JA | NO | NO |
| Hub/gekoppelde services beheren | JA | JA | JA |
| Hub/Access Control beheren (gebruikers toewijzen aan Synapse werkruimte rollen) | JA | NO | NO |
| Hub/integratie-Runtimes beheren | JA | JA | JA |


> [!NOTE]
> [1] toegang tot gegevens in containers is afhankelijk van het toegangs beheer in ADLS Gen2. </br>
> [2] SQL-tabellen en Spark-tabellen bevatten hun gegevens in ADLS Gen2 en toegang vereist de juiste machtigingen voor ADLS Gen2.

## <a name="next-steps"></a>Volgende stappen

Een [Synapse-werk ruimte](../quickstart-create-workspace.md) maken

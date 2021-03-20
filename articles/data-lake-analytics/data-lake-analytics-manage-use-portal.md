---
title: Azure Data Lake Analytics beheren door gebruik te maken van de Azure Portal
description: In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om Data Lake Analytics accounts, gegevens bronnen, gebruikers & taken te beheren.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: c1057478d9ae7d63bef9477a0b1297fa2c507ffa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218422"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Azure Data Lake Analytics beheren met de Azure-portal
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

In dit artikel wordt beschreven hoe u Azure Data Lake Analytics accounts, gegevens bronnen, gebruikers en taken beheert met behulp van de Azure Portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Data Lake Analytics-accounts beheren

### <a name="create-an-account"></a>Een account maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **een resource**  >  **Intelligence + Analytics**-  >  **Data Lake Analytics** maken.
3. Selecteer waarden voor de volgende items: 
   1. **Naam**: de naam van het data Lake Analytics-account.
   2. **Abonnement**: het Azure-abonnement dat wordt gebruikt voor het account.
   3. **Resource groep**: de Azure-resource groep waarin het account moet worden gemaakt. 
   4. **Locatie**: het Azure-data centrum voor het data Lake Analytics-account. 
   5. **Data Lake Store**: het standaard archief dat moet worden gebruikt voor het data Lake Analytics-account. Het Azure data Lake Store-account en het data Lake Analytics-account moeten zich op dezelfde locatie bestaan.
4. Klik op **Create**. 

### <a name="delete-a-data-lake-analytics-account"></a>Een Data Lake Analytics account verwijderen

Verwijder het standaard Data Lake Store account voordat u een Data Lake Analytics account verwijdert.

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik op **Verwijderen**.
3. Typ de account naam.
4. Klik op **Verwijderen**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Gegevensbronnen beheren

Data Lake Analytics ondersteunt de volgende gegevens bronnen:

* Data Lake Store
* Azure Storage

U kunt Data Explorer gebruiken om door gegevens bronnen te bladeren en basis bewerkingen voor bestands beheer uit te voeren. 

### <a name="add-a-data-source"></a>Een gegevensbron toevoegen

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik op **gegevens bronnen**.
3. Klik op **gegevens bron toevoegen**.
    
   * Als u een Data Lake Store account wilt toevoegen, hebt u de account naam en toegang tot het account nodig om er een query op uit te kunnen maken.
   * Als u Azure Blob-opslag wilt toevoegen, hebt u het opslag account en de account sleutel nodig. Ga naar het opslag account in de portal om ze te vinden.

## <a name="set-up-firewall-rules"></a>Firewall regels instellen

U kunt Data Lake Analytics gebruiken om de toegang tot uw Data Lake Analytics-account op netwerk niveau te vergren delen. U kunt een firewall inschakelen, een IP-adres opgeven of een IP-adres bereik definiëren voor uw vertrouwde clients. Nadat u deze metingen hebt ingeschakeld, kunnen alleen clients met de IP-adressen binnen het gedefinieerde bereik verbinding maken met de Store.

Als andere Azure-Services, zoals Azure Data Factory of Vm's, verbinding maken met het Data Lake Analytics-account, moet u ervoor zorgen dat **Azure-Services toestaan** is **ingeschakeld.** 

### <a name="set-up-a-firewall-rule"></a>Een firewallregel instellen

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik in het menu aan de linkerkant op **firewall**.

## <a name="add-a-new-user"></a>Een nieuwe gebruiker toevoegen

U kunt de **wizard gebruiker toevoegen** gebruiken om eenvoudig nieuwe data Lake gebruikers in te richten.

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik aan de linkerkant, onder **aan** de slag, op **wizard gebruiker toevoegen**.
3. Selecteer een gebruiker en klik vervolgens op **selecteren**.
4. Selecteer een rol en klik vervolgens op **selecteren**. Als u een nieuwe ontwikkelaar wilt instellen om Azure Data Lake te gebruiken, selecteert u de rol **Data Lake Analytics ontwikkelaar** .
5. Selecteer de toegangs beheer lijsten (Acl's) voor de U-SQL-data bases. Wanneer u tevreden bent met uw keuzes, klikt u op **selecteren**.
6. Selecteer de Acl's voor bestanden. Voor de standaard opslag wijzigt u niet de Acl's voor de hoofdmap//en voor de map/systeemstatus. Klik op **Selecteren**.
7. Controleer alle geselecteerde wijzigingen en klik vervolgens op **uitvoeren**.
8. Wanneer de wizard is voltooid, klikt u op **gereed**.

## <a name="manage-azure-role-based-access-control"></a>Toegangs beheer op basis van rollen beheren in azure

Net als andere Azure-Services kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om te bepalen hoe gebruikers met de service communiceren.

De standaard functies van Azure hebben de volgende mogelijkheden:
* **Eigenaar**: kan taken verzenden, taken bewaken, taken van een gebruiker annuleren en het account configureren.
* **Inzender**: kan taken verzenden, taken bewaken, taken van een gebruiker annuleren en het account configureren.
* **Lezer**: kan taken bewaken.

Gebruik de rol Data Lake Analytics ontwikkelaar om U-SQL-ontwikkel aars in staat te stellen de Data Lake Analytics-service te gebruiken. U kunt de Data Lake Analytics ontwikkelaars functie gebruiken voor het volgende:
* Taken verzenden.
* Controleer de taak status en de voortgang van taken die door elke gebruiker worden ingediend.
* Zie de U-SQL-scripts van taken die door een wille keurige gebruiker worden verzonden.
* Annuleer alleen uw eigen taken.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Gebruikers of beveiligings groepen toevoegen aan een Data Lake Analytics-account

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik op **toegangs beheer (IAM)**  >  **roltoewijzing toevoegen**.
3. Selecteer een rol.
4. Voeg een gebruiker toe.
5. Klik op **OK**.

>[!NOTE]
>Als een gebruiker of een beveiligings groep taken moet indienen, hebben ze ook machtigingen nodig voor het opslag account. Zie beveiligde gegevens die zijn [opgeslagen in data Lake Store](../data-lake-store/data-lake-store-secure-data.md)voor meer informatie.
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Taken beheren

### <a name="submit-a-job"></a>Een taak indienen

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.

2. Klik op **nieuwe taak**. Configureer voor elke taak:

    1. **Taak naam**: de naam van de taak.
    2. **Prioriteit**: lagere getallen hebben een hogere prioriteit. Als twee taken in de wachtrij worden geplaatst, wordt de waarde met een lagere prioriteit eerst uitgevoerd.
    3. **Parallellisme**: het maximum aantal reken processen dat voor deze taak moet worden gereserveerd.

3. Klik op **Taak verzenden**.

### <a name="monitor-jobs"></a>Taken controleren

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Klik op **alle taken weer geven**. Er wordt een lijst weer gegeven met alle actieve en recent voltooide taken in het account.
3. Klik desgewenst op **filter** om u te helpen de taken te vinden op **tijds bereik**, **taak naam** en waarden voor **Auteur** . 

### <a name="monitoring-pipeline-jobs"></a>Pijplijn taken bewaken
Taken die deel uitmaken van een pijp lijn werken samen, meestal opeenvolgend, om een specifiek scenario te bereiken. U kunt bijvoorbeeld een pijp lijn hebben die het opschonen, uitpakken, transformeren, het gebruik van klant inzichten samenvoegt. Pijplijn taken worden geïdentificeerd met behulp van de eigenschap pijp lijn wanneer de taak is verzonden. Voor taken die zijn gepland met ADF v2, wordt deze eigenschap automatisch ingevuld. 

Een lijst met U-SQL-taken weer geven die deel uitmaken van pijp lijnen: 

1. Ga in het Azure Portal naar uw Data Lake Analytics-accounts.
2. Klik op **taak inzichten**. Het tabblad alle taken wordt standaard weer gegeven, met een lijst met actieve, in de wachtrij geplaatste en beëindigde taken.
3. Klik op het tabblad **pijplijn taken** . Er wordt een lijst met pijplijn taken weer gegeven samen met geaggregeerde statistieken voor elke pijp lijn.

### <a name="monitoring-recurring-jobs"></a>Terugkerende taken bewaken
Een terugkerende taak is één die dezelfde bedrijfs logica heeft, maar die telkens wanneer deze wordt uitgevoerd verschillende invoer gegevens gebruikt. In het ideale geval moeten terugkerende taken altijd slagen en relatief stabiele uitvoerings tijd hebben. Als u deze problemen bewaken, kunt u ervoor zorgen dat de taak in orde is. Terugkerende taken worden geïdentificeerd met behulp van de eigenschap Recurrence. Voor taken die zijn gepland met ADF v2, wordt deze eigenschap automatisch ingevuld.

Een lijst met U-SQL-taken weer geven die periodiek zijn: 

1. Ga in het Azure Portal naar uw Data Lake Analytics-accounts.
2. Klik op **taak inzichten**. Het tabblad alle taken wordt standaard weer gegeven, met een lijst met actieve, in de wachtrij geplaatste en beëindigde taken.
3. Klik op het tabblad **terugkerende taken** . Er wordt een lijst met terugkerende taken weer gegeven samen met geaggregeerde statistieken voor elke terugkerende taak.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Data Lake Analytics](data-lake-analytics-overview.md)
* [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Azure Data Lake Analytics beheren met behulp van beleid](data-lake-analytics-account-policies.md)

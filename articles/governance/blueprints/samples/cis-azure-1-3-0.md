---
title: Voor beeld van CIS Microsoft Azure basis Bench Mark v 1.3.0 blauw druk
description: Overzicht van het CIS-voor beeld van het DIS Microsoft Azure fundament-benchmark v 1.3.0. Met dit blauwdrukvoorbeeld kunnen klanten specifieke beheeropties bekijken.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: d6b3a84ab426c5003233958b77f5c480f28cd704
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103202642"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>Voor beeld van CIS Microsoft Azure basis Bench Mark v 1.3.0 blauw druk

Het CIS-voor beeld van het DIS Microsoft Azure Stichtings-benchmark v 1.3.0 maakt governance-rails mogelijk met [Azure Policy](../../policy/overview.md) die u helpen specifieke aanbevelingen van CIS Microsoft Azure Stichting-benchmark v 1.3.0 te beoordelen. Deze blauw druk helpt klanten bij het implementeren van een kern verzameling beleids regels voor elke Azure-geïmplementeerde architectuur die Microsoft Azure CIS-1.3.0-aanbevelingen moet implementeren.

## <a name="recommendation-mapping"></a>Toewijzing van aanbevelingen

De [toewijzing van de Azure Policy aanbeveling](../../policy/samples/cis-azure-1-3-0.md) bevat details over de beleids definities die zijn opgenomen in deze blauw druk en hoe deze beleids definities worden toegewezen aan de **aanbevelingen** in cis-Microsoft Azure fundament Bench Mark v-1.3.0. Wanneer resources worden toegewezen aan een architectuur, worden deze op niet-naleving van toegewezen beleidsregels geëvalueerd door Azure Policy. Zie [Azure Policy](../../policy/overview.md) voor meer informatie.

## <a name="deploy"></a>Implementeren

De volgende stappen moeten worden uitgevoerd voor het implementeren van het Azure-blauw drukken CIS Microsoft Azure Stichting voor beeld van de basisbenchmark v-1.3.0:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

### <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het CIS-voor beeld van het **DIS-Microsoft Azure basis Bench Mark v 1.3.0** onder _andere voor beelden_ en selecteer **dit voor beeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Naam van blauwdruk**: Geef een naam op voor uw exemplaar van het blauwdrukvoorbeeld van CIS Microsoft Azure Foundations Benchmark.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die in het blauwdrukvoorbeeld zijn opgenomen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

### <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften, maar deze wijziging kan worden verplaatst van de uitlijning met CIS-Microsoft Azure basis benchmark v-1.3.0 aanbevelingen.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **Notities over wijzigingen** op, zoals Eerste gepubliceerde versie op basis van het blauwdrukvoorbeeld CIS Microsoft Azure Foundations Benchmark. Selecteer vervolgens **Publiceren** onder aan de pagina.

### <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **Gepubliceerd**, kan het worden toegewezen aan een abonnement binnen de beheergroep waarin de kopie is opgeslagen. Dit is de stap waarin parameters worden opgegeven om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina Blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen**: Selecteer een of meer van de abonnementen in de beheergroep waarin u uw kopie van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt een toewijzing gemaakt voor elk abonnement waarvoor de ingevoerde parameters worden gebruikt.
     - **Naam van toewijzing**: De naam wordt vooraf voor u ingevuld op basis van de naam van de blauwdruk.
       Wijzig de naam als dat nodig is of gebruik de opgegeven naam.
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
     - **Blauwdrukdefinitieversie**: Kies een **gepubliceerde** versie van uw kopie van het blauwdrukvoorbeeld.

   - Toewijzing vergrendelen

     Selecteer de instelling voor het vergrendelen van de blauwdruk voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de optie voor de standaard door het _systeem toegewezen_ beheerde identiteit staan.

   - Artefactparameters

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder de parameter is gedefinieerd. Deze parameters zijn [dynamische parameters](../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens de toewijzing van de blauwdruk. Zie de [tabel met artefactparameters](#artifact-parameters-table) voor een volledige lijst met artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert u **Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van het artefact begint. De implementatie duurt circa één uur. Open de blauwdruktoewijzing om de status van de implementatie te controleren.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis**. Azure-resources zijn [geprijsd per product](https://azure.microsoft.com/pricing/). Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voor het uitvoeren van resources die door dit blauwdrukvoorbeeld zijn geïmplementeerd.

### <a name="artifact-parameters-table"></a>Tabel met artefactparameters

In de volgende tabel ziet u een lijst met de blauwdrukartefactparameters:

|Naam van het artefact|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Lijst met virtuele-machine-uitbreidingen die zijn goedgekeurd voor gebruik|Een door punt komma's gescheiden lijst met extensies van virtuele machines; Als u een volledige lijst met uitbrei dingen wilt zien, gebruikt u de opdracht Azure PowerShell Get-AzVMExtensionImage|
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: door SQL beheerde instanties moeten door de klant beheerde sleutels worden gebruikt om gegevens in rust te versleutelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in Azure Data Lake Store moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Schijfversleuteling moet worden toegepast op virtuele machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: voor sleutel kluis moet de beveiliging leegmaken zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Controleer of de API-app ' client certificaten (binnenkomende client certificaten) ' is ingesteld op ' on '|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: SQL-servers moeten door de klant beheerde sleutels gebruiken om gegevens in rust te versleutelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: beheerde identiteit moet worden gebruikt in uw functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor Key Vault moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: aangepaste rollen van abonnements eigenaren mogen niet bestaan|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: voor de sleutels moeten de verval datums zijn ingesteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Transparent Data Encryption in SQL-databases moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: de evaluatie van beveiligings problemen moet worden ingeschakeld op het SQL-beheerde exemplaar|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect van het beleid: Zorg ervoor dat PHP-versie de meest recente is, als deze wordt gebruikt als onderdeel van de API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor App Service moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: opslag accounts moeten netwerk toegang beperken met behulp van regels voor het virtuele netwerk|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: beheerde identiteit moet worden gebruikt in uw web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: SSH-toegang via internet moet worden geblokkeerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: niet-gekoppelde schijven moeten worden versleuteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor opslag moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: opslag accounts moeten netwerk toegang beperken|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in Logic Apps moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in IoT Hub moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: FTPS moet alleen worden vereist in uw functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beveiligings bewerkingen (micro soft. Security/securitySolutions/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beveiligings bewerkingen (micro soft. Security/securitySolutions/write)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in batch-accounts moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: het automatisch inrichten van de Log Analytics-agent moet zijn ingeschakeld voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Controleren of de Java-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: FTPS moet vereist zijn in uw web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor servers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: abonnementen moeten een e-mail adres voor contact personen hebben voor beveiligings problemen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: open bare toegang tot opslag account moet niet worden toegestaan|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor Kubernetes moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: voor het beperken van de verbinding moet PostgreSQL-database servers zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Zorg ervoor dat de WEB-app client certificaten (binnenkomende client certificaten) heeft ingesteld op on|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor SQL-servers op computers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: e-mail melding voor waarschuwingen met hoge urgentie moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: opslag account moet door de klant beheerde sleutel gebruiken voor versleuteling|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect van het beleid: Zorg ervoor dat python-versie de meest recente is, als deze wordt gebruikt als onderdeel van de API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in Virtual Machine Scale Sets moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor Azure SQL Database-servers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in Event hub moeten zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Er moeten systeemupdates op uw computers worden geïnstalleerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Controleer of ' Java version ' het meest recent is als onderdeel van de API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: SQL-servers moeten worden geconfigureerd met 90 dagen retentie of hoger.|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de web-app te openen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: verificatie moet zijn ingeschakeld in uw web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: voor geheimen moeten verloop datums zijn ingesteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect van het beleid: Controleer of ' HTTP version ' de meest recente is, indien gebruikt voor het uitvoeren van de API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: FTPS moet alleen vereist zijn in uw API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Zorg ervoor dat de nieuwste versie van Java wordt gebruikt als onderdeel van de functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Webtoepassing mag alleen toegankelijk zijn via HTTPS|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Controle op SQL-servers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Advanced Data Security moet zijn ingeschakeld op uw SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: geavanceerde gegevens beveiliging moet zijn ingeschakeld op een SQL-beheerd exemplaar|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Role-Based Access Control (RBAC) moet worden gebruikt voor Kubernetes-Services|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Ontbrekende Endpoint Protection bewaken in Azure Security Center|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Diagnostische logboeken in Search Services moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in App Services moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. Network/networkSecurityGroups/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. Network/networkSecurityGroups/securityRules/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. Network/networkSecurityGroups/securityRules/write)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. Network/networkSecurityGroups/schrijven)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. SQL/servers/firewallRules/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. SQL/servers/firewallRules/schrijven)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: alleen goedgekeurde VM-uitbrei dingen moeten worden geïnstalleerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor container registers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: beheerde identiteit moet worden gebruikt in uw API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: verificatie moet zijn ingeschakeld in uw API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beleids bewerkingen (micro soft. Authorization/policyAssignments/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beleids bewerkingen (micro soft. Authorization/policyAssignments/write)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: verificatie moet zijn ingeschakeld in uw functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in Data Lake Analytics moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: opslag accounts moeten toegang toestaan vanuit vertrouwde micro soft-Services|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in Key Vault moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-database servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de functie-app te openen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Gevolgen voor het beleid: RDP-toegang via internet moet worden geblokkeerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-database servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor het beleid: Zorg ervoor dat de functie-app client certificaten (binnenkomende client certificaten) heeft ingesteld op on|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: logboek controlepunten moeten zijn ingeschakeld voor PostgreSQL-database servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: logboek verbindingen moeten zijn ingeschakeld voor PostgreSQL-database servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: ontverbindingen moet worden vastgelegd voor PostgreSQL-database servers.|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in Service Bus moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in Azure Stream Analytics moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Effect voor beleid: opslag account met de container met activiteiten logboeken moet worden versleuteld met BYOK|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|AKS-clusters toevoegen wanneer er wordt gecontroleerd of Diagnostische logboeken voor virtuele-machine schaal sets zijn ingeschakeld||
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Nieuwste Java-versie voor App Services|De nieuwste ondersteunde Java-versie voor App Services|
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Meest recente python-versie voor Linux voor App Services|De nieuwste ondersteunde Python-versie voor App Services|
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Lijst met regio's waar Network Watcher moet worden ingeschakeld|Als u een volledige lijst met regio's wilt zien, voert u de Power shell-opdracht uit Get-AzLocation|
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Nieuwste PHP-versie voor App Services|De nieuwste ondersteunde PHP-versie voor App Services|
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Vereiste Bewaar periode (dagen) voor resource logboeken|Ga voor meer informatie over resource logboeken naar [https://aka.ms/resourcelogs](https://aka.ms/resourcelogs) |
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|De naam van de resource groep voor Network Watcher|De naam van de resource groep waarin de netwerk-volgers zich bevinden|
|CIS-Microsoft Azure Stichting voor bench Mark v 1.3.0|Beleidstoewijzing|Vereiste controle-instelling voor SQL-servers||

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
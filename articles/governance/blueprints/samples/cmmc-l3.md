---
title: CMMC Level 3-blauw druk-voor beeld
description: Overzicht van het CMMC Level 3-blauw druk-voor beeld. Met dit blauwdrukvoorbeeld kunnen klanten specifieke beheeropties bekijken.
ms.date: 03/24/2021
ms.topic: sample
ms.openlocfilehash: 950c6064ce8b3d9973ac08e5895a4b6f48e37d6a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572623"
---
# <a name="cmmc-level-3-blueprint-sample"></a>CMMC Level 3-blauw druk-voor beeld

De CMMC Level 3-blauw druk biedt beheer rails met behulp van [Azure Policy](../../policy/overview.md) die u helpen bij het beoordelen van specifieke Framework-besturings elementen voor [Cyber beveiliging (verval model certificering)](https://www.acq.osd.mil/cmmc/index.html) . Deze blauw druk helpt klanten bij het implementeren van een kern verzameling beleids regels voor elke Azure-geïmplementeerde architectuur die de besturings elementen voor CMMC niveau 3 moet implementeren.

## <a name="control-mapping"></a>Toewijzing van beheeropties

De [toewijzing van het Azure Policy besturings element](../../policy/samples/cmmc-l3.md) bevat details over beleids definities die zijn opgenomen in deze blauw druk en hoe deze beleids definities worden toegewezen aan de **besturings elementen** in het CMMC-Framework. Wanneer resources worden toegewezen aan een architectuur, worden deze op niet-naleving van toegewezen beleidsregels geëvalueerd door Azure Policy. Zie [Azure Policy](../../policy/overview.md) voor meer informatie.

## <a name="deploy"></a>Implementeren

De volgende stappen moeten worden uitgevoerd om het CMMC Level 3-blauw druk-voor beeld van Azure blauw drukken te implementeren:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

### <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het voor beeld van de blauw druk op **CMMC Level 3** onder _andere voor beelden_ en selecteer **dit voor beeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Blauw druk-naam**: Geef een naam op voor uw kopie van het voor beeld van het CMMC Level 3-blauw druk.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Bekijk de lijst met artefacten die in het blauwdrukvoorbeeld zijn opgenomen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

### <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het voor beeld van de blauw druk kan worden aangepast aan uw omgeving en behoeften, maar deze wijziging kan worden verplaatst van uitlijning met CMMC Level 3-besturings elementen.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **wijzigings notities** op, zoals ' eerste versie gepubliceerd vanuit het voor beeld van CMMC Level 3 '. Selecteer vervolgens **Publiceren** onder aan de pagina.

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
|CMMC niveau 3|Beleidstoewijzing|Aan arc verbonden servers toevoegen bij het evalueren van gast configuratie beleid|Door ' waar ' te selecteren, gaat u akkoord met de maandelijkse kosten per Arc verbonden machine. Ga voor meer informatie naar https://aka.ms/policy-pricing|
|CMMC niveau 3|Beleidstoewijzing|Lijst met gebruikers die moeten worden uitgesloten van de Windows VM-Beheerders groep|Een lijst met gebruikers die moeten worden uitgesloten in de lokale groep beheerders, gescheiden door puntkomma's. Bijvoorbeeld: Beheerder; myUser1; myUser2|
|CMMC niveau 3|Beleidstoewijzing|Lijst met gebruikers die moeten worden opgenomen in de groep beheerders voor Windows-VM's|Een lijst met gebruikers die moeten worden opgenomen in de lokale groep beheerders, gescheiden door puntkomma's. Bijvoorbeeld: Beheerder; myUser1; myUser2|
|CMMC niveau 3|Beleidstoewijzing|Id van de Log Analytics-werkruimte voor rapportage van de VM-agent|Id (GUID) van de Log Analytics-werkruimte waarin de VM-agents moeten rapporteren|
|CMMC niveau 3|Beleidstoewijzing|Toegestane elliptische-curve namen|De lijst met toegestane curve namen voor elliptische-curve certificaten.|
|CMMC niveau 3|Beleidstoewijzing|Toegestane sleutel typen|De lijst met toegestane sleutel typen|
|CMMC niveau 3|Beleidstoewijzing|Gebruik van Kubernetes voor het gehele cluster toestaan|Stel deze waarde in op waar als Pod is toegestaan om het hostcluster te gebruiken anders false.|
|CMMC niveau 3|Beleidstoewijzing|Wijziging in authenticatiebeleid controleren|Hiermee wordt aangegeven of controle gebeurtenissen worden gegenereerd wanneer wijzigingen worden aangebracht in het verificatie beleid. Deze instelling is handig voor het bijhouden van wijzigingen in de vertrouwens relatie op domein niveau en op forestniveau en machtigingen die worden verleend aan gebruikers accounts of-groepen.|
|CMMC niveau 3|Beleidstoewijzing|Wijziging in autorisatiebeleid controleren|Hiermee wordt aangegeven of controle gebeurtenissen worden gegenereerd voor toewijzing en verwijdering van gebruikers rechten in beleids regels voor gebruikers recht, wijzigingen in de machtiging voor het beveiligings token object, wijzigingen in bron kenmerken en wijzigingen in het centrale toegangs beleid voor bestandssysteem objecten.|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Azure Backup moet zijn ingeschakeld voor Virtual Machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Cognitive Services accounts moeten netwerk toegang beperken|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: door SQL beheerde instanties moeten door de klant beheerde sleutels worden gebruikt om gegevens in rust te versleutelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure API for FHIR moet een door de klant beheerde sleutel (CMK) gebruiken om gegevens in rust te versleutelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Web Application firewall (WAF) moet zijn ingeschakeld voor de Azure front-deur service|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: instellingen voor evaluatie van beveiligings problemen voor SQL Server moeten een e-mail adres bevatten voor het ontvangen van scan rapporten|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: open bare netwerk toegang moet zijn uitgeschakeld voor Cognitive Services accounts|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect van het beleid: CORS mag niet elke resource toestaan om toegang te krijgen tot uw functie-apps|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: aanbevelingen voor adaptieve netwerk beveiliging moeten worden toegepast op Internet gerichte virtuele machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Schijfversleuteling moet worden toegepast op virtuele machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: E-mailmelding aan abonnementseigenaar voor waarschuwingen met hoge urgentie moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: voor sleutel kluis moet de beveiliging leegmaken zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: SQL-servers moeten door de klant beheerde sleutels gebruiken om gegevens in rust te versleutelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor functie-apps|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor Key Vault moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect van het beleid: CORS mag niet toestaan dat elk domein toegang heeft tot uw API voor FHIR|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Windows-computers moeten voldoen aan de vereisten voor beveiligings opties-netwerk beveiliging|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Allowlist-regels in het adaptieve toepassings beheer beleid moeten worden bijgewerkt|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Web Application firewall (WAF) moet de opgegeven modus gebruiken voor Application Gateway|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: voor de sleutels moeten de verval datums zijn ingesteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Transparent Data Encryption in SQL-databases moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: in Azure Monitor logboek profiel moeten logboeken worden verzameld voor de categorieën schrijven, verwijderen, en actie|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: de evaluatie van beveiligings problemen moet worden ingeschakeld op het SQL-beheerde exemplaar|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect van het beleid: Zorg ervoor dat PHP-versie de meest recente is, als deze wordt gebruikt als onderdeel van de API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: voor de sleutel kluis moet de functie voor voorlopig verwijderen zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: alleen beveiligde verbindingen met uw Azure-cache voor redis moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: infrastructuur versleuteling moet zijn ingeschakeld voor Azure Database for PostgreSQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor App Service moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Windows-computers moeten voldoen aan de vereisten voor systeem controle beleid-beleids wijziging|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Cognitive Services accounts moeten gegevens versleuteling inschakelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: SSH-toegang via internet moet worden geblokkeerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: niet-gekoppelde schijven moeten worden versleuteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor opslag moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: opslag accounts moeten netwerk toegang beperken|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect van het beleid: CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: geavanceerde beveiliging tegen bedreigingen implementeren voor opslag accounts|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Automation-account variabelen moeten worden versleuteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in IoT Hub moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: infrastructuur versleuteling moet zijn ingeschakeld voor Azure Database for MySQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beveiligings bewerkingen (micro soft. Security/securitySolutions/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Windows-computers moeten voldoen aan de vereisten voor beveiligings opties-netwerk toegang|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect van het beleid: Azure Monitor moet activiteiten Logboeken uit alle regio's verzamelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Web Application firewall (WAF) moet de opgegeven modus gebruiken voor de Azure front-deur service|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: opslag accounts moeten infrastructuur versleuteling hebben|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: besturings elementen voor adaptieve toepassingen voor het definiëren van veilige toepassingen moeten worden ingeschakeld op uw computers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Windows-computers moeten voldoen aan de vereisten voor beveiligings opties-gebruikers account beheer|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Controleren of de Java-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor servers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: abonnementen moeten een e-mail adres voor contact personen hebben voor beveiligings problemen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: open bare toegang tot opslag account moet niet worden toegestaan|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: er moet een oplossing voor de evaluatie van beveiligings problemen worden ingeschakeld op uw virtuele machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor Kubernetes moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Firewall moet zijn ingeschakeld op Key Vault|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Web Application firewall (WAF) moet zijn ingeschakeld voor Application Gateway|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Gebruik van CORS mag er niet toe leiden dat elke resource toegang heeft tot uw webtoepassingen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Windows-machines controleren die het opnieuw gebruiken van de voor gaande 24 wacht woorden toestaan|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: container registers moeten worden versleuteld met een door de klant beheerde sleutel (CMK)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: open bare netwerk toegang moet worden uitgeschakeld voor PostgreSQL flexibele servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: beveiligings problemen in Azure Container Registry installatie kopieën moeten worden hersteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: voor Service Fabric clusters moet de eigenschap ClusterProtectionLevel zijn ingesteld op EncryptAndSign|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor SQL-servers op computers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Cognitive Services accounts moeten gegevens versleuteling met door de klant beheerde sleutel inschakelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Afgeschafte accounts moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Functie-app mag alleen toegankelijk zijn via HTTPS|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: e-mail melding voor waarschuwingen met hoge urgentie moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: opslag account moet door de klant beheerde sleutel gebruiken voor versleuteling|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect van het beleid: Zorg ervoor dat python-versie de meest recente is, als deze wordt gebruikt als onderdeel van de API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: sleutels moeten het opgegeven cryptografische type RSA of EC zijn|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure-abonnementen moeten een logboek profiel voor het activiteiten logboek hebben|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: zowel besturings systemen als gegevens schijven in azure Kubernetes-Service clusters moeten worden versleuteld door door de klant beheerde sleutels|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor Azure SQL Database-servers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Data Explorer versleuteling in rust moet een door de klant beheerde sleutel gebruiken|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: sleutels met RSA-crypto grafie moeten een opgegeven minimale sleutel grootte hebben|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Kubernetes cluster kan alleen gebruikmaken van een goedgekeurd host netwerk en poort bereik|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Er moeten systeemupdates op uw computers worden geïnstalleerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Windows-computers moeten voldoen aan de vereisten voor systeem controle beleid-gebruik van bevoegdheden|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Azure Stream Analytics-taken moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van gegevens|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Controleer of ' Java version ' het meest recent is als onderdeel van de API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de web-app te openen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect van het beleid: Controleer of ' HTTP version ' de meest recente is, indien gebruikt voor het uitvoeren van de API-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Microsoft IaaSAntimalware-uitbreiding moet zijn geïmplementeerd op Windows-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Zorg ervoor dat de nieuwste versie van Java wordt gebruikt als onderdeel van de functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: alle netwerk poorten moeten worden beperkt op netwerk beveiligings groepen die zijn gekoppeld aan uw virtuele machine|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: De prijscategorie Standard voor Security Center moet zijn geselecteerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Windows-machines controleren die de minimale wachtwoord lengte niet beperken tot 14 tekens|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Het gebruik van aangepaste RBAC-regels controleren|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Webtoepassing mag alleen toegankelijk zijn via HTTPS|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Controle op SQL-servers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: De Log Analytics-agent moet worden geïnstalleerd op virtuele machines|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Advanced Data Security moet zijn ingeschakeld op uw SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: geavanceerde gegevens beveiliging moet zijn ingeschakeld op een SQL-beheerd exemplaar|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Role-Based Access Control (RBAC) moet worden gebruikt voor Kubernetes-Services|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: virtuele machines moeten de gast configuratie-extensie hebben|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Ontbrekende Endpoint Protection bewaken in Azure Security Center|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: activiteiten logboek moet gedurende ten minste één jaar worden bewaard|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: beheer poorten van virtuele machines moeten worden beveiligd met Just-in-time-netwerk toegangs beheer|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: open bare netwerk toegang moet worden uitgeschakeld voor PostgreSQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Advanced Threat Protection implementeren voor Cosmos DB accounts|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Diagnostische logboeken in App Services moeten worden ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: API-app mag alleen toegankelijk zijn via HTTPS|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. ClassicNetwork/networkSecurityGroups/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. ClassicNetwork/networkSecurityGroups/securityRules/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. Network/networkSecurityGroups/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. Network/networkSecurityGroups/securityRules/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beheer bewerkingen (micro soft. SQL/servers/firewallRules/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: virtuele machines die niet zijn gericht op internet, moeten worden beveiligd met netwerk beveiligings groepen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: controleren van Windows-computers waarop de instelling voor wachtwoord complexiteit niet is ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Defender voor container registers moet zijn ingeschakeld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Azure Data Box-taken moeten dubbele versleuteling inschakelen voor gegevens in rust op het apparaat|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: micro soft antimalware voor Azure moet zo worden geconfigureerd dat beveiligings handtekeningen automatisch worden bijgewerkt|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: er moet een waarschuwing voor een activiteiten logboek bestaan voor specifieke beleids bewerkingen (micro soft. Authorization/policyAssignments/Delete)|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: open bare netwerk toegang moet worden uitgeschakeld voor MySQL-flexibele servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: opslag accounts moeten toegang toestaan vanuit vertrouwde micro soft-Services|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: voor certificaten met RSA-crypto grafie moet de minimale sleutel grootte worden opgegeven|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: container registers mogen geen onbeperkte netwerk toegang toestaan|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-database servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: gast configuratie-uitbrei ding moet worden geïmplementeerd op virtuele machines van Azure met door het systeem toegewezen beheerde identiteit|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Geografisch redundante back-up op de lange termijn moet zijn ingeschakeld voor Azure SQL Databases|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: open bare netwerk toegang moet worden uitgeschakeld voor MySQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Windows-machines controleren die geen wacht woorden opslaan met omkeer bare versleuteling|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Windows-computers moeten voldoen aan de vereisten voor de toewijzing van gebruikers rechten|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de functie-app te openen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Gevolgen voor het beleid: RDP-toegang via internet moet worden geblokkeerd|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Controleer Linux-machines waarop de passwd-bestands machtigingen niet zijn ingesteld op 0644|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: subnetten moeten worden gekoppeld aan een netwerk beveiligings groep|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-database servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor API-apps|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Linux-machines controleren die externe verbindingen toestaan van accounts zonder wacht woorden|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: dubbele versleuteling moet zijn ingeschakeld op Azure Data Explorer|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: De Log Analytics-agent moet worden geïnstalleerd op virtuele-machineschaalsets|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw web-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: schijf versleuteling moet zijn ingeschakeld op Azure Data Explorer|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Internet gerichte virtuele machines moeten worden beveiligd met netwerk beveiligings groepen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Linux-machines met accounts zonder wacht woorden controleren|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Azure Synapse-werk ruimten moeten door de klant beheerde sleutels gebruiken om gegevens in rust te versleutelen|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw functie-app|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Kubernetes Services moet worden bijgewerkt naar een niet-kwets bare Kubernetes-versie|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: alle Internet verkeer moet worden gerouteerd via uw geïmplementeerde Azure Firewall|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: Linux-machines moeten voldoen aan de vereisten voor de Azure-beveiligings basislijn|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor het beleid: open bare netwerk toegang moet worden uitgeschakeld voor MariaDB-servers|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in uw SQL-databases moeten worden opgelost|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Effect voor beleid: sleutels die gebruikmaken van elliptische curve-crypto grafie moeten de opgegeven curve namen hebben|Ga voor meer informatie over effecten naar [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|CMMC niveau 3|Beleidstoewijzing|Naam ruimten die zijn uitgesloten van de evaluatie van beleid: Kubernetes cluster-peul mag alleen een goedgekeurd host netwerk en poort bereik gebruiken|Lijst met Kubernetes-naam ruimten die moeten worden uitgesloten van de beleids evaluatie.|
|CMMC niveau 3|Beleidstoewijzing|Nieuwste Java-versie voor App Services|De nieuwste ondersteunde Java-versie voor App Services|
|CMMC niveau 3|Beleidstoewijzing|Meest recente python-versie voor Linux voor App Services|De nieuwste ondersteunde Python-versie voor App Services|
|CMMC niveau 3|Beleidstoewijzing|Optioneel: lijst met VM-installatie kopieën die ondersteunde Linux-besturings systemen bevatten om toe te voegen aan het bereik bij het controleren van de implementatie van Log Analytics agent|Voorbeeld waarde: '/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage '|
|CMMC niveau 3|Beleidstoewijzing|Optioneel: lijst met VM-installatie kopieën die een ondersteund Windows-besturings systeem hebben dat kan worden toegevoegd aan het bereik bij het controleren van de implementatie van Log Analytics agent|Voorbeeld waarde: '/Subscriptions/ <subscriptionId> /resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage '|
|CMMC niveau 3|Beleidstoewijzing|Lijst met regio's waar Network Watcher moet worden ingeschakeld|Controleer of Network Watcher niet is ingeschakeld voor een of meer regio's.|
|CMMC niveau 3|Beleidstoewijzing|Lijst met resourcetypen waarvoor diagnostische logboeken moeten zijn ingeschakeld||
|CMMC niveau 3|Beleidstoewijzing|De maximum waarde in het toegestane poort bereik van de host die in de hostnetwerkadapter kan worden gebruikt in de naam ruimte van het host netwerk|De maximum waarde in het poort bereik van de toegestane host die kan worden gebruikt in de naam ruimte van het host netwerk.|
|CMMC niveau 3|Beleidstoewijzing|Minimale RSA-sleutel grootte voor sleutels|De minimale sleutel grootte voor RSA-sleutels.|
|CMMC niveau 3|Beleidstoewijzing|Minimale certificaten voor RSA-sleutel grootte|De minimale sleutel grootte voor RSA-certificaten.|
|CMMC niveau 3|Beleidstoewijzing|Minimale TLS-versie voor Windows-webservers|Windows-webservers met lagere TLS-versies worden beoordeeld als niet-compatibel|
|CMMC niveau 3|Beleidstoewijzing|Minimum waarde in het toegestane poort bereik van de host die in de hostnetwerkadapter kan worden gebruikt in de naam ruimte van het host netwerk|De minimum waarde in het poort bereik van de toegestane host die in de naam ruimte van het host-netwerk kan worden gebruikt.|
|CMMC niveau 3|Beleidstoewijzing|Modus vereiste|Modus vereist voor alle WAF-beleids regels|
|CMMC niveau 3|Beleidstoewijzing|Modus vereiste|Modus vereist voor alle WAF-beleids regels|
|CMMC niveau 3|Beleidstoewijzing|Toegestane host-paden voor het gebruik van pod hostPath-volumes|De host-paden die zijn toegestaan voor het gebruik van pod hostPath-volumes. Geef een lege paden lijst op om alle host-paden te blok keren.|
|CMMC niveau 3|Beleidstoewijzing|Netwerktoegang: registerpaden die op afstand toegankelijk zijn|Hiermee bepaalt u welke registerpaden toegankelijk zijn via het netwerk, ongeacht de gebruikers of groepen die in de toegangsbeheerlijst (ACL) van de registersleutel `winreg` worden vermeld.|
|CMMC niveau 3|Beleidstoewijzing|Netwerktoegang: registerpaden en -subpaden die op afstand toegankelijk zijn|Hiermee bepaalt u welke registerpaden en -subpaden toegankelijk zijn via het netwerk, ongeacht de gebruikers of groepen die in de toegangsbeheerlijst (ACL) van de registersleutel `winreg` worden vermeld.|
|CMMC niveau 3|Beleidstoewijzing|Netwerktoegang: shares waarvoor anoniem toegang kan worden verkregen|Hiermee wordt aangegeven welke netwerkshares door anonieme gebruikers kunnen worden geopend. De standaardconfiguratie voor deze beleidsinstelling heeft weinig invloed omdat alle gebruikers moeten worden geverifieerd voordat ze toegang kunnen krijgen tot gedeelde resources op de server.|
|CMMC niveau 3|Beleidstoewijzing|Netwerk beveiliging: toegestane versleutelings typen configureren voor Kerberos|Hiermee geeft u de versleutelings typen die door Kerberos mogen worden gebruikt.|
|CMMC niveau 3|Beleidstoewijzing|Netwerkbeveiliging: LAN Manager-authenticatieniveau|Opgeven welk verificatie protocol voor Challenge-Response wordt gebruikt voor netwerk aanmeldingen. Deze keuze heeft betrekking op het niveau van het verificatie protocol dat door clients wordt gebruikt, het niveau van sessie beveiliging waarover wordt onderhandeld en het niveau van verificatie dat door servers wordt geaccepteerd.|
|CMMC niveau 3|Beleidstoewijzing|Netwerkbeveiliging: vereisten voor handtekening van LDAP-client|Geef het niveau van de gegevens ondertekening op dat wordt aangevraagd namens clients die LDAP-BINDINGs aanvragen verlenen.|
|CMMC niveau 3|Beleidstoewijzing|Netwerkbeveiliging: minimale sessiebeveiliging voor op NTLM SSP-gebaseerde (inclusief beveiligde RPC) clients|Hiermee geeft u op welk gedrag wordt toegestaan door clients voor toepassingen met behulp van de NTLM-SSP (Security Support Provider). De SSP-interface (SSPI) wordt gebruikt door toepassingen waarvoor verificatie services zijn vereist. Zie [https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/network-security-minimum-session-security-for-ntlm-ssp-based-including-secure-rpc-servers) voor meer informatie.|
|CMMC niveau 3|Beleidstoewijzing|Netwerkbeveiliging: minimale sessiebeveiliging voor op NTLM SSP-gebaseerde (inclusief beveiligde RPC) servers|Hiermee geeft u op welk gedrag door servers wordt toegestaan voor toepassingen die gebruikmaken van de NTLM-SSP (Security Support Provider). De SSP-interface (SSPI) wordt gebruikt door toepassingen waarvoor verificatie services zijn vereist.|
|CMMC niveau 3|Beleidstoewijzing|Nieuwste PHP-versie voor App Services|De nieuwste ondersteunde PHP-versie voor App Services|
|CMMC niveau 3|Beleidstoewijzing|Vereiste Bewaar periode (dagen) voor IoT Hub Diagnostische logboeken||
|CMMC niveau 3|Beleidstoewijzing|De naam van de resource groep voor Network Watcher|De naam van de resource groep van NetworkWatcher, zoals NetworkWatcherRG. Dit is de resource groep waar de netwerk zich bevinden.|
|CMMC niveau 3|Beleidstoewijzing|Vereiste controle-instelling voor SQL-servers||
|CMMC niveau 3|Beleidstoewijzing|Azure Data Box Sku's die ondersteuning bieden voor dubbele versleuteling op basis van software|De lijst met Azure Data Box Sku's die ondersteuning bieden voor dubbele versleuteling op basis van software|
|CMMC niveau 3|Beleidstoewijzing|UAC: modus door Administrator goed keuren voor het ingebouwde Administrator account|Hiermee geeft u het gedrag van de modus door Administrator goed keuren voor het ingebouwde Administrator-account.|
|CMMC niveau 3|Beleidstoewijzing|UAC: gedrag van de vraag om benodigde bevoegdheden voor beheerders in de modus door Administrator goed keuren|Hiermee geeft u het gedrag van de vraag om benodigde bevoegdheden voor beheerders.|
|CMMC niveau 3|Beleidstoewijzing|UAC: toepassings installaties detecteren en vragen om benodigde bevoegdheden|Hiermee geeft u het gedrag op voor het detecteren van toepassings installaties voor de computer.|
|CMMC niveau 3|Beleidstoewijzing|UAC: alle Administrators in de modus door Administrator goed keuren uitvoeren|Hiermee bepaalt u het gedrag van alle beleids instellingen voor Gebruikersaccountbeheer (UAC) voor de computer.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers en groepen die geforceerd kunnen worden afgesloten vanaf een extern systeem|Hiermee geeft u op welke gebruikers en groepen de computer mogen afsluiten vanaf een externe locatie op het netwerk.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers en groepen die geen toegang tot deze computer vanaf het netwerk hebben|Hiermee geeft u op welke gebruikers of groepen expliciet geen verbinding kunnen maken met de computer via het netwerk.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers en groepen die lokaal aanmelden zijn geweigerd|Hiermee geeft u op welke gebruikers en groepen expliciet niet mogen worden aangemeld bij de computer.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers en groepen die zich niet aanmelden als batch taak|Hiermee geeft u op welke gebruikers en groepen expliciet niet mogen worden aangemeld bij de computer als een batch-taak (d.w.z. geplande taak).|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers en groepen die zich niet aanmelden als service|Hiermee geeft u op welke service accounts expliciet geen proces als een service mogen registreren.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers en groepen die zich aanmelden via Extern bureaublad-services geweigerd|Hiermee geeft u op welke gebruikers en groepen expliciet niet mogen worden aangemeld bij de computer via Terminal Services/Extern bureaublad-client.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers en groepen die bestanden en mappen kunnen herstellen|Hiermee geeft u op welke gebruikers en groepen machtigingen voor bestanden, mappen, het REGI ster en andere permanente objecten mogen omzeilen bij het terugzetten van back-ups van bestanden en mappen.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers en groepen die het systeem kunnen afsluiten|Hiermee geeft u op welke gebruikers en groepen die lokaal zijn aangemeld op de computers in uw omgeving, het besturings systeem mogen afsluiten met de opdracht afsluiten.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers of groepen die lokaal kunnen worden aangemeld|Hiermee geeft u op welke externe gebruikers op het netwerk verbinding mogen maken met de computer. Dit omvat niet Verbinding met extern bureaublad.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers of groepen die een back-up van bestanden en mappen kunnen maken|Hiermee geeft u gebruikers en groepen op die machtigingen voor bestanden en mappen mogen omzeilen om een back-up van het systeem te maken.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers of groepen die de systeem tijd kunnen wijzigen|Hiermee geeft u op welke gebruikers en groepen de tijd en datum van de interne klok van de computer mogen wijzigen.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers of groepen die de tijd zone kunnen wijzigen|Hiermee geeft u op welke gebruikers en groepen de tijd zone van de computer mogen wijzigen.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers of groepen die een token object kunnen maken|Hiermee geeft u op welke gebruikers en groepen een toegangs token mogen maken. Dit kan verhoogde rechten bieden voor toegang tot gevoelige gegevens.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers of groepen die lokaal kunnen worden aangemeld|Hiermee geeft u op welke gebruikers of groepen zich interactief kunnen aanmelden bij de computer. Gebruikers die zich willen aanmelden via Verbinding met extern bureaublad of IIS, hebben ook dit gebruikers recht nodig.|
|CMMC niveau 3|Beleidstoewijzing|Extern bureaublad-gebruikers|Gebruikers of groepen die zich kunnen aanmelden via Extern bureaublad-services|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers of groepen die controle-en beveiligings logboeken kunnen beheren|Hiermee geeft u gebruikers en groepen op die de controle opties voor bestanden en mappen mogen wijzigen en het beveiligings logboek wissen.|
|CMMC niveau 3|Beleidstoewijzing|Gebruikers of groepen die eigenaar van bestanden of andere objecten kunnen worden|Hiermee geeft u op welke gebruikers en groepen eigenaar mogen worden van bestanden, mappen, register sleutels, processen of threads. Dit gebruikers recht omzeilt alle machtigingen die aanwezig zijn om objecten te beschermen om eigenaar te worden van de opgegeven gebruiker.|

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
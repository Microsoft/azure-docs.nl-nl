---
title: DoD Impact Level 5-blauwdrukvoorbeeld
description: Implementeer stappen voor het DoD Impact Level 5-blauwdrukvoorbeeld, inclusief de parametergegevens voor blauwdrukartefacten.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: d9292a9033a3c0b92e1a00c63d74bd1fb6023794
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386716"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>Het DoD Impact Level 5-blauwdrukvoorbeeld implementeren

Als u het DoD IL 5-blauwdrukvoorbeeld (Department of Defense Impact Level 5) van Azure Blueprints wilt implementeren, moet u de volgende stappen uitvoeren:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het blauwdrukvoorbeeld **DoD Impact Level 5** onder _Andere voorbeelden_, en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Naam van blauwdruk**: Geef een naam op voor uw kopie van het DoD Impact Level 5-blauwdrukvoorbeeld.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Controleer de lijst met artefacten die samen het blauwdrukvoorbeeld vormen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar door deze aanpassing is het mogelijk dat de kopie niet meer is afgestemd op de DoD Impact Level 5-beheeropties.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **Notities over wijzigingen** op, zoals 'Eerste gepubliceerde versie op basis van het DoD IL 5-blauwdrukvoorbeeld'. Selecteer vervolgens **Publiceren** onder aan de pagina.

## <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **Gepubliceerd**, kan het worden toegewezen aan een abonnement binnen de beheergroep waarin de kopie is opgeslagen. Dit is de stap waarin parameters worden opgegeven om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina Blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen**: Selecteer een of meer van de abonnementen in de beheergroep waarin u uw kopie van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt een toewijzing gemaakt voor elk abonnement waarvoor de ingevoerde parameters worden gebruikt.
     - **Naam van toewijzing**: De naam wordt vooraf voor u ingevuld op basis van de naam van de blauwdruk.
       Wijzig de naam als dat nodig is of gebruik de opgegeven naam.
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
     - **Blauwdrukdefinitieversie**: Kies een **gepubliceerde** versie van uw kopie van het blauwdrukvoorbeeld.

   - Toewijzing vergrendelen

     Selecteer de instelling voor het vergrendelen van de blauwdruk voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de optie voor de standaard door het _systeem toegewezen_ beheerde identiteit staan.

   - Artefactparameters

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder de parameter is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens de toewijzing van de blauwdruk. Zie de [tabel met artefactparameters](#artifact-parameters-table) voor een volledige lijst met artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert u **Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van het artefact begint. De implementatie duurt circa één uur. Open de blauwdruktoewijzing om de status van de implementatie te controleren.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis**. Azure-resources zijn [geprijsd per product](https://azure.microsoft.com/pricing/). Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voor het uitvoeren van resources die door dit blauwdrukvoorbeeld zijn geïmplementeerd.

## <a name="artifact-parameters-table"></a>Tabel met artefactparameters

In de volgende tabel ziet u een lijst met de blauwdrukartefactparameters:

|Naam van het artefact|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|DoD Impact Level 5|Beleidstoewijzing|Lijst met gebruikers die moeten worden opgenomen in de groep beheerders voor Windows-VM's|Een lijst met gebruikers die moeten worden opgenomen in de lokale groep beheerders, gescheiden door puntkomma's. Bijvoorbeeld: Beheerder; myUser1; myUser2|
|DoD Impact Level 5|Beleidstoewijzing|Lijst met gebruikers die zijn uitgesloten van de groep beheerders voor Windows-VM's|Een lijst met gebruikers die moeten worden uitgesloten in de lokale groep beheerders, gescheiden door puntkomma's. Bijvoorbeeld: Beheerder; myUser1; myUser2|
|DoD Impact Level 5|Beleidstoewijzing|Lijst met resourcetypen waarvoor diagnostische logboeken moeten zijn ingeschakeld||
|DoD Impact Level 5|Beleidstoewijzing|Id van de Log Analytics-werkruimte voor rapportage van de VM-agent|Id (GUID) van de Log Analytics-werkruimte waarin de VM-agents moeten rapporteren|
|DoD Impact Level 5|Beleidstoewijzing|Lijst met regio's waar Network Watcher moet worden ingeschakeld|Als u een volledige lijst met regio's wilt weergeven, gebruikt u Get-AzLocation|
|DoD Impact Level 5|Beleidstoewijzing|Minimale TLS-versie voor Windows-webservers|De minimale TLS-protocolversie die moet worden ingeschakeld op Windows-webservers|
|DoD Impact Level 5|Beleidstoewijzing|Meest recente PHP-versie|De nieuwste ondersteunde PHP-versie voor App Services|
|DoD Impact Level 5|Beleidstoewijzing|Meest recente Java-versie|De nieuwste ondersteunde Java-versie voor App Services|
|DoD Impact Level 5|Beleidstoewijzing|Meest recente Windows Python-versie|De nieuwste ondersteunde Python-versie voor App Services|
|DoD Impact Level 5|Beleidstoewijzing|Meest recente Linux Python-versie|De nieuwste ondersteunde Python-versie voor App Services|
|DoD Impact Level 5|Beleidstoewijzing|Optioneel: lijst met installatiekopieën van Windows-VM’s die ondersteuning bieden om de Log Analytics-agent aan het auditbereik toe te voegen|Een lijst met afbeeldingen, gescheiden door puntkomma's. Bijvoorbeeld: /subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Beleidstoewijzing|Optioneel: lijst met installatiekopieën van Linux-VM’s die ondersteuning bieden om de Log Analytics-agent aan het auditbereik toe te voegen|Een lijst met afbeeldingen, gescheiden door puntkomma's. Bijvoorbeeld: /subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Schijfversleuteling moet worden toegepast op virtuele machines|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: E-mailmelding aan abonnementseigenaar voor waarschuwingen met hoge urgentie moet zijn ingeschakeld|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor functie-apps|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de versie van .NET Framework de meest recente is als deze wordt gebruikt als onderdeel van de functie-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Transparent Data Encryption in SQL-databases moet zijn ingeschakeld|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Een Azure Active Directory-beheerder moet worden ingericht voor SQL-servers|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Alleen beveiligde verbindingen met uw Redis Cache moeten zijn ingeschakeld|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De Endpoint Protection-oplossing moet worden geïnstalleerd op virtuele-machineschaalsets|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Netwerktoegang tot opslagaccounts moet zijn beperkt|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De Advanced Data Security-instellingen voor SQL Managed Instance moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Besturingselementen voor adaptieve toepassingen om veilige toepassingen in de toegestane lijst op te nemen, moeten worden ingeschakeld op uw computers|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de Java-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Er moet een e-mailadres van de contactpersoon voor beveiliging worden opgeven voor uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Gebruik van CORS mag er niet toe leiden dat elke resource toegang heeft tot uw webtoepassingen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Externe accounts met schrijfmachtigingen moeten worden verwijderd uit uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Afgeschafte accounts moeten worden verwijderd uit uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Functie-app mag alleen toegankelijk zijn via HTTPS|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de functie-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de Python-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de versie van .NET Framework de meest recente is als deze wordt gebruikt als onderdeel van de web-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Er moeten systeemupdates op uw computers worden geïnstalleerd|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de Java-versie de meest recente is als deze wordt gebruikt als onderdeel van de API-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de web-app te openen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw API-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De Advanced Data Security-instellingen voor SQL Server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de API-app te openen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Microsoft IaaSAntimalware-uitbreiding moet zijn geïmplementeerd op Windows-servers|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Zorg ervoor dat de nieuwste versie van Java wordt gebruikt als onderdeel van de functie-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Toegang via een eindpunt gericht op internet moet worden beperkt|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De prijscategorie Standard voor Security Center moet zijn geselecteerd|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Het gebruik van aangepaste RBAC-regels controleren|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Webtoepassing mag alleen toegankelijk zijn via HTTPS|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controle op SQL-servers moet zijn ingeschakeld|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De Log Analytics-agent moet worden geïnstalleerd op virtuele machines|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De DDoS Protection-standaard moet zijn ingeschakeld|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de PHP-versie de meest recente is als deze wordt gebruikt als onderdeel van de functie-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Advanced Data Security moet zijn ingeschakeld op uw SQL-servers|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Advanced Data Security moet zijn ingeschakeld voor SQL Managed Instance|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: E-mailmeldingen naar beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen voor SQL Managed Instance|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Ontbrekende Endpoint Protection bewaken in Azure Security Center|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Er moet een telefoonnummer van de contactpersoon voor beveiliging worden opgeven voor uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor clientverificatie|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: API-app mag alleen toegankelijk zijn via HTTPS|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Advanced Threat Protection-typen moeten ingesteld zijn op Alles in de Advanced Data Security-instellingen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Geografisch redundante opslag moet zijn ingeschakeld voor opslagaccounts|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de versie van .NET Framework de meest recente is als deze wordt gebruikt als onderdeel van de API-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen voor SQL Server|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor webtoepassingen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Geografisch redundante back-up op de lange termijn moet zijn ingeschakeld voor Azure SQL Databases|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Controleren of de HTTP-versie de meest recente is als deze wordt gebruikt om de functie-app te openen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Advanced Threat Protection-typen moeten worden ingesteld op All in de Advanced Data Security-instellingen van SQL Server|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in beveiligingsconfiguraties voor containers moeten worden verholpen|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Externe foutopsporing moet worden uitgeschakeld voor API-apps|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Afgeschafte accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De Log Analytics-agent moet worden geïnstalleerd op virtuele-machineschaalsets|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw web-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Externe accounts met eigenaarsmachtigingen moeten worden verwijderd uit uw abonnement|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: De nieuwste TLS-versie moet worden gebruikt in uw functie-app|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: [Preview]: Kubernetes-services moeten worden geüpgraded naar een niet-kwetsbare Kubernetes-versie|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|
|DoD Impact Level 5|Beleidstoewijzing|Effect voor beleid: Beveiligingsproblemen in uw SQL-databases moeten worden opgelost|Azure Policy-effect voor dit beleid. Ga naar https://aka.ms/policyeffects voor meer informatie over effecten|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het DoD Impact Level 5-blauwdrukvoorbeeld hebt bekeken, raadpleegt u de volgende artikelen voor informatie over de blauwdruk en de toewijzing van besturingselementen:

> [!div class="nextstepaction"]
> [DoD Impact Level 5-blauwdruk - overzicht](./index.md)
> [DoD Impact Level 5-blauwdruk - toewijzing van beheeropties](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
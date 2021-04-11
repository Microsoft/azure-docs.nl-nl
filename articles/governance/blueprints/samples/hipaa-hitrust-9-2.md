---
title: Overzicht van het HIPAA HITRUST 9.2-blauwdrukvoorbeeld
description: Overzicht van het HIPAA HITRUST 9.2-blauwdrukvoorbeeld. Met dit blauwdrukvoorbeeld kunnen klanten specifieke HIPAA HITRUST 9.2- beheeropties evalueren.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: 168946319c11f31ee41594d82d9ff186dea232cd
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386581"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>HIPAA HITRUST 9.2-blauwdrukvoorbeeld

Het HIPAA HITRUST 9.2-blauwdrukvoorbeeld biedt richtlijnen voor governance met [Azure Policy](../../policy/overview.md), waarmee u bepaalde HIPAA HITRUST 9.2-beheeropties kunt evalueren. Deze blauwdruk helpt klanten een kernset met beleid te implementeren voor architectuur die in Azure is geïmplementeerd en waarin HIPAA HITRUST 9.2-beheeropties moeten worden geïmplementeerd.

## <a name="control-mapping"></a>Toewijzing van beheeropties

De [toewijzing van de Azure Policy-beheeroptie](../../policy/samples/hipaa-hitrust-9-2.md) bevat details over beleidsdefinities die in deze blauwdruk zijn opgenomen en hoe deze beleidsdefinities worden toegewezen aan de **nalevingsdomeinen** en **beheeropties** in HIPAA HITRUST 9.2. Wanneer resources worden toegewezen aan een architectuur, worden deze op niet-naleving van toegewezen beleidsregels geëvalueerd door Azure Policy. Zie [Azure Policy](../../policy/overview.md) voor meer informatie.

## <a name="deploy"></a>Implementeren

Als u het HIPAA HITRUST 9.2-blauwdrukvoorbeeld van Azure Blueprints wilt implementeren, moet u de volgende stappen uitvoeren:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

### <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het blauwdrukvoorbeeld **HIPAA HITRUST** onder _Andere voorbeelden_ en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Naam van blauwdruk**: Geef een naam op voor uw kopie van het HIPAA HITRUST 9.2-blauwdrukvoorbeeld.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Controleer de lijst met artefacten die samen het blauwdrukvoorbeeld vormen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

### <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar door die aanpassing is het mogelijk dat de kopie niet meer is afgestemd op de beheeropties van HIPAA HITRUST 9.2.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **Notities over wijzigingen** op, zoals 'Eerste gepubliceerde versie op basis van het HIPAA HITRUST 9.2-blauwdrukvoorbeeld'. Selecteer vervolgens **Publiceren** onder aan de pagina.

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

|Naam van het artefact |Parameternaam |Beschrijving |
|---|---|---|
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Toegang via een eindpunt gericht op internet moet worden beperkt |Controle van binnenkomende NSG-regels met te veel machtigingen in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Accounts: status van het gastaccount |Hiermee bepaalt u of het lokale gastaccount is uitgeschakeld. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Besturingselementen voor adaptieve toepassingen om veilige toepassingen in de toegestane lijst op te nemen, moeten worden ingeschakeld op uw computers |De bewaking van een lijst met goedgekeurde toepassingen in Azure Security Center in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Gelijktijdige verbindingen met internet of een Windows-domein toestaan |Geef op of moet worden voorkomen dat computers tegelijkertijd verbinding maken met een netwerk op domeinbasis en een netwerk dat niet op een domein is gebaseerd. De waarde 0 geeft aan dat gelijktijdige verbindingen zijn toegestaan; met de waarde 1 worden gelijktijdige verbindingen geblokkeerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |API-app mag alleen toegankelijk zijn via HTTPS V2 |De bewaking van het gebruik van HTTPS in een API-app V2 in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Toepassingsnamen (ondersteunt jokertekens) |Een door puntkomma's gescheiden lijst met de namen van de toepassingen die moeten worden geïnstalleerd. Bijvoorbeeld Microsoft SQL Server 2014 (64-bits); Microsoft Visual Studio Code of Microsoft SQL Server 2014* (in overeenstemming met een toepassing die begint met Microsoft SQL Server 2014) |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Het beëindigen van processen controleren |Hiermee wordt aangegeven of controlegebeurtenissen worden gegenereerd wanneer een proces is afgesloten. Wordt aanbevolen voor het bewaken van de beëindiging van kritieke processen. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Onbeperkte netwerktoegang tot opslagaccounts controleren |De bewaking van netwerktoegang tot opslagaccounts in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Controle: systeem onmiddellijk afsluiten als beveiligingscontroles niet in logboek kunnen worden opgeslagen |Hiermee voert u een audit uit als het systeem wordt afgesloten wanneer beveiligingsgebeurtenissen niet in een logboek kunnen worden opgeslagen. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Certificaatvingerafdrukken |Een door puntkomma's gescheiden lijst met certificaatvingerafdrukken die aanwezig moet zijn in de opslag voor vertrouwde basiscertificaten (Cert:\LocalMachine\Root). bijvoorbeeld THUMBPRINT1;THUMBPRINT2;THUMBPRINT3 |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Diagnostische logboeken in Batch-accounts moeten worden ingeschakeld |De bewaking van diagnostische logboeken in Batch-accounts in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Diagnostische logboeken in Event Hub moeten zijn ingeschakeld |De bewaking van diagnostische logboeken in Event Hub-accounts in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Diagnostische logboeken in zoekservices moeten zijn ingeschakeld |De bewaking van diagnostische logboeken in Azure Search-service in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Diagnostische logboeken in Microsoft Azure Virtual Machine Scale Sets moeten zijn ingeschakeld |De bewaking van diagnostische logboeken in Service Fabric in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Schijfversleuteling moet worden toegepast op virtuele machines |De bewaking van VM-schijfversleuteling in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Onveilige gastaanmeldingen toestaan |Hiermee bepaalt u of de SMB-client onveilige gastaanmeldingen op een SMB-server toestaat. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines |De bewaking van Just in Time-netwerktoegang in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Beheerpoorten moeten gesloten zijn op uw virtuele machines |De bewaking van geopende beheerpoorten op VM's in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |MFA moet zijn ingeschakeld voor accounts met schrijfmachtigingen voor uw abonnement |De bewaking van het gebruik van MFA voor accounts met schrijfmachtigingen in een abonnement in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |MFA moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement |De bewaking van het gebruik van MFA voor accounts met eigenaarsmachtigingen in een abonnement in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Netwerktoegang: registerpaden die op afstand toegankelijk zijn |Hiermee bepaalt u welke registerpaden toegankelijk zijn via het netwerk, ongeacht de gebruikers of groepen die in de toegangsbeheerlijst (ACL) van de registersleutel `winreg` worden vermeld. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Netwerktoegang: registerpaden en -subpaden die op afstand toegankelijk zijn |Hiermee bepaalt u welke registerpaden en -subpaden toegankelijk zijn via het netwerk, ongeacht de gebruikers of groepen die in de toegangsbeheerlijst (ACL) van de registersleutel `winreg` worden vermeld. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Netwerktoegang: shares waarvoor anoniem toegang kan worden verkregen |Hiermee wordt aangegeven welke netwerkshares door anonieme gebruikers kunnen worden geopend. De standaardconfiguratie voor deze beleidsinstelling heeft weinig invloed omdat alle gebruikers moeten worden geverifieerd voordat ze toegang kunnen krijgen tot gedeelde resources op de server. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Herstelconsole: kopiëren naar diskette en toegang tot alle schijven en alle mappen toestaan |Hiermee bepaalt u of de SET-opdracht van de herstelconsole beschikbaar wordt gesteld. Met deze opdracht kunnen de omgevingsvariabelen van de herstelvariabele worden ingesteld. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Externe foutopsporing moet worden uitgeschakeld voor API-app |De bewaking van externe foutopsporing voor een API-app in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Externe foutopsporing moet worden uitgeschakeld voor webtoepassing |De bewaking van externe foutopsporing voor een web-app in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Vereiste bewaarperiode (in dagen) van logboeken in Batch-accounts |De vereiste bewaarperiode in dagen van de diagnostische logboeken |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Vereiste bewaarperiode (in dagen) van logboeken in Azure Search-service |De vereiste bewaarperiode in dagen van de diagnostische logboeken |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Vereiste bewaarperiode (in dagen) van logboeken in Event Hub-accounts |De vereiste bewaarperiode in dagen van de diagnostische logboeken |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Resourcegroepnaam voor opslagaccount (moet al bestaan) voor de implementatie van diagnostische instellingen voor netwerkbeveiligingsgroepen |De resourcegroep waarin het opslagaccount wordt gemaakt. Deze resourcegroep moet al bestaan. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Op rollen gebaseerd toegangsbeheer (RBAC) moet worden gebruikt voor Kubernetes Services |De bewaking van Kubernetes Services zonder RBAC in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |TDE-beveiliging van SQL Managed Instance moet worden versleuteld met uw eigen sleutel |De bewaking van Transparent Data Encryption (TDE) met ondersteuning van uw eigen sleutel in- of uitschakelen. TDE met ondersteuning van uw eigen sleutels voorziet in verbeterde transparantie en controle voor TDE-beveiliging, verbeterde beveiliging via een externe service met HSM en bevordering van scheiding van taken. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |TDE-beveiliging van de SQL-server moet worden versleuteld met uw eigen sleutel |De bewaking van Transparent Data Encryption (TDE) met ondersteuning van uw eigen sleutel in- of uitschakelen. TDE met ondersteuning van uw eigen sleutels voorziet in verbeterde transparantie en controle voor TDE-beveiliging, verbeterde beveiliging via een externe service met HSM en bevordering van scheiding van taken. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Opslagaccountvoorvoegsel voor regionaal opslagaccount voor de implementatie van diagnostische instellingen voor netwerkbeveiligingsgroepen |Dit voorvoegsel vormt in combinatie met de locatie van de netwerkbeveiligingsgroep de naam van het gemaakte opslagaccount. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd |Rapportage van systeemupdates voor virtuele-machineschaalsets in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Systeemupdates op virtuele-machineschaalsets moeten worden geïnstalleerd |Rapportage van systeemupdates voor virtuele-machineschaalsets in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Multicast-naamomzetting uitschakelen |Hiermee bepaalt u of LLMNR, een secundair naamomzettingsprotocol waarmee gegevens via multicast over een lokale subnetkoppeling op één subnet worden verzonden, wordt ingeschakeld. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Virtuele machines moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources |De bewaking van klassieke reken-VM's in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Beveiligingsproblemen in de beveiligingsconfiguratie van virtuele-machineschaalsets moeten worden hersteld |Bewaking van beveiligingsproblemen van besturingssystemen voor virtuele-machineschaalsets in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen |De detectie van VM-beveiligingsproblemen door een evaluatieoplossing in- of uitschakelen |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren |Controleer beheerde SQL-exemplaren waarvoor geen terugkerende evaluatie van beveiligingsproblemen is ingeschakeld. Met een evaluatie van beveiligingsproblemen kunt u potentiële beveiligingsproblemen van de database detecteren, bijhouden en herstellen. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (domein): Lokale firewallregels toepassen |Hiermee bepaalt u of lokale beheerders lokale firewallregels mogen maken die van toepassing zijn in combinatie met firewallregels die vanuit groepsbeleid voor het profiel Domein zijn geconfigureerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (domein): De standaardactie voor uitgaande verbindingen |Hiermee geeft u het gedrag op voor uitgaande verbindingen voor het profiel Domein dat niet overeenkomt met een uitgaande firewallregel. Standaardwaarde 0 betekent dat verbindingen zijn toegestaan en de waarde 1 betekent dat verbindingen worden geblokkeerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (domein): De standaardactie voor uitgaande verbindingen |Hiermee geeft u het gedrag op voor uitgaande verbindingen voor het profiel Domein dat niet overeenkomt met een uitgaande firewallregel. Standaardwaarde 0 betekent dat verbindingen zijn toegestaan en de waarde 1 betekent dat verbindingen worden geblokkeerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (domein): Meldingen weergeven |Hiermee bepaalt u voor het profiel Domein of meldingen in Windows Firewall met geavanceerde beveiliging voor gebruikers worden weergegeven wanneer het ontvangen van inkomende verbindingen voor een programma is geblokkeerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (domein): Profielinstellingen gebruiken |Hiermee wordt aangegeven of Windows Firewall met geavanceerde beveiliging de instellingen voor het profiel Domein gebruikt voor het filteren van netwerkverkeer. Als u Uit selecteert, worden in Windows Firewall met geavanceerde beveiliging geen van de firewallregels of verbindingsbeveiligingsregels voor dit profiel gebruikt. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (privé): Lokale verbindingsbeveiligingsregels toepassen |Hiermee wordt bepaald of lokale beheerders verbindingsbeveiligingsregels mogen maken die van toepassing zijn in combinatie met verbindingsbeveiligingsregels die vanuit groepsbeleid voor het profiel Privé zijn geconfigureerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (privé): Lokale firewallregels toepassen |Hiermee wordt bepaald of lokale beheerders lokale firewallregels mogen maken die van toepassing zijn in combinatie met firewallregels die vanuit groepsbeleid voor het profiel Privé zijn geconfigureerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (privé): De standaardactie voor uitgaande verbindingen |Hiermee geeft u het gedrag op voor uitgaande verbindingen voor het profiel Privé dat niet overeenkomt met een uitgaande firewallregel. Standaardwaarde 0 betekent dat verbindingen zijn toegestaan en de waarde 1 betekent dat verbindingen worden geblokkeerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (privé): Meldingen weergeven |Hiermee bepaalt u voor het profiel Privé of meldingen in Windows Firewall met geavanceerde beveiliging voor gebruikers worden weergegeven wanneer het ontvangen van inkomende verbindingen voor een programma is geblokkeerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (privé): Profielinstellingen gebruiken |Hiermee wordt aangegeven of Windows Firewall met geavanceerde beveiliging de instellingen voor het profiel Privé gebruikt voor het filteren van netwerkverkeer. Als u Uit selecteert, worden in Windows Firewall met geavanceerde beveiliging geen van de firewallregels of verbindingsbeveiligingsregels voor dit profiel gebruikt. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (openbaar): Lokale verbindingsbeveiligingsregels toepassen |Hiermee bepaalt u of lokale beheerders verbindingsbeveiligingsregels mogen maken die van toepassing zijn in combinatie met verbindingsbeveiligingsregels die vanuit groepsbeleid voor het profiel Openbaar zijn geconfigureerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (openbaar): Lokale firewallregels toepassen |Hiermee bepaalt u of lokale beheerders lokale firewallregels mogen maken die van toepassing zijn in combinatie met firewallregels die vanuit groepsbeleid voor het profiel Openbaar zijn geconfigureerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (openbaar): De standaardactie voor uitgaande verbindingen |Hiermee geeft u het gedrag op voor uitgaande verbindingen voor het profiel Openbaar dat niet overeenkomt met een uitgaande firewallregel. Standaardwaarde 0 betekent dat verbindingen zijn toegestaan en de waarde 1 betekent dat verbindingen worden geblokkeerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (openbaar): Meldingen weergeven |Hiermee bepaalt u voor het profiel Openbaar of meldingen in Windows Firewall met geavanceerde beveiliging voor gebruikers worden weergegeven wanneer het ontvangen van inkomende verbindingen voor een programma is geblokkeerd. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall (openbaar): Profielinstellingen gebruiken |Hiermee wordt aangegeven of Windows Firewall met geavanceerde beveiliging de instellingen voor het profiel Openbaar gebruikt voor het filteren van netwerkverkeer. Als u Uit selecteert, worden in Windows Firewall met geavanceerde beveiliging geen van de firewallregels of verbindingsbeveiligingsregels voor dit profiel gebruikt. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall: Domain: Unicast-antwoord toestaan |Hiermee bepaalt u voor het profiel Domein of unicast-antwoorden op de uitgaande multicast- of broadcastberichten kunnen worden ontvangen via Windows Firewall met geavanceerde beveiliging op de lokale computer. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall: Privé: Unicast-antwoord toestaan |Hiermee bepaalt u voor het profiel Privé of unicast-antwoorden op de uitgaande multicast- of broadcastberichten kunnen worden ontvangen via Windows Firewall met geavanceerde beveiliging op de lokale computer. |
|HITRUST/HIPAA-controles controleren en specifieke VM-extensies ter ondersteuning van de controlevereisten implementeren |Windows Firewall: Openbaar: Unicast-antwoord toestaan |Hiermee bepaalt u voor het profiel Openbaar of unicast-antwoorden op de uitgaande multicast- of broadcastberichten kunnen worden ontvangen via Windows Firewall met geavanceerde beveiliging op de lokale computer. |

## <a name="next-steps"></a>Volgende stappen

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).

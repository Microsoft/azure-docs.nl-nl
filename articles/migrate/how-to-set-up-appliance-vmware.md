---
title: Een Azure Migrate apparaat instellen voor VMware
description: Meer informatie over het instellen van een Azure Migrate apparaat om servers in VMware-omgeving te beoordelen en te migreren.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 685d7f0a0aaab2f38967e0eb6c32c3fb4067dbe3
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612820"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>Een apparaat instellen voor servers in VMware-omgeving

Volg dit artikel om het Azure Migrate-apparaat in te stellen voor evaluatie met de Azure Migrate: hulp programma voor [detectie en evaluatie](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) en voor migratie zonder agent met behulp van het Azure migrate: hulp programma voor [server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Het [Azure migrate apparaat](migrate-appliance.md) is een licht gewicht dat door Azure migrate wordt gebruikt: detectie en evaluatie en server migratie om servers te detecteren die in vCenter Server worden uitgevoerd, Server configuratie-en prestatie-meta gegevens te verzenden naar Azure, en voor replicatie van servers die gebruikmaken van agentloze migratie.

U kunt het apparaat implementeren met een aantal methoden:

- Maak een server op vCenter Server met behulp van een gedownloade eicellen-sjabloon. Dit is de methode die in dit artikel wordt beschreven.
- Stel het apparaat in op een bestaande server met behulp van een Power shell-installatie script. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u geen eicellen-sjabloon kunt gebruiken of als u zich in azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of het een verbinding kan maken met Azure Migrate: detectie en evaluatie, registreert u het met het project en configureert u het apparaat om detectie te initiëren.

## <a name="deploy-with-ova"></a>Implementeren met OVA

Als u het apparaat wilt instellen met behulp van een OVA-sjabloon, doet u het volgende:

1. Geef een naam op voor het apparaat en Genereer een project sleutel in de portal.
1. Download een OVA-sjabloonbestand en importeer het naar vCenter Server. Controleer of de eicellen veilig zijn.
1. Maak de toestel-VM op basis van het bestand van de eicellen en controleer of het verbinding kan maken met Azure Migrate.
1. Configureer het apparaat voor de eerste keer en registreer het met het project met behulp van de project sleutel.

### <a name="1-generate-the-project-key"></a>1. de project sleutel genereren

1. In **migratie doelen**  >  **servers**  >  **Azure migrate: detectie en evaluatie** selecteert u **detecteren**.
2. In **Discover-servers**  >  **zijn uw servers gevirtualiseerd?**, selecteert u **Ja, met VMware vSphere Hyper Visor**.
3. Geef in **1: project sleutel genereren** een naam op voor het Azure migrate apparaat dat u wilt instellen voor de detectie van servers in uw VMware-omgeving. De naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de detectie pagina niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt een project sleutel * * gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="2-download-the-ova-template"></a>2. de sjabloon van de eicellen downloaden

In **2: Azure Migrate-apparaat downloaden**, selecteert u het OVA-bestand en klikt u op **Downloaden**.

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het OVA-bestand veilig is voordat u het implementeert:

1. Open een Administrator-opdracht venster op de server waarnaar u het bestand hebt gedownload.
2. Gebruik de volgende opdracht om de hash voor het OVA-bestand te genereren:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Controleer de meest recente versie van het apparaat en de hashwaarden:

    - Voor de openbare Azure-cloud:
    
        **Algoritme** | **Downloaden** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-create-the-appliance-server"></a>3. de toestel server maken

Het gedownloade bestand importeren en een server maken in de VMware-omgeving

1. Klik in de Client vSphere-console op **Bestand** > **OVF-sjabloon implementeren**.
2. Geef in de wizard OVF-sjabloon implementeren > **Bron** de locatie van het OVA-bestand op.
3. Geef bij **naam** en **locatie** een beschrijvende naam op voor de-server. Selecteer het inventaris object waarin de server wordt gehost.
5. Geef in **host/cluster** de host of het cluster op waarop de server wordt uitgevoerd.
6. Geef in **opslag** de opslag bestemming voor de server op.
7. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
8. Geef bij **netwerk toewijzing** het netwerk op waarmee de server verbinding maakt. Het netwerk heeft Internet connectiviteit nodig om meta gegevens naar Azure Migrate te verzenden.
9. Controleer en bevestig de instellingen en klik op **Voltooien**.

### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de toestel server verbinding kan maken met Azure-Url's voor [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.

### <a name="4-configure-the-appliance"></a>4. het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [**Power shell-script**](deploy-appliance-script.md) in plaats van de gedownloade eicellen, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in de vSphere-client console met de rechter muisknop op de server en selecteer vervolgens **console openen**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een wille keurige server die verbinding kan maken met de toestel server en open de URL van het configuratie beheer van het apparaat: `https://appliance name or IP address: 44368` .

   U kunt de Configuration Manager ook openen via het bureau blad van de toestel server door de snelkoppeling voor Configuration Manager te selecteren.
1. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
1. Ga als volgt te werk in de Configuration Manager-> vereisten in te **stellen**:
   - **Connectiviteit**: het apparaat controleert of de server toegang heeft tot internet. Als de server gebruikmaakt van een proxy:
     - Klik op **proxy instellen** om het proxy adres op te geven in de poort van het formulier `http://ProxyIPAddress` of het `http://ProxyFQDN` Luis teren.
     - Geef referenties op als de proxy verificatie nodig heeft.
     - Alleen HTTP-proxy wordt ondersteund.
     - Als u proxydetails hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteitscontrole opnieuw te activeren.
   - **Tijdsynchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat detectie goed werkt.
   - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates zijn geïnstalleerd. Nadat de controle is voltooid, kunt u klikken op **apparaten weer geven** om de status en versies van de services die op de toestel server worden uitgevoerd te bekijken.
   - **VDDK installeren**: het apparaat controleert of VMware vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als dat niet het geval is, downloadt u VDDK 6.7 van VMware en extraheert u de inhoud van het gedownloade zipbestand naar de opgegeven locatie op het apparaat. Zie voor meer informatie de **installatie-instructies**.

     Azure Migrate server migratie gebruikt de VDDK om servers te repliceren tijdens de migratie naar Azure. 
1. Als u wilt, kunt u **de vereisten op elk gewenst moment opnieuw uitvoeren** tijdens de configuratie van het apparaat om te controleren of het apparaat nog steeds voldoet aan alle vereisten.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Paneel 1 op het configuratie beheer van het apparaat":::

## <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **project sleutel** die u hebt gekopieerd uit de portal. Als u de sleutel niet hebt, gaat u naar **detectie en evaluatie> detecteert> bestaande apparaten te beheren**, selecteert u de naam van het apparaat dat u hebt ingevoerd op het moment van sleutel genereren en kopieert u de bijbehorende sleutel.
1. U hebt een apparaatcode nodig om te verifiëren bij Azure. Als u klikt op **Aanmelden**, wordt er een modaal met de apparaatcode geopend, zoals hieronder weergegeven.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modaal waarin de apparaatcode wordt weergegeven":::

1. Klik op **Code kopiëren en aanmelden** om de apparaatcode te kopiëren en een Azure-aanmeldingsprompt te openen op een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Plak op het tabblad Nieuw de code van het apparaat en meld u aan met behulp van uw Azure-gebruikers naam en-wacht woord.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Als u het aanmeldingstabblad per ongeluk sluit zonder u aan te melden, vernieuwt u het browsertabblad van Apparaatconfiguratiebeheer om de knop Aanmelden opnieuw in te schakelen.
1. Als u bent aangemeld, gaat u terug naar het vorige tabblad in Apparaatconfiguratiebeheer.
1. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste machtigingen  heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratiedetails zien door op **Details weergeven** te klikken.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Deel 2 van het configuratie beheer van het apparaat":::

## <a name="start-continuous-discovery"></a>Continue detectie starten

### <a name="provide-vcenter-server-details"></a>vCenter Server Details opgeven

Het apparaat moet verbinding maken met vCenter Server om de configuratie-en prestatie gegevens van de servers te detecteren.

1. In **stap 1: geef vCenter Server referenties** op en klik op **referenties toevoegen** om een beschrijvende naam voor de referenties op te geven, Voeg **gebruikers naam** en **wacht woord** toe voor het vCenter Server account dat het apparaat gebruikt om servers te detecteren die worden uitgevoerd op de vCenter Server.
    - U moet een account met de vereiste machtigingen hebben ingesteld, zoals in dit artikel wordt besproken.
    - Als u de detectie van de scope wilt beperken tot specifieke VMware-objecten (vCenter Server Data Centers, clusters, een map met clusters, hosts, een map van hosts of afzonderlijke servers.), raadpleegt u de instructies in [dit artikel](set-discovery-scope.md) om het account dat wordt gebruikt door Azure migrate te begrenzen.
1. In **stap 2: geef vCenter Server Details** op, klik op **detectie bron toevoegen** om de beschrijvende naam voor referenties in de vervolg keuzelijst te selecteren, geeft u het **IP-adres/de FQDN** van de vCenter Server op. U kunt **Poort** op 443 laten staan (de standaardinstelling) of een aangepaste poort opgeven waarop vCenter Server luistert en op **Opslaan** klikken.
1. Wanneer u op **Opslaan** klikt, probeert het apparaat de verbinding met de vCenter Server te valideren met de referenties die zijn gegeven en wordt de **validatie status** in de tabel vergeleken met het vCenter Server IP-adres/de FQDN-naam.
1. U kunt de connectiviteit opnieuw **valideren** om op elk gewenst moment te vCenter Server voordat de detectie wordt gestart.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Paneel op toestel Configuration Manager voor vCenter Server Details":::

### <a name="provide-server-credentials"></a>Server referenties opgeven

In **stap 3: Server referenties opgeven voor het uitvoeren van software-inventaris, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases**. u kunt ervoor kiezen om meerdere Server referenties op te geven of als u deze functies niet wilt gebruiken, kunt u de stap overs Laan en door gaan met vCenter Server detectie. U kunt uw intentie op elk later tijdstip wijzigen.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panel 3 op het apparaat Configuration Manager voor Server Details":::


Als u gebruik wilt maken van deze functies, kunt u de referenties van de server opgeven door de volgende stappen uit te voeren. Het apparaat zal proberen de referenties automatisch toe te wijzen aan de servers om de detectie functies uit te voeren.

- U kunt Server referenties toevoegen door te klikken op de knop **referenties toevoegen** . Hiermee opent u een modaal, waar u het **type referenties** kunt kiezen in de vervolg keuzelijst.
- U kunt referenties voor domein/Windows (niet-domein)/Linux (niet-domein)/SQL Server verificatie opgeven. Meer [informatie](add-server-credentials.md) over hoe u referenties kunt opgeven en hoe u deze kunt afhandelen.
- Voor elk type referenties moet u een beschrijvende naam voor de referenties opgeven, **gebruikers naam** en **wacht woord** toevoegen en op **Opslaan** klikken.
- Als u domein referenties kiest, moet u ook de FQDN voor het domein opgeven. De FQDN is vereist voor het valideren van de authenticiteit van de referenties met de Active Directory van dat domein.
- Controleer de [vereiste machtigingen](add-server-credentials.md#required-permissions) voor het account voor detectie van geïnstalleerde toepassingen, afhankelijkheids analyse zonder agent of voor detectie van SQL Server instanties en data bases.
- Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **Meer toevoegen** om meer referenties op te slaan en toe te voegen.
- Wanneer u op **Opslaan** of **meer toevoegen** klikt, valideert het apparaat de domein referenties met de Active Directory van het domein voor de echtheid. Dit wordt gedaan om account vergrendelingen te voor komen wanneer het apparaat meerdere iteraties gebruikt om referenties aan de desbetreffende servers toe te wijzen.
- U kunt de **validatie status** voor alle domein referenties in de tabel referenties bekijken. Alleen domein referenties worden gevalideerd.
- Als de validatie mislukt, kunt u op de status **mislukt** klikken om de gevonden fout te zien en klikt u op **referenties opnieuw valideren** nadat het probleem is opgelost en de referenties voor het mislukte domein opnieuw te valideren.
    :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Panel 3 op het apparaat Configuration Manager voor het opgeven van meerdere referenties":::

### <a name="start-discovery"></a>Detectie starten

1. Klik op **detectie starten** om te beginnen met de detectie van vCenter Server. Nadat de detectie is gestart, kunt u de detectie status controleren op basis van het vCenter Server IP-adres/de FQDN in de tabel bron.
1. Als u Server referenties hebt ingesteld, wordt de software-inventarisatie (detectie van geïnstalleerde toepassingen) automatisch gestart nadat de detectie van vCenter Server is voltooid. De software-inventarisatie wordt één keer per 12 uur uitgevoerd.
1. [Software-inventaris](how-to-discover-applications.md) identificeert de SQL Server exemplaren die worden uitgevoerd op de servers en met behulp van de informatie, het apparaat probeert verbinding te maken met de exemplaren via de Windows-verificatie of SQL Server verificatie referenties die op het apparaat zijn verstrekt en gegevens verzamelen op SQL server data bases en de bijbehorende eigenschappen. De SQL-detectie wordt elke 24 uur uitgevoerd.
1. Tijdens software-inventarisatie worden de referenties van de toegevoegde servers herhaald op servers en gevalideerd voor de afhankelijkheids analyse zonder agent. U kunt afhankelijkheids analyse zonder agent inschakelen voor servers vanuit de portal. Alleen de servers waarop de validatie slaagt, kunnen worden geselecteerd voor het inschakelen van afhankelijkheids analyse zonder agent.

Detectie werkt als volgt:

- Het duurt ongeveer 15 minuten voordat de gedetecteerde servers inventarisatie wordt weer gegeven in de portal.
- De detectie van geïnstalleerde toepassingen kan enige tijd duren. De duur is afhankelijk van het aantal gedetecteerde servers. Voor 500-servers duurt het ongeveer een uur voordat de gedetecteerde inventaris wordt weer gegeven in de Azure Migrate Portal.
- Nadat de detectie van servers is voltooid, kunt u een afhankelijkheids analyse zonder agent inschakelen op de servers van de portal.
- SQL Server instanties en data base-gegevens worden binnen 24 uur weer gegeven in de portal vanaf de detectie-start.

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelf studies voor [VMware-evaluatie](./tutorial-assess-vmware-azure-vm.md) en migratie zonder [agent](tutorial-migrate-vmware.md).

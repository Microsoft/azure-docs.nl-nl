---
title: Een Azure Migrate apparaat instellen voor VMware
description: Meer informatie over het instellen van een Azure Migrate apparaat om VMware-Vm's te beoordelen en te migreren.
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: f20bb77c29d98ab4e3549bfed43d47d1f1f7dc0c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013629"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Een apparaat instellen voor VMware-Vm's

Volg dit artikel om het Azure Migrate-apparaat in te stellen voor evaluatie met het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool en voor migratie zonder agent met behulp van het Azure migrate: hulp programma voor [server migratie](migrate-services-overview.md#azure-migrate-server-migration-tool) .

Het [Azure migrate apparaat](migrate-appliance.md) is een licht gewicht dat door Azure migrate wordt gebruikt: Server evaluatie en server migratie om on-premises virtuele VMware-machines te detecteren, VM-meta gegevens/prestatie gegevens naar Azure te verzenden en voor replicatie van virtuele VMware-machines tijdens migratie zonder agent.

U kunt het apparaat implementeren met een aantal methoden:

- Ingesteld op een virtuele VMware-machine met behulp van een gedownloade OVA-sjabloon. Dit is de methode die in dit artikel wordt beschreven.
- Ingesteld op een VMware-VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u geen virtuele machine kunt instellen met behulp van een eicellen-sjabloon of als u zich in azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate-serverevaluatie, configureert u het voor de eerste keer en registreert u het bij het Azure Migrate-project.


## <a name="appliance-deployment-ova"></a>Implementatie van het apparaat (eicellen)

Als u het apparaat wilt instellen met behulp van een OVA-sjabloon, doet u het volgende:
- Geef een naam op voor het apparaat en genereer een Azure Migrate-projectsleutel in de portal
- Download een OVA-sjabloonbestand en importeer het naar vCenter Server.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate-serverevaluatie.
- Configureer het apparaat voor het eerst en registreer het bij het Azure Migrate-project met behulp van de Azure Migrate-projectsleutel.

### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate-projectsleutel genereren

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere-hypervisor**.
3. In **1: Azure Migrate-projectsleutel genereren** geeft u een naam op voor het Azure Migrate-apparaat dat u voor de detectie van VMware-VM's wilt instellen. De naam moet alfanumeriek zijn en 14 tekens of minder bevatten.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina Computers detecteren niet tijdens het maken van resources.
1. Nadat de Azure-resources zijn gemaakt, wordt er een **Azure Migrate-projectsleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="download-the-ova-template"></a>De OVA-sjabloon downloaden
In **2: Azure Migrate-apparaat downloaden**, selecteert u het OVA-bestand en klikt u op **Downloaden**. 


   ![Selecties voor Computers detecteren](./media/tutorial-assess-vmware/servers-discover.png)


   ![Selecties voor Sleutel genereren](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het bestand van de eicellen veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de eicellen te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```
3. De gegenereerde hash moet overeenkomen met deze [instellingen](./tutorial-discover-vmware.md#verify-security)voor de meest recente versie van het apparaat.



## <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak een virtuele machine.

1. Klik in de Client vSphere-console op **Bestand** > **OVF-sjabloon implementeren**.
![Menuopdracht voor het implementeren van een OVF-sjabloon](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Geef in de wizard OVF-sjabloon implementeren > **Bron** de locatie van het OVA-bestand op.
3. Geef bij **Naam** en **Locatie** een beschrijvende naam op voor de virtuele machine. Selecteer het inventarisobject waarin de VM wordt gehost.
5. Geef bij **Host/Cluster** de host of het cluster op waarop de VM wordt uitgevoerd.
6. Geef bij **Opslag** de opslaglocatie voor de VM op.
7. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
8. Geef onder **Netwerktoewijzing** het netwerk op waarmee de virtuele machine verbinding maakt. Het netwerk heeft internetconnectiviteit nodig om metagegevens naar Azure Migrate-serverevaluatie te verzenden.
9. Controleer en bevestig de instellingen en klik op **Voltooien**.


## <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.


### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade OVA, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in de vSphere-clientconsole met de rechtermuisknop op de VM en selecteer **Console openen**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de VM en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door de snelkoppeling naar de app te selecteren.
1. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
   - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine een proxy gebruikt:
     - Klik op **Proxy instellen** en geef het proxyadres (in het formulier http://ProxyIPAddress of http://ProxyFQDN) ) en de controlepoort op.
     - Geef referenties op als de proxy verificatie nodig heeft.
     - Alleen HTTP-proxy wordt ondersteund.
     - Als u proxydetails hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteitscontrole opnieuw te activeren.
   - **Tijdsynchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat detectie goed werkt.
   - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates zijn geïnstalleerd. Als de controle is voltooid, kunt u op **Apparaatservices weergeven** klikken om de status en versies te zien van de onderdelen die op het apparaat worden uitgevoerd.
   - **VDDK installeren**: het apparaat controleert of VMware vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als dat niet het geval is, downloadt u VDDK 6.7 van VMware en extraheert u de inhoud van het gedownloade zipbestand naar de opgegeven locatie op het apparaat. Zie voor meer informatie de **installatie-instructies**.

     Azure Migrate-servermigratie gebruikt de VDDK om computers te repliceren tijdens migratie naar Azure. 
1. Als u wilt, kunt u **de vereisten op elk gewenst moment opnieuw uitvoeren** tijdens de configuratie van het apparaat om te controleren of het apparaat nog steeds voldoet aan alle vereisten.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **Azure Migrate-projectsleutel** die u in de portal hebt gekopieerd. Als u de sleutel niet hebt, gaat u naar **Serverevaluatie > Detecteren > Bestaande apparaten beheren**, selecteert u de naam van het apparaat die u hebt ingevoerd op het moment dat de sleutel werd gegenereerd en kopieert u de bijbehorende sleutel.
1. Klik op **Aanmelden**. Er wordt een Azure-aanmeldingsprompt geopend in een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app. 
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste [machtigingen ](./tutorial-discover-vmware.md#prepare-an-azure-user-account) heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratiedetails zien door op **Details weergeven** te klikken.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Het apparaat moet verbinding maken met vCenter Server om de configuratie- en prestatiegegevens van de virtuele machines te detecteren.

1. In **Stap 1: Geef referenties voor vCenter Server op**, klik op **Referenties toevoegen** om een beschrijvende naam voor de referenties op te geven, voeg een **gebruikersnaam** en een **wachtwoord** toe voor het VCenter Server-account dat door het apparaat wordt gebruikt voor het detecteren van VM's op de instantie van vCenter Server.
    - U moet een account met de vereiste machtigingen hebben ingesteld in de [vorige zelfstudie](./tutorial-discover-vmware.md#create-an-account-to-access-vcenter).
    - Als u het detectiebereik wilt beperken tot specifieke VMware-objecten (vCenter Server-datacenters, clusters, een map met clusters, hosts, een map met hosts of afzonderlijke VM's), raadpleegt u de instructies in [dit artikel](set-discovery-scope.md) om het account dat wordt gebruikt door Azure Migrate te begrenzen.
1. In **Stap 2: vCenter Server-gegevens opgeven**, klikt u op **Detectiebron toevoegen** om de beschrijvende naam voor referenties te selecteren in de vervolgkeuzelijst en geeft u het **IP-adres/FQDN** van de vCenter Server-instantie op. U kunt **Poort** op 443 laten staan (de standaardinstelling) of een aangepaste poort opgeven waarop vCenter Server luistert en op **Opslaan** klikken.
1. Wanneer u op Opslaan klikt, probeert het apparaat de verbinding met vCenter Server te valideren met de opgegeven referenties en wordt de **validatiestatus** in de tabel weergegeven voor het IP-adres of de FQDN van vCenter Server.
1. Voordat u de detectie start, kunt u de connectiviteit met vCenter Server altijd **opnieuw valideren**.
1. In **stap 3: VM-referenties opgeven voor het detecteren van geïnstalleerde toepassingen en voor het uitvoeren van afhankelijkheidstoewijzing zonder agent** klikt u op **Referenties toevoegen** en geeft u het besturingssysteem op waarvoor de referenties zijn opgegeven, evenals een beschrijvende naam voor de referenties en een **gebruikersnaam** en **wachtwoord**. Klik vervolgens op **Opslaan**.

    - U kunt hier eventueel referenties toevoegen als u een account hebt gemaakt dat u wilt gebruiken voor de functie voor [toepassingsdetectie](how-to-discover-applications.md) of de [functie voor analyse van afhankelijkheden zonder agent](how-to-create-group-machine-dependencies-agentless.md).
    - Als u deze functies niet wilt gebruiken, klikt u op de schuifregelaar om de stap over te slaan. U kunt de intentie op elk gewenst moment ongedaan maken.
    - Controleer de referenties die nodig zijn voor [toepassingsdetectie](migrate-support-matrix-vmware.md#application-discovery-requirements) of voor [afhankelijkheidsanalyse zonder agent](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Klik op **Detectie starten** om VM-detectie te starten. Nadat de detectie is gestart, kunt u de detectiestatus voor het IP-adres of de FQDN van vCenter Server controleren in de tabel.

Detectie werkt als volgt:
- Het duurt ongeveer 15 minuten voordat gedetecteerde VM-metagegevens worden weergegeven in de portal.
- Detectie van geïnstalleerde toepassingen, functies en onderdelen kan enige tijd duren. De duur is afhankelijk van het aantal VM's dat wordt gedetecteerd. Voor 500 VM's duurt het ongeveer een uur voordat de inventaris van toepassingen wordt weergegeven in de Azure Migrate-portal.

## <a name="next-steps"></a>Volgende stappen

Bekijk de zelf studies voor [VMware-evaluatie](./tutorial-assess-vmware-azure-vm.md) en migratie zonder [agent](tutorial-migrate-vmware.md).
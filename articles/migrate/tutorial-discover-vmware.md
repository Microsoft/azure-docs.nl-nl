---
title: VMware-VM's detecteren met Azure Migrate-serverevaluatie
description: Meer informatie over het detecteren van on-premises virtuele VMware-machines met het Azure Migrate-hulpprogramma voor serverevaluatie
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: e11c3277ffa07fe0a8d5fc7495e2c09152ce585f
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704283"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Zelfstudie: Virtuele VMware-machines detecteren met Serverevaluatie

Als onderdeel van de migratie naar Azure, detecteert u uw on-premises inventaris en werkbelastingen. 

In deze zelfstudie leert u meer over het detecteren van virtuele VMware-machines met Azure Migrate- Serverevaluatie en een licht Azure Migrate-apparaat. U implementeert het apparaat als een virtuele VMware-machine, zodat er voortdurend sprake is van detectie van virtuele machines en prestatiegegevens, apps die worden uitgevoerd op virtuele machines en VM-afhankelijkheden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure-account instellen.
> * De VMware-omgeving voorbereiden voor detectie.
> * Maak een Azure Migrate-project.
> * Het Azure Migrate-apparaat instellen.
> * Continue detectie starten.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken waar mogelijk de standaardopties.  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

Controleer of deze vereisten aanwezig zijn voordat u met deze zelfstudie begint.


**Vereiste** | **Details**
--- | ---
**vCenter Server/ESXi host** | U hebt een vCenter-server met versie 5.5, 6.0, 6.5 of 6.7 nodig.<br/><br/> Virtuele machines moeten worden gehost op een ESXi-host waarop versie 5.5 of hoger wordt uitgevoerd.<br/><br/> Sta op de vCenter-server binnenkomende verbindingen op TCP-poort 443 toe, zodat het apparaat evaluatiegegevens kan verzamelen.<br/><br/> Het apparaat maakt standaard verbinding met vCenter op poort 443. Als de vCenter-server naar een andere poort luistert, kunt u de poort wijzigen wanneer u vanaf het apparaat verbinding maakt met de server om de detectie te starten.<br/><br/> Zorg ervoor dat op de EXSi-server die als host fungeert voor de virtuele machines, binnenkomende toegang is toegestaan op TCP-poort 443 voor app-detectie.
**Apparaat** | vCenter Server heeft resources nodig om een virtuele machine toe te wijzen voor het Azure Migrate-apparaat:<br/><br/> - Windows Server 2016<br/><br/> -32 GB RAM, acht vCPU's en ongeveer 80 GB aan schijfruimte.<br/><br/> -Een externe virtuele switch en internettoegang voor de virtuele machine, rechtstreeks of via een proxy.
**VM's** | Voor het gebruik van deze zelfstudie moet op virtuele Windows-machines Windows Server 2016, 2012 R2, 2012 of 2008 R2 worden uitgevoerd.<br/><br/> Op Virtuele Linux-machines moet Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 of CentOS 5/6/7 worden uitgevoerd.<br/><br/> Op virtuele machines moeten VMware-hulpprogramma's (een versie die hoger is dan 10.2.0) worden geïnstalleerd en uitgevoerd.<br/><br/> Op virtuele Windows-machines moet Windows Power Shell 2.0 of hoger zijn geïnstalleerd.


## <a name="prepare-an-azure-user-account"></a>Een Azure-gebruikersaccount voorbereiden

Om een Azure Migrate-project te maken en het Azure Migrate-apparaat te registreren, hebt u een account nodig met:
- Machtigingen op inzender- of eigenaarniveau voor een Azure-abonnement.
- Machtigingen verlenen om Azure Active Directory-apps te registreren.

Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement. Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de volgende machtigingen toe te wijzen:

1. Zoek in de Azure Portal naar 'Abonnementen' en selecteer onder **Services** **Abonnementen**.

    ![Zoekvak om te zoeken naar het Azure-abonnement](./media/tutorial-discover-vmware/search-subscription.png)

2. Selecteer op de pagina **Abonnementen** het abonnement waarin u een Azure Migrate-project wilt maken. 
3. Selecteer onder het abonnement de optie **Toegangsbeheer (IAM)**  > **Toegang controleren**.
4. Zoek onder **Toegang controleren** naar het relevante gebruikersaccount.
5. Klik onder **Een roltoewijzing toevoegen** op **Toevoegen**.

    ![Een gebruikersaccount zoeken om toegang te controleren en een rol toe te wijzen](./media/tutorial-discover-vmware/azure-account-access.png)

6. Selecteer onder **Roltoewijzing toevoegen** de rol Inzender of Eigenaar en selecteer account (azmigrateuser in ons voorbeeld). Klik vervolgens op **Opslaan**.

    ![Hiermee opent u de pagina Roltoewijzing toevoegen om een rol aan het account toe te wijzen](./media/tutorial-discover-vmware/assign-role.png)

7. Zoek in de portal naar gebruikers en selecteer onder **Services** **Gebruikers**.
8. Controleer onder **Gebruikersinstellingen** of Azure AD-gebruikers toepassingen kunnen registreren (standaard ingesteld op **Ja**).

    ![Verifiëren onder Gebruikersinstellingen of gebruikers Active Directory-apps kunnen registreren](./media/tutorial-discover-vmware/register-apps.png)

9. Als alternatief kan een tenant/globale beheerder de rol van **toepassingsontwikkelaar** toewijzen aan het account om de registratie van AAD-apps mogelijk te maken. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>VMware voorbereiden

Maak op de vCenter-server een account dat het apparaat kan gebruiken om toegang te krijgen tot de vCenter-server en controleer of de vereiste poorten zijn geopend. U hebt ook een account nodig dat het apparaat kan gebruiken om toegang te krijgen tot de virtuele machines. 

### <a name="create-an-account-to-access-vcenter"></a>Een account maken voor toegang tot vCenter

Stel in de vSphere-webclient als volgt een account in:

1. Gebruik een account met beheerdersbevoegdheden in de vSphere-webclient en selecteer **Beheer**.
2. **Toegang**, selecteer **SSO-gebruikers en -groepen**.
3. Voeg onder **Gebruiker** een nieuwe gebruiker toe.
4. Typ onder **Nieuwe gebruiker** de accountgegevens. Klik vervolgens op **OK**.
5. Selecteer onder **Globale machtigingen** het gebruikersaccount en wijs de rol **Alleen-lezen** toe aan het account. Klik vervolgens op **OK**.
6. Selecteer onder **Rollen** de rol **Alleen-lezen** en selecteert onder **Bevoegdheden** **Gastbewerkingen**. Deze bevoegdheden zijn nodig om apps te detecteren die worden uitgevoerd op virtuele machines en om VM-afhankelijkheden te analyseren.
 
    ![Selectievakje om gastbewerkingen toe te staan voor de rol Alleen-lezen](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Een account maken voor toegang tot virtuele machines

Het apparaat opent virtuele machines om apps te detecteren en VM-afhankelijkheden te analyseren. Het apparaat installeert geen agents op virtuele machines.

1. Maak een lokaal beheerdersaccount dat het apparaat kan gebruiken om apps en afhankelijkheden op virtuele Windows-machines te detecteren.
2. Maak voor Linux-machines een gebruikersaccount met hoofdbevoegdheden of een gebruikersaccount met deze machtigingen voor /bin/netstat- en /bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.

> [!NOTE]
> Azure Migrate ondersteunt één referentie voor detectie van apps op alle Windows-servers en één referentie voor detectie van apps op alle Linux-machines.


## <a name="set-up-a-project"></a>Een project instellen

Stel als volgt een nieuw Azure Migrate-project in.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer onder **Overzicht** de optie **Project maken**.
5. Selecteer onder **Project maken** uw Azure-abonnement en resourcegroep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Vakken voor projectnaam en regio](./media/tutorial-discover-vmware/new-project.png)

7. Selecteer **Maken**.
8. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd.

Het hulpprogramma **Azure Migrate: Serverevaluatie** wordt standaard toegevoegd aan het nieuwe project.

![Pagina waarop wordt weergegeven dat het hulpprogramma Serverevaluatie standaard wordt toegevoegd](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Het apparaat instellen

Als u het apparaat wilt instellen met behulp van een OVA-sjabloon, doet u het volgende:
- Geef een naam op voor het apparaat en genereer een Azure Migrate-projectsleutel in de portal
- Download een OVA-sjabloonbestand en importeer het naar vCenter Server.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate-serverevaluatie.
- Configureer het apparaat voor het eerst en registreer het bij het Azure Migrate-project met behulp van de Azure Migrate-projectsleutel.

> [!NOTE]
> Als u het apparaat om de een of andere reden niet kunt instellen met behulp van de sjabloon, kunt u het instellen met behulp van een PowerShell-script. [Meer informatie](deploy-appliance-script.md#set-up-the-appliance-for-vmware).


### <a name="deploy-with-ova"></a>Implementeren met OVA

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


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het OVA-bestand veilig is voordat u het implementeert:

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Gebruik de volgende opdracht om de hash voor het OVA-bestand te genereren:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Controleer de meest recente versie van het apparaat en de hashwaarden:

    - Voor de openbare Azure-cloud:
    
        **Algoritme** | **Downloaden** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Voor Azure Government:
    
        **Algoritme** | **Downloaden** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca




### <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak een virtuele machine.

1. Klik in de Client vSphere-console op **Bestand** > **OVF-sjabloon implementeren**.
2. Geef in de wizard OVF-sjabloon implementeren > **Bron** de locatie van het OVA-bestand op.
3. Geef bij **Naam** en **Locatie** een beschrijvende naam op voor de virtuele machine. Selecteer het inventarisobject waarin de VM wordt gehost.
5. Geef bij **Host/Cluster** de host of het cluster op waarop de VM wordt uitgevoerd.
6. Geef bij **Opslag** de opslaglocatie voor de VM op.
7. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
8. Geef onder **Netwerktoewijzing** het netwerk op waarmee de virtuele machine verbinding maakt. Het netwerk heeft internetconnectiviteit nodig om metagegevens naar Azure Migrate-serverevaluatie te verzenden.
9. Controleer en bevestig de instellingen en klik op **Voltooien**.


### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

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
1. U hebt een apparaatcode nodig om te verifiëren bij Azure. Als u klikt op **Aanmelden**, wordt er een modaal met de apparaatcode geopend, zoals hieronder weergegeven.

    ![Modaal waarin de apparaatcode wordt weergegeven](./media/tutorial-discover-vmware/device-code.png)

1. Klik op **Code kopiëren en aanmelden** om de apparaatcode te kopiëren en een Azure-aanmeldingsprompt te openen op een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Plak de apparaatcode in het nieuwe tabblad en meld u aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Als u het aanmeldingstabblad per ongeluk sluit zonder u aan te melden, vernieuwt u het browsertabblad van Apparaatconfiguratiebeheer om de knop Aanmelden opnieuw in te schakelen.
1. Als u bent aangemeld, gaat u terug naar het vorige tabblad in Apparaatconfiguratiebeheer.
1. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste machtigingen  heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratiedetails zien door op **Details weergeven** te klikken.



## <a name="start-continuous-discovery"></a>Continue detectie starten

Het apparaat moet verbinding maken met vCenter Server om de configuratie- en prestatiegegevens van de virtuele machines te detecteren.

1. In **Stap 1: Geef referenties voor vCenter Server op**, klik op **Referenties toevoegen** om een beschrijvende naam voor de referenties op te geven, voeg een **gebruikersnaam** en een **wachtwoord** toe voor het VCenter Server-account dat door het apparaat wordt gebruikt voor het detecteren van VM's op de instantie van vCenter Server.
    - U moet een account met de vereiste machtigingen hebben ingesteld in de vorige zelfstudie.
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

- [VMware-VM's evalueren](./tutorial-assess-vmware-azure-vm.md) voor migratie naar virtuele Azure-machines.
- [De gegevens controleren ](migrate-appliance.md#collected-data---vmware) die door het apparaat worden verzameld tijdens de detectie.
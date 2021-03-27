---
title: Servers op Hyper-V detecteren met Azure Migrate detectie en evaluatie
description: Meer informatie over het detecteren van on-premises servers op Hyper-V met het hulp programma voor detectie en evaluatie van Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: f461778f988fafeacc480e100b00be7d4c165dfb
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612514"
---
# <a name="tutorial-discover-servers-running-on-hyper-v-with-azure-migrate-discovery-and-assessment"></a>Zelf studie: servers die worden uitgevoerd op Hyper-V detecteren met Azure Migrate: detectie en evaluatie

Als onderdeel van de migratie naar Azure, detecteert u uw on-premises inventaris en werkbelastingen.

Deze zelf studie laat zien hoe u on-premises servers op Hyper-V-hosts kunt detecteren met de Azure Migrate: hulp programma voor detectie en evaluatie met behulp van een licht gewicht Azure Migrate apparaat. U implementeert het apparaat als een server op een Hyper-V-host, zodat de meta gegevens van de computer en de prestaties continu worden gedetecteerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure-account instellen
> * De Hyper-V-omgeving voorbereiden voor detectie.
> * Een project maken.
> * Het Azure Migrate-apparaat instellen.
> * Continue detectie starten.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken de standaardopties.  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Controleer of deze vereisten aanwezig zijn voordat u met deze zelfstudie begint.

**Vereiste** | **Details**
--- | ---
**Hyper-V-host** | Hyper-V-hosts waarop servers zich bevinden, kunnen zelfstandig zijn of in een cluster.<br/><br/> Op deze host moet Windows Server 2019 R2, Windows Server 2016 of Windows Server 2012 R2 worden uitgevoerd.<br/><br/> Controleer of binnenkomende verbindingen zijn toegestaan op WinRM-poort 5985 (HTTP), zodat het apparaat verbinding kan maken met de meta gegevens van de server en de prestaties, met behulp van een Common Information Model CIM-sessie.
**Implementatie van het apparaat** | De Hyper-V-host heeft resources nodig om een server toe te wijzen aan het apparaat:<br/><br/> -16 GB aan RAM, 8 Vcpu's en ongeveer 80 GB aan schijf opslag.<br/><br/> -Een externe virtuele switch en Internet toegang op het apparaat rechtstreeks of via een proxy.
**Servers** | Op servers kan elk Windows-of Linux-besturings systeem worden uitgevoerd.

## <a name="prepare-an-azure-user-account"></a>Een Azure-gebruikersaccount voorbereiden

Als u een project wilt maken en het Azure Migrate apparaat wilt registreren, hebt u een account nodig met:
- Machtigingen op inzender- of eigenaarniveau voor een Azure-abonnement.
- Machtigingen voor het registreren van Azure Active Directory-apps (AAD).

Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement. Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de volgende machtigingen toe te wijzen:

1. Zoek in de Azure Portal naar 'Abonnementen' en selecteer onder **Services** **Abonnementen**.

    ![Zoekvak om te zoeken naar het Azure-abonnement](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Selecteer op de pagina **abonnementen** het abonnement waarin u een project wilt maken.
3. Selecteer onder het abonnement de optie **Toegangsbeheer (IAM)**  > **Toegang controleren**.
4. Zoek onder **Toegang controleren** naar het relevante gebruikersaccount.
5. Klik onder **Een roltoewijzing toevoegen** op **Toevoegen**.

    ![Een gebruikersaccount zoeken om toegang te controleren en een rol toe te wijzen](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. Selecteer onder **Roltoewijzing toevoegen** de rol Inzender of Eigenaar en selecteer account (azmigrateuser in ons voorbeeld). Klik vervolgens op **Opslaan**.

    ![Hiermee opent u de pagina Roltoewijzing toevoegen om een rol aan het account toe te wijzen](./media/tutorial-discover-hyper-v/assign-role.png)

1. Als u het apparaat wilt registreren, heeft uw Azure-account **machtigingen nodig voor het registreren van Aad-apps.**
1. Ga in azure Portal naar **Azure Active Directory**  >  **gebruikers**  >  **gebruikers instellingen**.
1. Controleer onder **Gebruikersinstellingen** of Azure AD-gebruikers toepassingen kunnen registreren (standaard ingesteld op **Ja**).

    ![Verifiëren onder Gebruikersinstellingen of gebruikers Active Directory-apps kunnen registreren](./media/tutorial-discover-hyper-v/register-apps.png)

9. Als de App-registraties-instellingen is ingesteld op Nee, vraagt u de Tenant/globale beheerder om de vereiste machtiging toe te wijzen. De Tenant/globale beheerder kan de rol van **toepassings ontwikkelaar** ook toewijzen aan een account om de registratie van de Aad-app toe te staan. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Hyper-V-hosts voorbereiden

U kunt Hyper-V-hosts hand matig voorbereiden of een script gebruiken. De voorbereidings stappen worden in de tabel samenvatten. Het script bereidt deze automatisch voor.

**Stap** | **Script** | **Handmatig**
--- | --- | ---
De vereisten voor de host controleren | Controleert of op de host een ondersteunde versie van Hyper-V en de Hyper-V-rol wordt uitgevoerd.<br/><br/>Hiermee schakelt u de WinRM-service in en opent u poort 5985 (HTTP) en 5986 (HTTPS) op de host (vereist voor de verzameling metagegevens). | Op deze host moet Windows Server 2019 R2, Windows Server 2016 of Windows Server 2012 R2 worden uitgevoerd.<br/><br/> Controleer of binnenkomende verbindingen zijn toegestaan op WinRM-poort 5985 (HTTP), zodat het apparaat verbinding kan maken met de meta gegevens van de server en de prestaties, met behulp van een Common Information Model CIM-sessie.<br/><br/> Het script wordt momenteel niet ondersteund op hosts met een niet-Engelse land instelling.  
PowerShell-versie bevestigen | Controleert of u het script uitvoert op een ondersteunde PowerShell-versie. | Controleer of u PowerShell versie 4.0 of hoger uitvoert op de Hyper-V-host.
Een account maken | Verifieert of u de juiste machtigingen hebt op de Hyper-V-host.<br/><br/> Hiermee kunt u een lokaal gebruikers account met de juiste machtigingen maken. | Optie 1: Maak een account met beheerderstoegang op de Hyper-V-hostmachine.<br/><br/> Optie 2: Bereid een lokaal beheerdersaccount of een domeinbeheerdersaccount voor en voeg het account toe aan deze groepen: Gebruikers van extern beheer, Hyper-V-beheerders, en Prestatiemetergebruikers.
Externe communicatie met PowerShell inschakelen | Hiermee schakelt u externe communicatie van Power shell op de host in, zodat het Azure Migrate apparaat Power shell-opdrachten op de host kan uitvoeren via een WinRM-verbinding. | Als u op elke host wilt instellen, opent u een Power shell-console als beheerder en voert u de volgende opdracht uit: ``` powershell Enable-PSRemoting -force ```
Hyper-V-integratie Services instellen | Hiermee wordt gecontroleerd of de Hyper-V-integratie Services zijn ingeschakeld op alle servers die worden beheerd door de host. | [Schakel Hyper-V-integratie Services](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) in op elke server.<br/><br/> Als u werkt met Windows Server 2003, [volgt u deze instructies](prepare-windows-server-2003-migration.md).
Referenties delegeren als server schijven zich op externe SMB-shares bevinden | Gemachtigden referenties | Voer deze opdracht uit om CredSSP in te scha kelen voor het delegeren van referenties op hosts waarop servers worden uitgevoerd met schijven op SMB-shares: ```powershell Enable-WSManCredSSP -Role Server -Force ```<br/><br/> U kunt deze opdracht op afstand uitvoeren op alle Hyper-V-hosts.<br/><br/> Als u nieuwe host knooppunten op een cluster toevoegt, worden deze automatisch toegevoegd voor detectie, maar moet u CredSSP hand matig inschakelen.<br/><br/> Wanneer u het apparaat instelt, voltooit u de instelling van CredSSP door [deze in te schakelen op het apparaat](#delegate-credentials-for-smb-vhds). 

### <a name="run-the-script"></a>Het script uitvoeren

1. Download het script via het [Microsoft Downloadcenter](https://aka.ms/migrate/script/hyperv). Het script is cryptografisch ondertekend door Microsoft.
2. Valideer de scriptintegriteit met behulp van MD5 of SHA256 hash-bestanden. De hashtagwaarden staan hieronder. Gebruik deze opdracht om de hash voor het script te genereren:

    ```powershell
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Gebruiksvoorbeeld:

    ```powershell
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```
3. Nadat u de script integriteit hebt gevalideerd, voert u het script uit op elke Hyper-V-host met deze Power shell-opdracht met verhoogde bevoegdheden:

    ```powershell
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```
Hashwaarden zijn:

**Hash** |  **Waarde**
--- | ---
MD5 | 0ef418f31915d01f896ac42a80dc414e
SHA256 | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2

## <a name="set-up-a-project"></a>Een project instellen

Stel een nieuw project in.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer onder **Overzicht** de optie **Project maken**.
5. Selecteer onder **Project maken** uw Azure-abonnement en resourcegroep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Vakken voor projectnaam en regio](./media/tutorial-discover-hyper-v/new-project.png)

7. Selecteer **Maken**.
8. Wacht een paar minuten totdat het project is geïmplementeerd. Het **Azure migrate: hulp programma voor detectie en evaluatie** wordt standaard toegevoegd aan het nieuwe project.

![Pagina met Azure Migrate: er is standaard een hulp programma voor detectie en evaluatie toegevoegd](./media/tutorial-discover-hyper-v/added-tool.png)

> [!NOTE]
> Als u al een project hebt gemaakt, kunt u hetzelfde project gebruiken om extra apparaten te registreren om meer servers te detecteren en te evalueren. [Meer informatie](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Het apparaat instellen

Azure Migrate maakt gebruik van een lichtgewicht Azure Migrate-apparaat. Het apparaat voert server detectie uit en verzendt meta gegevens van de server configuratie en prestaties naar Azure Migrate. Het apparaat kan worden ingesteld door een VHD-bestand te implementeren dat vanuit het project kan worden gedownload.

> [!NOTE]
> Als u het apparaat om de een of andere reden niet kunt instellen met behulp van de sjabloon, stelt u dit in met behulp van een Power shell-script op een bestaande Windows Server 2016-server. [Meer informatie](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).

In deze zelf studie wordt het apparaat ingesteld op een server die wordt uitgevoerd in de Hyper-V-omgeving, als volgt:

1. Geef een naam op voor het apparaat en Genereer een project sleutel in de portal.
1. Download een gecomprimeerde Hyper-V VHD vanuit de Azure Portal.
1. Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate: detectie en evaluatie.
1. Configureer het apparaat voor de eerste keer en registreer het met het project met behulp van de project sleutel.

### <a name="1-generate-the-project-key"></a>1. de project sleutel genereren

1. In **migratie doelen**  >  **servers**  >  **Azure migrate: detectie en evaluatie** selecteert u **detecteren**.
2. In **Discover-servers**  >  **zijn uw servers gevirtualiseerd?**, selecteert u **Ja, met Hyper-V**.
3. Geef in **1: project sleutel genereren** een naam op voor het Azure migrate apparaat dat u wilt instellen voor detectie van servers. De naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina Discover-server niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt er een **project sleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="2-download-the-vhd"></a>2. down load de VHD

In **2: Azure Migrate-apparaat downloaden**, selecteert u het VHD-bestand en klikt u op **Downloaden**.

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.

2. Gebruik de volgende PowerShell-opdracht om de hash voor het zip-bestand te genereren
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```

3.  Controleer de meest recente versie van het apparaat en de hashwaarden:

    - Voor de openbare Azure-cloud:

        **Scenario** | **Downloaden** | **SHA256**
        --- | --- | ---
        Hyper-V (8,91 GB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - Voor Azure Government:

        **Scenario** _ | _ *Downloaden** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="3-create-an-appliance"></a>3. een apparaat maken

Importeer het gedownloade bestand en maak een apparaat.

1. Pak het gecomprimeerde VHD-bestand uit in een map op de Hyper-V-host die als host fungeert voor het apparaat. Er worden drie mappen uitgepakt.
2. Open Hyper-V-beheer. Klik in **Acties** op **Virtuele machine importeren**.
2. Klik in de wizard Virtuele machine importeren > **Voordat u begint** op **Volgende**.
3. Geef onder **Map zoeken** de map op die de uitgepakte VHD bevat. Klik op **Volgende**.
1. Klik in **Virtuele machine selecteren** op **Volgende**.
2. Klik in **Importtype kiezen** op **De virtuele machine kopiëren (een nieuwe unieke id maken)** . Klik op **Volgende**.
3. Laat in **Bestemming kiezen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
4. Laat in **Opslagmappen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
5. Geef in **netwerk kiezen** de virtuele switch op die het apparaat gaat gebruiken. De switch heeft internetverbinding nodig om gegevens naar Azure te verzenden.
6. Controleer de instellingen in **Samenvatting**. Klik vervolgens op **Voltooien**.
7. In Hyper-V-beheer > **virtual machines** het apparaat starten.

### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="4-configure-the-appliance"></a>4. het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade VHD, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in Hyper-V-beheer > **virtual machines** met de rechter muisknop op het toestel > **verbinding te maken**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met het apparaat en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door te klikken op de snelkoppeling naar de app.
1. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Connectiviteit**: de app controleert of het apparaat toegang tot internet heeft. Als het apparaat gebruikmaakt van een proxy:
      - Klik op **proxy voor installatie** en geef het proxy adres op (in de vorm http://ProxyIPAddress of http://ProxyFQDN) en luister poort.
      - Geef referenties op als de proxy verificatie nodig heeft.
      - Alleen HTTP-proxy wordt ondersteund.
      - Als u proxydetails hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteitscontrole opnieuw te activeren.
    - **Tijdsynchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat serverdetectie goed werkt.
    - **Updates installeren**: Azure migrate: detectie en evaluatie controleert of de meest recente updates zijn geïnstalleerd op het apparaat. Als de controle is voltooid, kunt u op **Apparaatservices weergeven** klikken om de status en versies te zien van de onderdelen die op het apparaat worden uitgevoerd.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **project sleutel** die u hebt gekopieerd uit de portal. Als u de sleutel niet hebt, gaat u naar **Azure migrate: detectie en evaluatie> detecteren> bestaande apparaten te beheren**, selecteert u de naam van het apparaat dat u hebt ingevoerd op het moment van sleutel genereren en kopieert u de bijbehorende sleutel.
1. U hebt een apparaatcode nodig om te verifiëren bij Azure. Als u klikt op **Aanmelden**, wordt er een modaal met de apparaatcode geopend, zoals hieronder weergegeven.

    ![Modaal waarin de apparaatcode wordt weergegeven](./media/tutorial-discover-vmware/device-code.png)

1. Klik op **Code kopiëren en aanmelden** om de apparaatcode te kopiëren en een Azure-aanmeldingsprompt te openen op een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Plak op het tabblad Nieuw de code van het apparaat en meld u aan met behulp van uw Azure-gebruikers naam en-wacht woord.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Als u het aanmeldingstabblad per ongeluk sluit zonder u aan te melden, vernieuwt u het browsertabblad van Apparaatconfiguratiebeheer om de knop Aanmelden opnieuw in te schakelen.
1. Als u bent aangemeld, gaat u terug naar het vorige tabblad in Apparaatconfiguratiebeheer.
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste machtigingen  heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratiedetails zien door op **Details weergeven** te klikken.

### <a name="delegate-credentials-for-smb-vhds"></a>Referenties voor SMB-VHD's delegeren

Als u VHD's uitvoert op SMB's, moet u de delegatie van referenties van het apparaat naar de Hyper-V-hosts inschakelen. Als u dit wilt doen vanaf het apparaat:

1. Voer deze opdracht uit op het apparaat. HyperVHost1/HyperVHost2 zijn voorbeelden van hostnamen.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. U kunt dit ook doen in de editor voor Lokaal groepsbeleid op het apparaat:
    - Klik in **Lokaal computerbeleid** > **Computerconfiguratie** op **Administratieve sjablonen** > **Systeem** > **Delegatie van referenties**.
    - Dubbelklik op **Delegeren van nieuwe referenties toestaan** en selecteer **Ingeschakeld**.
    - Klik in **Opties** op **Weergeven** en voeg elke Hyper-V-host toe die u op de lijst wilt detecteren, waarbij u **wsman/** gebruikt als voorvoegsel.
    - Dubbelklik in **Delegatie van referenties** op **Delegeren van nieuwe referenties toestaan met NTLM-serververificatie**. Voeg weer elke Hyper-V-host toe die u op de lijst wilt detecteren, met **wsman/** als voorvoegsel.

## <a name="start-continuous-discovery"></a>Continue detectie starten

Verbinding maken vanaf het apparaat met Hyper-V-hosts of-clusters en server detectie starten.

1. In **stap 1: referenties opgeven voor hyper-v-host**, klikt u op **referenties toevoegen** om een beschrijvende naam op te geven voor referenties, **gebruikers naam** en **wacht woord** voor een Hyper-V-host/cluster toevoegen die het apparaat gaat gebruiken om servers te detecteren. Klik op **Opslaan**.
1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **Meer toevoegen** om meer referenties op te slaan en toe te voegen. Meerdere referenties worden ondersteund voor de detectie van servers in de Hyper-V-omgeving.
1. In **Stap 2: Geef de gegevens voor de Hyper-V-host of het Hyper-V-cluster op**, klik op **Detectiebron toevoegen** om het **IP-adres of de FQDN** van de Hyper-V-host of het Hyper-V-cluster op te geven en tevens de beschrijvende naam voor de referenties waarmee verbinding wordt gemaakt met de host of het cluster.
1. U kunt **één item per keer toevoegen** of **meerdere items in één keer toevoegen**. Er is ook een optie om de gegevens van een Hyper-V-host/-cluster op te geven via **CSV importeren**.

    - Als u kiest voor **Eén item toevoegen**, moet u een beschrijvende naam opgeven voor referenties en tevens het **IP-adres of de FQDN** van de Hyper-V-host of het -cluster. Klik vervolgens op **Opslaan**.
    - Als u **meerdere items toevoegen** _(standaard geselecteerd)_ hebt gekozen, kunt u meerdere records tegelijk toevoegen door een **IP-adres/FQDN** voor de Hyper-V-host/het cluster op te geven met de beschrijvende naam voor de referenties in het tekstvak. **Controleer** de toegevoegde records en klik op **Opslaan**.
    - Als u **CSV importeren** kiest, kunt u een CSV-sjabloonbestand downloaden, het bestand vullen met het **IP-adres of de FQDN** van de Hyper-V-host of het -cluster, en een beschrijvende naam voor referenties. Vervolgens importeert u het bestand in het apparaat, **controleert u** de records in het bestand en klikt u op **Opslaan**.

1. Wanneer u op Opslaan klikt, wordt de verbinding met de toegevoegde Hyper-V-hosts/-clusters gevalideerd en wordt de **validatiestatus** in de tabel voor elke host of elk cluster weergegeven.
    - Voor gevalideerde hosts/clusters kunt u meer details weergeven door op het IP-adres of de FQDN te klikken.
    - Als de validatie voor een host mislukt, bekijkt u de fout door in de kolom Status van de tabel op **Validatie mislukt** te klikken. Los het probleem op en valideer opnieuw.
    - Als u hosts of clusters wilt verwijderen, klikt u op **Verwijderen**.
    - U kunt een specifieke host niet verwijderen uit een cluster. U kunt alleen het hele cluster verwijderen.
    - U kunt een cluster toevoegen, zelfs als er problemen zijn met specifieke hosts in het cluster.
1. U kunt de connectiviteit van hosts/clusters op elk gewenst moment opnieuw **valideren** voordat u de detectie start.
1. Klik op **detectie starten** om de server detectie van de gevalideerde hosts/clusters te laten slagen. Nadat de detectie is gestart, kunt u de detectiestatus controleren voor elke host of elk cluster in de tabel.

De detectie wordt gestart. Het duurt ongeveer twee minuten per host voordat de metagegevens van de gedetecteerde servers worden weergegeven in de Azure-portal.

## <a name="verify-servers-in-the-portal"></a>Verifieer servers in de portal

Nadat de detectie is voltooid, kunt u controleren of de servers worden weergegeven in de portal.

1. Open het Azure Migrate-dashboard.
2. Klik in **Azure migrate servers**  >  **Azure migrate: pagina detectie en evaluatie** op het pictogram met het aantal voor **gedetecteerde servers**.

## <a name="next-steps"></a>Volgende stappen

- [Servers op de Hyper-V-omgeving beoordelen](tutorial-assess-hyper-v.md) voor migratie naar virtuele Azure-machines.
- [De gegevens controleren ](migrate-appliance.md#collected-data---hyper-v) die door het apparaat worden verzameld tijdens de detectie.
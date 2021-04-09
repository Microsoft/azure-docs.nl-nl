---
title: Servers die worden uitgevoerd in de VMware-omgeving detecteren met Azure Migrate detectie en evaluatie
description: Meer informatie over het detecteren van on-premises servers in VMware-omgevingen met het hulp programma voor detectie en evaluatie van Azure Migrate
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/25/2021
ms.custom: mvc
ms.openlocfilehash: 09b04c67519bfa920a3781612823c5755cbc6d2d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627792"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-discovery-and-assessment"></a>Zelf studie: servers die worden uitgevoerd in de VMware-omgeving detecteren met Azure Migrate: detectie en evaluatie

Als onderdeel van de migratie naar Azure, detecteert u uw on-premises inventaris en werkbelastingen.

In deze zelf studie leert u hoe u servers detecteert die worden uitgevoerd in de VMware-omgeving met Azure Migrate: hulp programma voor detectie en evaluatie, met behulp van een licht gewicht Azure Migrate apparaat. U implementeert het apparaat als een server die wordt uitgevoerd in uw vCenter Server, om voortdurend servers en hun prestatie-meta gegevens te detecteren, toepassingen die worden uitgevoerd op servers, Server afhankelijkheden en SQL Server instanties en data bases.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure-account instellen.
> * De VMware-omgeving voorbereiden voor detectie.
> * Een project maken.
> * Het Azure Migrate-apparaat instellen.
> * Continue detectie starten.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken waar mogelijk de standaardopties.  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Controleer of deze vereisten aanwezig zijn voordat u met deze zelfstudie begint.

**Vereiste** | **Details**
--- | ---
**vCenter Server/ESXi host** | U hebt een vCenter-server met versie 5.5, 6.0, 6.5 of 6.7 nodig.<br/><br/> Servers moeten worden gehost op een ESXi-host waarop versie 5,5 of hoger wordt uitgevoerd.<br/><br/> Op de vCenter Server, binnenkomende verbindingen op TCP-poort 443 toestaan, zodat het apparaat de meta gegevens van de configuratie en prestaties kan verzamelen.<br/><br/> Het apparaat maakt standaard verbinding met vCenter Server op poort 443. Als de vCenter Server op een andere poort luistert, kunt u de poort wijzigen wanneer u de vCenter Server Details opgeeft in het configuratie beheer van het apparaat.<br/><br/> Controleer op de ESXi-hosts of binnenkomende toegang is toegestaan op TCP-poort 443 om geïnstalleerde toepassingen en afhankelijkheids analyse zonder agent op servers uit te voeren.
**Apparaat** | vCenter Server hebt resources nodig om een server toe te wijzen voor het Azure Migrate apparaat:<br/><br/> -32 GB aan RAM, 8 Vcpu's en ongeveer 80 GB aan schijf opslag.<br/><br/> -Een externe virtuele switch en Internet toegang op de toestel server, rechtstreeks of via een proxy.
**Servers** | Alle versies van het Windows-en Linux-besturings systeem worden ondersteund voor detectie van meta gegevens van de configuratie en prestaties. <br/><br/> Voor het uitvoeren van toepassings detectie op servers worden alle versies van Windows-en Linux-besturings systemen ondersteund. [Hier](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) kunt u controleren welke versies van het besturings systeem worden ondersteund voor afhankelijkheids analyse zonder agent.<br/><br/> Voor de detectie van geïnstalleerde toepassingen en afhankelijkheids analyse zonder agent moeten VMware-Hulpprogram Ma's (later dan 10.2.0) op servers worden geïnstalleerd en worden uitgevoerd. Op Windows-servers moet Power shell-versie 2,0 of hoger zijn geïnstalleerd.<br/><br/> Als u SQL Server instanties en data bases wilt detecteren, kunt u [hier](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases) een van de ondersteunde versies en edities SQL Server van het Windows-besturings systeem en de ondersteunde verificatie mechanismen controleren.

## <a name="prepare-an-azure-user-account"></a>Een Azure-gebruikersaccount voorbereiden

Als u een project wilt maken en het Azure Migrate apparaat wilt registreren, hebt u een account nodig met:

- Inzender of eigenaars machtigingen voor het Azure-abonnement
- Machtigingen voor het registreren van Azure Active Directory-apps (AAD)
- Eigenaar of Inzender plus beheerders machtigingen voor gebruikers toegang voor het Azure-abonnement om een Key Vault te maken, die wordt gebruikt tijdens server migratie zonder agents

Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement. Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de volgende machtigingen toe te wijzen:

1. Zoek in de Azure Portal naar 'Abonnementen' en selecteer onder **Services** **Abonnementen**.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Zoekvak om te zoeken naar het Azure-abonnement":::


2. Selecteer op de pagina **abonnementen** het abonnement waarin u een project wilt maken.
3. Selecteer onder het abonnement de optie **Toegangsbeheer (IAM)**  > **Toegang controleren**.
4. Zoek onder **Toegang controleren** naar het relevante gebruikersaccount.
5. Klik onder **Een roltoewijzing toevoegen** op **Toevoegen**.
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Een gebruikersaccount zoeken om toegang te controleren en een rol toe te wijzen":::
    
6. Selecteer onder **Roltoewijzing toevoegen** de rol Inzender of Eigenaar en selecteer account (azmigrateuser in ons voorbeeld). Klik vervolgens op **Opslaan**.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Hiermee opent u de pagina Roltoewijzing toevoegen om een rol aan het account toe te wijzen":::

1. Als u het apparaat wilt registreren, heeft uw Azure-account **machtigingen nodig voor het registreren van Aad-apps.**
1. Ga in azure Portal naar **Azure Active Directory**  >  **gebruikers**  >  **gebruikers instellingen**.
1. Controleer onder **Gebruikersinstellingen** of Azure AD-gebruikers toepassingen kunnen registreren (standaard ingesteld op **Ja**).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Verifiëren onder Gebruikersinstellingen of gebruikers Active Directory-apps kunnen registreren":::

9. Als de App-registraties-instellingen is ingesteld op Nee, vraagt u de Tenant/globale beheerder om de vereiste machtiging toe te wijzen. De Tenant/globale beheerder kan de rol van **toepassings ontwikkelaar** ook toewijzen aan een account om de registratie van de Aad-app toe te staan. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-vmware"></a>VMware voorbereiden

Controleer op vCenter Server of uw account over de machtigingen beschikt om een virtuele machine te maken met behulp van een OVA-bestand. Dit is nodig wanneer u met behulp van een OVA-bestand het Azure Migrate-apparaat als een VMware-VM implementeert.

Azure Migrate heeft een vCenter Server alleen-lezen-account nodig voor detectie en evaluatie van servers die in uw VMware-omgeving worden uitgevoerd. Als u ook de detectie van geïnstalleerde toepassingen en afhankelijkheids analyse zonder agent wilt uitvoeren, heeft het account bevoegdheden nodig die zijn ingeschakeld voor **Virtual Machines > gast bewerkingen**.

### <a name="create-an-account-to-access-vcenter"></a>Een account maken voor toegang tot vCenter

Stel in de vSphere-webclient als volgt een account in:

1. Gebruik een account met beheerdersbevoegdheden in de vSphere-webclient en selecteer **Beheer**.
2. **Toegang**, selecteer **SSO-gebruikers en -groepen**.
3. Voeg onder **Gebruiker** een nieuwe gebruiker toe.
4. Typ onder **Nieuwe gebruiker** de accountgegevens. Klik vervolgens op **OK**.
5. Selecteer onder **Globale machtigingen** het gebruikersaccount en wijs de rol **Alleen-lezen** toe aan het account. Klik vervolgens op **OK**.
6. Als u ook de detectie van geïnstalleerde toepassingen en afhankelijkheids analyse zonder agent wilt uitvoeren, gaat u naar **rollen** > selecteert u de rol **alleen-lezen** en selecteert u in **bevoegdheden** **gast bewerkingen**. U kunt de bevoegdheden door geven aan alle objecten onder de vCenter Server door de selectie vakje ' door geven aan onderliggende items ' in te scha kelen.

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Selectievakje om gastbewerkingen toe te staan voor de rol Alleen-lezen":::

> [!NOTE]
> U kunt de detectie beperken tot specifieke vCenter Server Data Centers, clusters, een map met clusters, hosts, een map van hosts of afzonderlijke servers door de vCenter Server-account te bereiknen. Meer [**informatie**](set-discovery-scope.md) over het bereik van de vCenter Server-gebruikers account.

### <a name="create-an-account-to-access-servers"></a>Een account maken voor toegang tot servers

U hebt een gebruikers account met de vereiste bevoegdheden op de servers nodig om de detectie van geïnstalleerde toepassingen, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases te kunnen uitvoeren. U kunt het gebruikers account opgeven op de configuratie beheer van het apparaat. Op het apparaat worden geen agents op de servers geïnstalleerd.

1. Maak voor Windows-servers een account (lokaal of domein) met beheerders machtigingen op de servers. Als u SQL Server instanties en data bases wilt detecteren, moet u het Windows-of SQL Server-account lid zijn van de serverrol sysadmin. Meer [informatie](/sql/relational-databases/security/authentication-access/server-level-roles) over het toewijzen van de vereiste rol aan het gebruikers account.
2. Maak voor Linux-servers een account met hoofd bevoegdheden. U kunt ook een account met de volgende machtigingen maken voor/bin/netstat-en/bin/ls-bestanden: CAP_DAC_READ_SEARCH en CAP_SYS_PTRACE.

> [!NOTE]
> U kunt nu meerdere Server referenties toevoegen aan Configuration Manager voor het uitvoeren van detectie van geïnstalleerde toepassingen, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases. U kunt meerdere domein/Windows-(niet-domein)/Linux-en/of SQL Server verificatie referenties toevoegen. [**Meer informatie**](add-server-credentials.md)

## <a name="set-up-a-project"></a>Een project instellen

Stel een nieuw project in.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer **in overzicht** > op basis van uw migratie doelen: **Windows, Linux en SQL Server** of **SQL Server (alleen)** of **Verken meer scenario's** > Selecteer **project maken**.
5. Selecteer onder **Project maken** uw Azure-abonnement en resourcegroep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Vakken voor projectnaam en regio":::

7. Selecteer **Maken**.
8. Wacht een paar minuten totdat het project is geïmplementeerd. Het **Azure migrate: hulp programma voor detectie en evaluatie** wordt standaard toegevoegd aan het nieuwe project.

> [!NOTE]
> Als u al een project hebt gemaakt, kunt u hetzelfde project gebruiken om extra apparaten te registreren om meer nee te ontdekken en te evalueren. van servers. [ **Meer informatie**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Het apparaat instellen

Azure Migrate: detectie en evaluatie gebruiken een licht gewicht Azure Migrate apparaat. Het apparaat voert server detectie uit en verzendt meta gegevens van de server configuratie en prestaties naar Azure Migrate. Het apparaat kan worden ingesteld door een eicellen-sjabloon te implementeren die vanuit het project kan worden gedownload.

> [!NOTE]
> Als u het apparaat om de een of andere reden niet kunt instellen met behulp van de sjabloon, stelt u dit in met behulp van een Power shell-script op een bestaande Windows Server 2016-server. [**Meer informatie**](deploy-appliance-script.md#set-up-the-appliance-for-vmware).

### <a name="deploy-with-ova"></a>Implementeren met OVA

Als u het apparaat wilt instellen met behulp van een OVA-sjabloon, doet u het volgende:

1. Geef een naam op voor het apparaat en Genereer een project sleutel in de portal.
1. Download een OVA-sjabloonbestand en importeer het naar vCenter Server. Controleer of de eicellen veilig zijn.
1. Maak het apparaat vanuit het bestand van de eicellen en controleer of het verbinding kan maken met Azure Migrate.
1. Configureer het apparaat voor de eerste keer en registreer het met het project met behulp van de project sleutel.

### <a name="1-generate-the-project-key"></a>1. de project sleutel genereren

1. In **migratie doelen**  >  **Windows, Linux-en SQL-servers**  >  **Azure migrate: detectie en evaluatie**, selecteer **detecteren**.
2. In **Discover-servers**  >  **zijn uw servers gevirtualiseerd?**, selecteert u **Ja, met VMware vSphere Hyper Visor**.
3. Geef in **1: project sleutel genereren** een naam op voor het Azure migrate apparaat dat u wilt instellen voor de detectie van servers in uw VMware-omgeving. De naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de detectie pagina niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt er een **project sleutel** gegenereerd.
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

    - Voor Azure Government:
    
        **Algoritme** | **Downloaden** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

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
3. Open een browser op een computer die verbinding kan maken met het apparaat en open de URL van het configuratie beheer van het apparaat: `https://appliance name or IP address: 44368` .

   U kunt de Configuration Manager ook openen via het bureau blad van de toestel server door de snelkoppeling voor Configuration Manager te selecteren.
1. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
1. Ga als volgt te werk in de Configuration Manager-> vereisten in te **stellen**:
   - **Connectiviteit**: het apparaat controleert of de server toegang heeft tot internet. Als de server gebruikmaakt van een proxy:
     - Klik op **proxy voor installatie** om het proxy adres `http://ProxyIPAddress` of `http://ProxyFQDN` en luister poort op te geven.
     - Geef referenties op als de proxy verificatie nodig heeft.
     - Alleen HTTP-proxy wordt ondersteund.
     - Als u proxydetails hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteitscontrole opnieuw te activeren.
   - **Tijdsynchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat detectie goed werkt.
   - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates zijn geïnstalleerd. Nadat de controle is voltooid, kunt u klikken op **apparaten weer geven** om de status en versies van de services die op de toestel server worden uitgevoerd te bekijken.
   - **VDDK installeren**: het apparaat controleert of VMware vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als dat niet het geval is, downloadt u VDDK 6.7 van VMware en extraheert u de inhoud van het gedownloade zipbestand naar de opgegeven locatie op het apparaat. Zie voor meer informatie de **installatie-instructies**.

     Azure Migrate server migratie gebruikt de VDDK om servers te repliceren tijdens de migratie naar Azure. 
1. Als u wilt, kunt u **de vereisten op elk gewenst moment opnieuw uitvoeren** tijdens de configuratie van het apparaat om te controleren of het apparaat nog steeds voldoet aan alle vereisten.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Paneel 1 op het configuratie beheer van het apparaat":::

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **project sleutel** die u hebt gekopieerd uit de portal. Als u de sleutel niet hebt, gaat u naar **Azure migrate: detectie en evaluatie> detecteren> bestaande apparaten te beheren**, selecteert u de naam van het apparaat dat u hebt ingevoerd op het moment van sleutel genereren en kopieert u de bijbehorende sleutel.
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

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Panel 3 op het apparaat Configuration Manager voor vCenter Server Details":::

### <a name="provide-server-credentials"></a>Server referenties opgeven

In **stap 3: Server referenties opgeven voor het uitvoeren van software-inventaris, afhankelijkheids analyse zonder agent en detectie van SQL Server instanties en data bases**. u kunt ervoor kiezen om meerdere Server referenties op te geven of als u deze functies niet wilt gebruiken, kunt u de stap overs Laan en door gaan met vCenter Server detectie. U kunt uw intentie op elk later tijdstip wijzigen.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Panel 3 op het apparaat Configuration Manager voor Server Details":::

Als u deze functies wilt gebruiken, kunt u de referenties van de server opgeven door de volgende stappen uit te voeren. Het apparaat zal proberen de referenties automatisch toe te wijzen aan de servers om de detectie functies uit te voeren.

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

> [!Note]
>Azure Migrate versleutelt de communicatie tussen Azure Migrate apparaat en bron SQL Server instanties (waarbij de eigenschap versleutelings verbinding is ingesteld op TRUE). Deze verbindingen worden versleuteld met [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (ingesteld op True). de transportlaag gebruikt SSL om het kanaal te versleutelen en de certificaat keten te omzeilen om de vertrouwens relatie te valideren. De toestel server moet zijn ingesteld om [**de basis instantie van het certificaat te vertrouwen**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Als er geen certificaat is ingericht op de server wanneer het wordt gestart, SQL Server genereert een zelfondertekend certificaat dat wordt gebruikt om aanmeldings pakketten te versleutelen. [**Meer informatie**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Detectie werkt als volgt:

- Het duurt ongeveer 15 minuten voordat de gedetecteerde servers inventarisatie wordt weer gegeven in de portal.
- De detectie van geïnstalleerde toepassingen kan enige tijd duren. De duur is afhankelijk van het aantal gedetecteerde servers. Voor 500-servers duurt het ongeveer een uur voordat de gedetecteerde inventaris wordt weer gegeven in de Azure Migrate Portal.
- Nadat de detectie van servers is voltooid, kunt u een afhankelijkheids analyse zonder agent inschakelen op de servers van de portal.
- SQL Server instanties en data base-gegevens worden binnen 24 uur weer gegeven in de portal vanaf de detectie-start.

## <a name="next-steps"></a>Volgende stappen

- [Servers beoordelen](./tutorial-assess-vmware-azure-vm.md) voor migratie naar virtuele Azure-machines.
- [SQL-servers beoordelen](./tutorial-assess-sql.md) voor migratie naar Azure SQL.
- [De gegevens controleren ](migrate-appliance.md#collected-data---vmware) die door het apparaat worden verzameld tijdens de detectie.

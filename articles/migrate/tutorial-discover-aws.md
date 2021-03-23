---
title: AWS-instanties detecteren met Azure Migrate detectie en evaluatie
description: Meer informatie over het detecteren van AWS-instanties met Azure Migrate detectie en evaluatie.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 295cd5a6831cb64d146bb92bca74b82ff7ab29df
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771478"
---
# <a name="tutorial-discover-aws-instances-with-azure-migrate-discovery-and-assessment"></a>Zelf studie: AWS-instanties detecteren met Azure Migrate: detectie en evaluatie

Als onderdeel van uw migratietraject naar Azure, detecteert u uw servers voor evaluatie en migratie.

In deze zelf studie ziet u hoe u Amazon Web Services-exemplaren (AWS) detecteert met Azure Migrate het hulp programma voor detectie en evaluatie met behulp van een licht gewicht Azure Migrate apparaat. U implementeert het apparaat als een fysieke server, zodat de metagegevens van de computer en de prestaties continu worden gedetecteerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure-account instellen.
> * AWS-instanties voorbereiden voor detectie.
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
**Apparaat** | U hebt een virtuele EC2-machine nodig waarop het Azure Migrate-apparaat kan worden uitgevoerd. De machine moet aan de volgende voorwaarden voldoen:<br/><br/> -Windows Server 2016 is geïnstalleerd.<br/> _Het apparaat wordt niet ondersteund op een computer met Windows Server 2019_.<br/><br/> - 16 GB RAM, 8 vCPU's, ongeveer 80 GB opslagruimte en een externe virtuele switch.<br/><br/> - Een statisch of dynamisch IP-adres met internettoegang, hetzij rechtstreeks of via een proxy.
**Windows-exemplaren** | Sta binnenkomende verbindingen op WinRM-poort 5985 (HTTP) toe, zodat het apparaat metagegevens over de configuratie en prestaties kan ophalen.
**Linux-exemplaren** | Sta binnenkomende verbindingen op poort 22 (TCP) toe.<br/><br/> De exemplaren moeten `bash` gebruiken als de standaardshell, anders mislukt de detectie.

## <a name="prepare-an-azure-user-account"></a>Een Azure-gebruikersaccount voorbereiden

Als u een project wilt maken en het Azure Migrate apparaat wilt registreren, hebt u een account nodig met:

* Machtigingen op inzender- of eigenaarniveau voor een Azure-abonnement.
* Machtigingen voor het registreren van Azure Active Directory-apps (AAD).

Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement. Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de volgende machtigingen toe te wijzen:

1. Zoek in de Azure Portal naar 'Abonnementen' en selecteer onder **Services** **Abonnementen**.

    ![Zoekvak om te zoeken naar het Azure-abonnement](./media/tutorial-discover-aws/search-subscription.png)

2. Selecteer op de pagina **abonnementen** het abonnement waarin u een project wilt maken.
3. Selecteer onder het abonnement de optie **Toegangsbeheer (IAM)**  > **Toegang controleren**.
4. Zoek onder **Toegang controleren** naar het relevante gebruikersaccount.
5. Klik onder **Een roltoewijzing toevoegen** op **Toevoegen**.

    ![Een gebruikersaccount zoeken om toegang te controleren en een rol toe te wijzen](./media/tutorial-discover-aws/azure-account-access.png)

6. Selecteer onder **Roltoewijzing toevoegen** de rol Inzender of Eigenaar en selecteer account (azmigrateuser in ons voorbeeld). Klik vervolgens op **Opslaan**.

    ![Hiermee opent u de pagina Roltoewijzing toevoegen om een rol aan het account toe te wijzen](./media/tutorial-discover-aws/assign-role.png)

1. Als u het apparaat wilt registreren, heeft uw Azure-account **machtigingen nodig voor het registreren van Aad-apps.**
1. Ga in azure Portal naar **Azure Active Directory**  >  **gebruikers**  >  **gebruikers instellingen**.
1. Controleer onder **Gebruikersinstellingen** of Azure AD-gebruikers toepassingen kunnen registreren (standaard ingesteld op **Ja**).

    ![Verifiëren onder Gebruikersinstellingen of gebruikers Active Directory-apps kunnen registreren](./media/tutorial-discover-aws/register-apps.png)

1. Als de App-registraties-instellingen is ingesteld op Nee, vraagt u de Tenant/globale beheerder om de vereiste machtiging toe te wijzen. De Tenant/globale beheerder kan de rol van **toepassings ontwikkelaar** ook toewijzen aan een account om de registratie van de Aad-app toe te staan. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-aws-instances"></a>AWS-instanties voorbereiden

Stel een account in dat het apparaat kan gebruiken om toegang te krijgen tot AWS-exemplaren.

- Voor **Windows-servers** stelt u een lokaal gebruikers account in op alle Windows-servers die u wilt toevoegen in de detectie. Voeg het gebruikersaccount toe aan de volgende groepen: - Gebruikers voor extern beheer - Prestatiemetergebruikers - Prestatielogboekgebruikers.
 - Voor **Linux-servers** hebt u een hoofd account nodig op de Linux-servers die u wilt detecteren. Raadpleeg de instructies in de [ondersteunings matrix](migrate-support-matrix-physical.md#physical-server-requirements) voor een alternatief.
- Azure Migrate gebruikt wachtwoordverificatie wanneer u AWS-instanties detecteert. AWS-instanties ondersteunen niet standaard wachtwoordverificatie. Voordat u een instantie kunt detecteren, moet u wachtwoordverificatie inschakelen.
    - Voor Windows-servers, allow WinRM-poort 5985 (HTTP). Zo kunt u externe WMI aanroepen.
    - Voor Linux-servers:
        1. Aanmelden bij elke Linux-machine.
        2. Open het bestand sshd_config: vi /etc/ssh/sshd_config
        3. Ga in het bestand naar de regel **PasswordAuthentication** en wijzig de waarde in **yes**.
        4. Sla het bestand op en sluit het. Start de ssh-service opnieuw.
    - Als u een hoofd gebruiker gebruikt voor het detecteren van uw Linux-servers, moet u ervoor zorgen dat basis aanmelding is toegestaan op de-servers.
        1. Aanmelden bij elke Linux-machine
        2. Open het bestand sshd_config: vi /etc/ssh/sshd_config
        3. Ga in het bestand naar de regel **PermitRootLogin** en wijzig de waarde in **yes**.
        4. Sla het bestand op en sluit het. Start de ssh-service opnieuw.

## <a name="set-up-a-project"></a>Een project instellen

Stel een nieuw project in.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer onder **Overzicht** de optie **Project maken**.
5. Selecteer onder **Project maken** uw Azure-abonnement en resourcegroep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Vakken voor projectnaam en regio](./media/tutorial-discover-aws/new-project.png)

7. Selecteer **Maken**.
8. Wacht een paar minuten totdat het project is geïmplementeerd. Het **Azure migrate: hulp programma voor detectie en evaluatie** wordt standaard toegevoegd aan het nieuwe project.

![Pagina waarop wordt weergegeven dat het hulpprogramma Serverevaluatie standaard wordt toegevoegd](./media/tutorial-discover-aws/added-tool.png)

> [!NOTE]
> Als u al een project hebt gemaakt, kunt u hetzelfde project gebruiken om extra apparaten te registreren om meer servers te detecteren en te evalueren. [Meer informatie](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>Het apparaat instellen

Het Azure Migrate apparaat is een licht gewicht apparaat, dat wordt gebruikt door Azure Migrate: detectie en evaluatie om het volgende te doen:

- On-premises servers detecteren.
- Meta gegevens en prestatie gegevens voor gedetecteerde servers verzenden naar Azure Migrate: detectie en evaluatie.

[Meer informatie](migrate-appliance.md) over het Azure Migrate-apparaat.

Om het apparaat in te stellen, moet u het volgende doen:

1. Geef een naam op voor het apparaat en Genereer een project sleutel in de portal.
1. Download een zip-bestand met Azure Migrate-installatiescript uit de Azure-portal.
1. Pak de inhoud uit het zip-bestand uit. Start PowerShell-console met beheerdersbevoegdheden.
1. Voer het PowerShell-script uit om de webtoepassing voor het apparaat te starten.
1. Configureer het apparaat voor de eerste keer en registreer het met het project met behulp van de project sleutel.

### <a name="1-generate-the-project-key"></a>1. de project sleutel genereren

1. In **migratie doelen**  >  **Windows, Linux-en SQL-servers**  >  **Azure migrate: detectie en evaluatie**, selecteer **detecteren**.
2. In **Discover-servers**  >  **zijn uw servers gevirtualiseerd?** selecteert u **fysiek of ander (AWS, GCP, xen, enzovoort)**.
3. Geef in **1: project sleutel genereren** een naam op voor het Azure migrate apparaat dat u wilt instellen voor de detectie van fysieke of virtuele servers. De naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina servers detecteren niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt er een **project sleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="2-download-the-installer-script"></a>2. het installatie script downloaden

In **2: Download Azure Migrate-apparaat**, klik op **Downloaden**.

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Gebruik de volgende opdracht om de hash voor het zip-bestand te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld van gebruik voor openbare cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Voorbeeld van gebruik voor overheidscloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Controleer de meest recente versie van het apparaat en de hashwaarden:
    - Voor de openbare cloud:

        **Scenario** | **Downloaden** _ | _ *Hash-waarde**
        --- | --- | ---
        Fysiek (85 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140334) | 207157bab39303dca1c2b93562d6f1deaa05aa7c992f480138e17977641163fb

    - Voor Azure Government:

        **Scenario** | **Downloaden** _ | _ *Hash-waarde**
        --- | --- | ---
        Fysiek (85 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140338) | ca67e8dbe21d113ca93bfe94c1003ab7faba50472cb03972d642be8a466f78ce
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Voer het Azure Migrate-installatie script uit
Het installatiescript doet het volgende:

- Installeert agents en een webtoepassing voor detectie en evaluatie van fysieke servers.
- Installeer Windows-rollen, waaronder Windows-activeringsservice, IIS en PowerShell ISE.
- Een herschrijfbare module van IIS downloaden en installeren. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een registersleutel (HKLM) bij met permanente instellingsgegevens voor Azure Migrate.
- Maakt de volgende bestanden onder het pad:
    - **Configuratiebestanden**: %Programdata%\Microsoft Azure\Config
    - **Logboekbestanden**: %Programdata%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat.  Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start PowerShell op de bovenstaande server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map waarin de inhoud is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer de volgende opdracht uit om het script uit te voeren met de naam **AzureMigrateInstaller.ps1**:

    - Voor de openbare cloud: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Voor Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Met het script wordt de webtoepassing voor het apparaat gestart wanneer deze succesvol is voltooid.

Als u problemen ondervindt, kunt u het script Logboeken openen op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log voor probleemoplossing.

### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="4-configure-the-appliance"></a>4. het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Open een browser op een computer die verbinding kan maken met het apparaat en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad door te klikken op de snelkoppeling naar de app.
2. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Connectiviteit**: De app controleert of de server toegang heeft tot internet. Als de server gebruikmaakt van een proxy:
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
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste [machtigingen ](./tutorial-discover-physical.md) heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratiedetails zien door op **Details weergeven** te klikken.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak nu verbinding vanaf het apparaat met de fysieke servers die moeten worden gedetecteerd en start de detectie.

1. In **Stap 1: Geef referenties op voor de detectie van fysieke of virtuele Windows- en Linux-servers**, en klik op **Referenties toevoegen**.
1. Voor Windows Server selecteert u het bron type als **Windows-Server**, geeft u een beschrijvende naam op voor referenties, voegt u de gebruikers naam en het wacht woord toe. Klik op **Opslaan**.
1. Als u verificatie op basis van wacht woorden voor Linux-server gebruikt, selecteert u het bron type als **Linux-server (op wacht woord gebaseerd)**, geeft u een beschrijvende naam op voor referenties, voegt u de gebruikers naam en het wacht woord toe. Klik op **Opslaan**.
1. Als u gebruikmaakt van verificatie op basis van SSH-sleutels voor Linux-server, kunt u bron type selecteren als **Linux-server (SSH-sleutel gebaseerd)**, een beschrijvende naam voor referenties opgeven, de gebruikers naam toevoegen, bladeren en het bestand met de persoonlijke SSH-sleutel selecteren. Klik op **Opslaan**.

    * Azure Migrate ondersteunt de persoonlijke SSH-sleutel die wordt gegenereerd door de opdracht ssh-keygen met behulp van RSA-, DSA-, ECDSA-en ed25519-algoritmen.
    * Momenteel wordt Azure Migrate geen SSH-sleutel op basis van een wachtwoordzin ondersteund. Gebruik een SSH-sleutel zonder wachtwoordzin.
    * Azure Migrate biedt momenteel geen ondersteuning voor persoonlijke SSH-sleutelbestanden gegenereerd met PuTTY.
    * Azure Migrate biedt ondersteuning voor de OpenSSH-indeling van het persoonlijke SSH-sleutelbestand, zoals hieronder weergegeven:
    
    ![Ondersteunde indeling voor persoonlijke SSH-sleutels](./media/tutorial-discover-physical/key-format.png)


1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **Meer toevoegen** om meer referenties op te slaan en toe te voegen. Er worden meerdere referenties ondersteund voor detectie van fysieke servers.
1. In **Stap 2: Geef de gegevens voor de fysieke of virtuele server op**, klik op **Detectiebron toevoegen** om het **IP-adres of de FQDN** van de server op te geven en tevens de beschrijvende naam voor de referenties waarmee verbinding wordt gemaakt met de server.
1. U kunt **één item per keer toevoegen** of **meerdere items in één keer toevoegen**. Er is ook een optie om de gegevens van een server op te geven via **CSV importeren**.


    - Als u kiest voor **Eén item toevoegen**, kunt u het type besturingssysteem kiezen, een beschrijvende naam opgeven voor referenties en het **IP-adres of de FQDN** van de server toevoegen. Klik vervolgens op **Opslaan**.
    - Als u **meerdere items toevoegen** hebt gekozen, kunt u meerdere records tegelijk toevoegen door het **IP-adres/de FQDN** van de server op te geven met de beschrijvende naam voor de referenties in het tekstvak. Verifieer * * de toegevoegde records en klik op **Opslaan**.
    - Als u **CSV importeren** _(standaard geselecteerd)_ kiest, kunt u een CSV-sjabloonbestand downloaden, het bestand vullen met het **IP-adres of de FQDN** van de server en een beschrijvende naam voor referenties. Vervolgens importeert u het bestand in het apparaat, **controleert u** de records in het bestand en klikt u op **Opslaan**.

1. Wanneer u op Opslaan klikt, wordt de verbinding met de toegevoegde servers gevalideerd en wordt de **validatiestatus** in de tabel voor elke server weergegeven.
    - Als de validatie voor een server mislukt, bekijkt u de fout door in de kolom Status van de tabel op **Validatie mislukt** te klikken. Los het probleem op en valideer opnieuw.
    - Als u een server wilt verwijderen, klikt u op **Verwijderen**.
1. U kunt de connectiviteit met servers op elk gewenst moment opnieuw **valideren** voordat u de detectie start.
1. Klik op **Detectie starten** om met detectie van de gevalideerde servers te beginnen. Nadat de detectie is gestart, kunt u de detectiestatus controleren voor elke server in de tabel.


De detectie wordt gestart. Het duurt ongeveer 2 minuten per server voordat de metagegevens van de gedetecteerde server worden weergegeven in de Azure-portal.

## <a name="verify-servers-in-the-portal"></a>Verifieer servers in de portal

Nadat de detectie is voltooid, kunt u controleren of de servers worden weergegeven in de portal.

1. Open het Azure Migrate-dashboard.
2. Klik in **Azure migrate-Windows-, Linux-en SQL-servers**  >  **Azure migrate: de pagina detectie en evaluatie** op het pictogram dat het aantal voor **gedetecteerde servers** weergeeft.

## <a name="next-steps"></a>Volgende stappen

- [Fysieke servers beoordelen](tutorial-migrate-aws-virtual-machines.md) voor migratie naar virtuele Azure-machines.
- [De gegevens controleren ](migrate-appliance.md#collected-data---physical) die door het apparaat worden verzameld tijdens de detectie.

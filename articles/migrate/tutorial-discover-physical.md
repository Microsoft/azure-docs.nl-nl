---
title: Fysieke servers detecteren met Azure Migrate-serverevaluatie
description: Meer informatie over het detecteren van on-premises fysieke servers met Azure Migrate-serverevaluatie.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 2da5577b1b82c5374f33b732a0af54bac5ebae58
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109212"
---
# <a name="tutorial-discover-physical-servers-with-server-assessment"></a>Zelfstudie: Fysieke servers detecteren met Serverevaluatie

Als onderdeel van uw migratietraject naar Azure, detecteert u uw servers voor evaluatie en migratie.

In deze zelfstudie leert u meer over het detecteren van on-premises fysieke servers met Azure Migrate: Serverevaluatie en een licht Azure Migrate-apparaat. U implementeert het apparaat als een fysieke server, zodat de metagegevens van de computer en de prestaties continu worden gedetecteerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure-account instellen.
> * Fysieke servers voorbereiden voor detectie.
> * Maak een Azure Migrate-project.
> * Het Azure Migrate-apparaat instellen.
> * Continue detectie starten.

> [!NOTE]
> Zelfstudies laten de snelste manier zien om een scenario uit te proberen, en gebruiken de standaardopties.  

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Controleer of deze vereisten aanwezig zijn voordat u met deze zelfstudie begint.

**Vereiste** | **Details**
--- | ---
**Apparaat** | U hebt een machine nodig waarop het Azure Migrate-apparaat kan worden uitgevoerd. De machine moet aan de volgende voorwaarden voldoen:<br/><br/> -Windows Server 2016 is geïnstalleerd. _(Momenteel wordt de implementatie van apparaten alleen ondersteund voor Windows Server 2016.)_<br/><br/> - Ongeveer 16 GB RAM, 8 vCPU's en 80 GB schijfruimte.<br/><br/> -Een statisch of dynamisch IP-adres met internettoegang, hetzij rechtstreeks of via een proxy.
**Windows-servers** | Sta binnenkomende verbindingen op WinRM-poort 5985 (HTTP) toe, zodat het apparaat metagegevens over de configuratie en prestaties kan ophalen.
**Linux-servers** | Sta binnenkomende verbindingen op poort 22 (TCP) toe.

## <a name="prepare-an-azure-user-account"></a>Een Azure-gebruikersaccount voorbereiden

Om een Azure Migrate-project te maken en het Azure Migrate-apparaat te registreren, hebt u een account nodig met:
- Machtigingen op inzender- of eigenaarniveau voor een Azure-abonnement.
- Machtigingen verlenen om Azure Active Directory-apps te registreren.

Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement. Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de volgende machtigingen toe te wijzen:

1. Zoek in de Azure Portal naar 'Abonnementen' en selecteer onder **Services** **Abonnementen**.

    ![Zoekvak om te zoeken naar het Azure-abonnement](./media/tutorial-discover-physical/search-subscription.png)

2. Selecteer op de pagina **Abonnementen** het abonnement waarin u een Azure Migrate-project wilt maken. 
3. Selecteer onder het abonnement de optie **Toegangsbeheer (IAM)**  > **Toegang controleren**.
4. Zoek onder **Toegang controleren** naar het relevante gebruikersaccount.
5. Klik onder **Een roltoewijzing toevoegen** op **Toevoegen**.

    ![Een gebruikersaccount zoeken om toegang te controleren en een rol toe te wijzen](./media/tutorial-discover-physical/azure-account-access.png)

6. Selecteer onder **Roltoewijzing toevoegen** de rol Inzender of Eigenaar en selecteer account (azmigrateuser in ons voorbeeld). Klik vervolgens op **Opslaan**.

    ![Hiermee opent u de pagina Roltoewijzing toevoegen om een rol aan het account toe te wijzen](./media/tutorial-discover-physical/assign-role.png)

7. Zoek in de portal naar gebruikers en selecteer onder **Services** **Gebruikers**.
8. Controleer onder **Gebruikersinstellingen** of Azure AD-gebruikers toepassingen kunnen registreren (standaard ingesteld op **Ja**).

    ![Verifiëren onder Gebruikersinstellingen of gebruikers Active Directory-apps kunnen registreren](./media/tutorial-discover-physical/register-apps.png)

9. Als alternatief kan een tenant/globale beheerder de rol van **toepassingsontwikkelaar** toewijzen aan het account om de registratie van AAD-apps mogelijk te maken. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-physical-servers"></a>Fysieke servers voorbereiden

Stel een account in dat het apparaat kan gebruiken om toegang te krijgen tot de fysieke servers.

- Gebruik voor Windows-servers een domeinaccount voor computer die lid zijn van een domein, en een lokaal account voor computers die geen lid zijn van een domein. Het gebruikersaccount moet worden toegevoegd aan deze groepen: Gebruikers van extern beheer, prestatiemetergebruikers en gebruikers van prestatielogboeken.
- Voor Linux-servers hebt u een hoofdaccount nodig op de Linux-servers die u wilt detecteren. U kunt ook een zich niet in de hoofdmap bevindend account met de vereiste mogelijkheden instellen met behulp van de volgende opdrachten:

**Opdracht** | **Doel**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(als /usr/sbin/fdisk niet aanwezig is)_ | Gegevens over de schijfconfiguratie verzamelen
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Gegevens van de schijfprestaties verzamelen
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Het BIOS-serienummer verzamelen
chmod a+r /sys/class/dmi/id/product_uuid | BIOS-GUID verzamelen


## <a name="set-up-a-project"></a>Een project instellen

Stel als volgt een nieuw Azure Migrate-project in.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer onder **Overzicht** de optie **Project maken**.
5. Selecteer onder **Project maken** uw Azure-abonnement en resourcegroep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Vakken voor projectnaam en regio](./media/tutorial-discover-physical/new-project.png)

7. Selecteer **Maken**.
8. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd.

Het hulpprogramma **Azure Migrate: Serverevaluatie** wordt standaard toegevoegd aan het nieuwe project.

![Pagina waarop wordt weergegeven dat het hulpprogramma Serverevaluatie standaard wordt toegevoegd](./media/tutorial-discover-physical/added-tool.png)


## <a name="set-up-the-appliance"></a>Het apparaat instellen

Om het apparaat in te stellen, moet u het volgende doen:
- Geef een naam op voor het apparaat en genereer een Azure Migrate-projectsleutel in de portal.
- Download een zip-bestand met Azure Migrate-installatiescript uit de Azure-portal.
- Pak de inhoud uit het zip-bestand uit. Start PowerShell-console met beheerdersbevoegdheden.
- Voer het PowerShell-script uit om de webtoepassing voor het apparaat te starten.
- Configureer het apparaat voor het eerst en registreer het bij het Azure Migrate-project met behulp van de Azure Migrate-projectsleutel.

### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate-projectsleutel genereren

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. In **Computers detecteren** > **Zijn de computers gevirtualiseerd?** selecteert u **Fysiek of anders (AWS, GCP, Xen enzovoorts)** .
3. In **1: Azure Migrate-projectsleutel genereren** geeft u een naam op voor het Azure Migrate-apparaat dat u voor de detectie van fysieke of virtuele servers wilt instellen. De naam moet alfanumeriek zijn en 14 tekens of minder bevatten.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina Computers detecteren niet tijdens het maken van resources.
1. Nadat de Azure-resources zijn gemaakt, wordt er een **Azure Migrate-projectsleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="download-the-installer-script"></a>Download het installatiescript

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
        Fysiek (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140334) | ce5e6f0507936def8020eb7b3109173dad60fc51dd39c3bd23099bc9baaabe29

    - Voor Azure Government:

        **Scenario** | **Downloaden** _ | _ *Hash-waarde**
        --- | --- | ---
        Fysiek (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140338) | ae132ebc574caf231bf41886891040ffa7abbe150c8b50436818b69e58622276
 

### <a name="run-the-azure-migrate-installer-script"></a>Het Azure Migrate-installatiescript uitvoeren
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

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Open een browser op een computer die verbinding kan maken met het apparaat en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad door te klikken op de snelkoppeling naar de app.
2. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Connectiviteit**: De app controleert of de server toegang heeft tot internet. Als de server gebruikmaakt van een proxy:
        - Klik op **Proxy instellen** en geef het proxyadres (in het formulier http://ProxyIPAddress of http://ProxyFQDN) ) en de controlepoort op.
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
        - Als u proxydetails hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteitscontrole opnieuw te activeren.
    - **Tijdsynchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat serverdetectie goed werkt.
    - **Updates installeren**: Azure Migrate-serverevaluatie controleert of de meest recente updates op het apparaat zijn geïnstalleerd. Na de controle kunt u op **Apparaatservices weergeven** om de status en versies te bekijken van de onderdelen die op het apparaat worden uitgevoerd.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **Azure Migrate-projectsleutel** die u in de portal hebt gekopieerd. Als u de sleutel niet hebt, gaat u naar **Serverevaluatie > Detecteren > Bestaande apparaten beheren**, selecteert u de naam van het apparaat die u hebt ingevoerd op het moment dat de sleutel werd gegenereerd en kopieert u de bijbehorende sleutel.
1. Klik op **Aanmelden**. Er wordt een Azure-aanmeldingsprompt geopend in een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app. 
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste [machtigingen ]() heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratiedetails zien door op **Details weergeven** te klikken.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak nu verbinding vanaf het apparaat met de fysieke servers die moeten worden gedetecteerd en start de detectie.

1. In **Stap 1: Geef referenties op voor de detectie van fysieke of virtuele Windows- en Linux-servers**, en klik op **Referenties toevoegen**.
1. Selecteer voor een Windows-server de optie **Windows-server** als brontype. Geef een beschrijvende naam op voor de referenties, en voeg de gebruikersnaam en het wachtwoord toe. Klik op **Opslaan**.
1. Als u verificatie op basis van een wachtwoord gebruikt voor een Linux-server, selecteert u **Linux-server (op basis van wachtwoord)** als brontype. Geef een beschrijvende naam op voor de referenties, en voeg de gebruikersnaam en het wachtwoord toe. Klik op **Opslaan**.
1. Als u gebruikmaakt van verificatie op basis van een SSH-sleutel voor een Linux-server, kunt u **Linux-server (op basis van SSH-sleutel)** selecteren. Geef een beschrijvende naam op voor de referenties, voeg de gebruikersnaam toe, ga naar de persoonlijke SSH-sleutel en selecteer deze. Klik op **Opslaan**.

    - Azure Migrate biedt ondersteuning voor de persoonlijke SSH-sleutel die wordt gegenereerd met de opdracht ssh-keygen, met behulp van de algoritmen RSA, DSA, ECDSA en ed25519.
    - Azure Migrate biedt momenteel geen ondersteuning voor SSH-sleutels op basis van een wachtwoordzin. Gebruik een SSH-sleutel zonder een wachtwoordzin.
    - Azure Migrate biedt momenteel geen ondersteuning voor persoonlijke SSH-sleutelbestanden gegenereerd met PuTTY.

1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **Meer toevoegen** om meer referenties op te slaan en toe te voegen. Er worden meerdere referenties ondersteund voor detectie van fysieke servers.
1. In **Stap 2: Geef de gegevens voor de fysieke of virtuele server op**, klik op **Detectiebron toevoegen** om het **IP-adres of de FQDN** van de server op te geven en tevens de beschrijvende naam voor de referenties waarmee verbinding wordt gemaakt met de server.
1. U kunt **één item per keer toevoegen** of **meerdere items in één keer toevoegen**. Er is ook een optie om de gegevens van een server op te geven via **CSV importeren**.


    - Als u kiest voor **Eén item toevoegen**, kunt u het type besturingssysteem kiezen, een beschrijvende naam opgeven voor referenties en het **IP-adres of de FQDN** van de server toevoegen. Klik vervolgens op **Opslaan**.
    - Als u kiest voor **Meerdere items toevoegen**, kunt u meerdere records tegelijk toevoegen door het **IP-adres of de FQDN** van de server met de beschrijvende naam voor referenties in het tekstvak op te geven. **Controleer** de toegevoegde records en klik op **Opslaan**.
    - Als u **CSV importeren** _(standaard geselecteerd)_ kiest, kunt u een CSV-sjabloonbestand downloaden, het bestand vullen met het **IP-adres of de FQDN** van de server en een beschrijvende naam voor referenties. Vervolgens importeert u het bestand in het apparaat, **controleert u** de records in het bestand en klikt u op **Opslaan**.

1. Wanneer u op Opslaan klikt, wordt de verbinding met de toegevoegde servers gevalideerd en wordt de **validatiestatus** in de tabel voor elke server weergegeven.
    - Als de validatie voor een server mislukt, bekijkt u de fout door in de kolom Status van de tabel op **Validatie mislukt** te klikken. Los het probleem op en valideer opnieuw.
    - Als u een server wilt verwijderen, klikt u op **Verwijderen**.
1. Voordat u de detectie start, kunt u de connectiviteit van servers altijd **opnieuw valideren**.
1. Klik op **Detectie starten** om met detectie van de gevalideerde servers te beginnen. Nadat de detectie is gestart, kunt u de detectiestatus controleren voor elke server in de tabel.


De detectie wordt gestart. Het duurt ongeveer 2 minuten per server voordat de metagegevens van de gedetecteerde server worden weergegeven in de Azure-portal.

## <a name="verify-servers-in-the-portal"></a>Verifieer servers in de portal

Nadat de detectie is voltooid, kunt u controleren of de servers worden weergegeven in de portal.

1. Open het Azure Migrate-dashboard.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** op het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.
## <a name="next-steps"></a>Volgende stappen

- [Fysieke servers beoordelen](tutorial-assess-physical.md) voor migratie naar virtuele Azure-machines.
- [De gegevens controleren ](migrate-appliance.md#collected-data---physical) die door het apparaat worden verzameld tijdens de detectie.
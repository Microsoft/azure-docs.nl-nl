---
title: Een Azure Migrate apparaat instellen voor fysieke servers
description: Meer informatie over het instellen van een Azure Migrate apparaat voor detectie en evaluatie van fysieke servers.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 9052cbd3dc728b50b62c33f3a11a5e36a7504f29
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771563"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Een apparaat instellen voor fysieke servers

In dit artikel wordt beschreven hoe u het Azure Migrate apparaat instelt als u fysieke servers wilt beoordelen met de Azure Migrate: hulp programma voor detectie en evaluatie.

Het Azure Migrate apparaat is een licht gewicht apparaat, dat wordt gebruikt door Azure Migrate: detectie en evaluatie om het volgende te doen:

- On-premises servers detecteren.
- Meta gegevens en prestatie gegevens voor gedetecteerde servers verzenden naar Azure Migrate: detectie en evaluatie.

[Meer informatie](migrate-appliance.md) over het Azure Migrate-apparaat.


## <a name="appliance-deployment-steps"></a>Stappen voor implementatie van het apparaat

Om het apparaat in te stellen, moet u het volgende doen:

- Geef een naam op voor het apparaat en Genereer een project sleutel in de portal.
- Download een zip-bestand met Azure Migrate-installatiescript uit de Azure-portal.
- Pak de inhoud uit het zip-bestand uit. Start PowerShell-console met beheerdersbevoegdheden.
- Voer het PowerShell-script uit om de webtoepassing voor het apparaat te starten.
- Configureer het apparaat voor de eerste keer en registreer het met het project met behulp van de project sleutel.

### <a name="generate-the-project-key"></a>De project sleutel genereren

1. In **migratie doelen**  >  **Windows, Linux-en SQL-servers**  >  **Azure migrate: detectie en evaluatie**, selecteer **detecteren**.
2. In **Discover-servers**  >  **zijn uw servers gevirtualiseerd?** selecteert u **fysiek of ander (AWS, GCP, xen, enzovoort)**.
3. Geef in **1: project sleutel genereren** een naam op voor het Azure migrate apparaat dat u wilt instellen voor de detectie van fysieke of virtuele servers. De naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina servers detecteren niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt er een **project sleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="download-the-installer-script"></a>Download het installatiescript

In **2: Download Azure Migrate-apparaat**, klik op **Downloaden**.

   ![Selecties voor Computers detecteren](./media/tutorial-assess-physical/servers-discover.png)


   ![Selecties voor Sleutel genereren](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een opdracht venster voor beheerders op de servers waarnaar u het bestand hebt gedownload.
2. Gebruik de volgende opdracht om de hash voor het zip-bestand te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld van gebruik voor openbare cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Voorbeeld van gebruik voor overheidscloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Controleer de nieuwste versie van het apparaat en de instellingen van de [hash-waarden](tutorial-discover-physical.md#verify-security) .
 

## <a name="run-the-azure-migrate-installer-script"></a>Het Azure Migrate-installatiescript uitvoeren
Het installatiescript doet het volgende:

- Installeert agents en een webtoepassing voor detectie en evaluatie van fysieke servers.
- Installeer Windows-rollen, waaronder Windows-activeringsservice, IIS en PowerShell ISE.
- Een herschrijfbare module van IIS downloaden en installeren. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een registersleutel (HKLM) bij met permanente instellingsgegevens voor Azure Migrate.
- Maakt de volgende bestanden onder het pad:
    - **Configuratiebestanden**: %Programdata%\Microsoft Azure\Config
    - **Logboekbestanden**: %Programdata%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat.  Zorg ervoor dat u het script niet uitvoert op een server met een bestaand Azure Migrate apparaat.
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

### <a name="configure-the-appliance"></a>Het apparaat configureren

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

    - Azure Migrate ondersteunt de persoonlijke SSH-sleutel die wordt gegenereerd door de opdracht ssh-keygen met behulp van RSA-, DSA-, ECDSA-en ed25519-algoritmen.
    - Momenteel wordt Azure Migrate geen SSH-sleutel op basis van een wachtwoordzin ondersteund. Gebruik een SSH-sleutel zonder wachtwoordzin.
    - Azure Migrate biedt momenteel geen ondersteuning voor persoonlijke SSH-sleutelbestanden gegenereerd met PuTTY.
    - Azure Migrate biedt ondersteuning voor de OpenSSH-indeling van het persoonlijke SSH-sleutelbestand, zoals hieronder weergegeven:
    
    ![Ondersteunde indeling voor persoonlijke SSH-sleutels](./media/tutorial-discover-physical/key-format.png)
1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **Meer toevoegen** om meer referenties op te slaan en toe te voegen. Er worden meerdere referenties ondersteund voor detectie van fysieke servers.
1. In **Stap 2: Geef de gegevens voor de fysieke of virtuele server op**, klik op **Detectiebron toevoegen** om het **IP-adres of de FQDN** van de server op te geven en tevens de beschrijvende naam voor de referenties waarmee verbinding wordt gemaakt met de server.
1. U kunt **één item per keer toevoegen** of **meerdere items in één keer toevoegen**. Er is ook een optie om de gegevens van een server op te geven via **CSV importeren**.

    ![Selecties voor het toevoegen van de detectiebron](./media/tutorial-assess-physical/add-discovery-source-physical.png)

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

Probeer de [evaluatie van fysieke servers](tutorial-assess-physical.md) uit met Azure migrate: detectie en evaluatie.
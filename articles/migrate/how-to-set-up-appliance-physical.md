---
title: Een Azure Migrate apparaat instellen voor fysieke servers
description: Meer informatie over het instellen van een Azure Migrate apparaat voor de evaluatie van fysieke servers.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/15/2020
ms.openlocfilehash: 73c3d529978c91946632ed599f02b8938830621e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705316"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Een apparaat instellen voor fysieke servers

In dit artikel wordt beschreven hoe u het Azure Migrate apparaat instelt als u fysieke servers wilt beoordelen met het Azure Migrate: Server Assessment-hulp programma.

Het Azure Migrate-apparaat is een licht apparaat dat door de Azure Migrate-serverevaluatie wordt gebruikt om het volgende te doen:

- On-premises servers detecteren.
- Metagegevens en prestatiegegevens voor gedetecteerde servers verzenden naar Azure Migrate-serverevaluatie.

[Meer informatie](migrate-appliance.md) over het Azure Migrate-apparaat.


## <a name="appliance-deployment-steps"></a>Stappen voor implementatie van het apparaat

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

   ![Selecties voor Computers detecteren](./media/tutorial-assess-physical/servers-discover.png)


   ![Selecties voor Sleutel genereren](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
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
1. U hebt een apparaatcode nodig om te verifiëren bij Azure. Als u klikt op **Aanmelden**, wordt er een modaal met de apparaatcode geopend, zoals hieronder weergegeven.

    ![Modaal waarin de apparaatcode wordt weergegeven](./media/tutorial-discover-vmware/device-code.png)

1. Klik op **Code kopiëren en aanmelden** om de apparaatcode te kopiëren en een Azure-aanmeldingsprompt te openen op een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Plak de apparaatcode in het nieuwe tabblad en meld u aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Als u het aanmeldingstabblad per ongeluk sluit zonder u aan te melden, vernieuwt u het browsertabblad van Apparaatconfiguratiebeheer om de knop Aanmelden opnieuw in te schakelen.
1. Als u bent aangemeld, gaat u terug naar het vorige tabblad in Apparaatconfiguratiebeheer.
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste [machtigingen ](./tutorial-discover-physical.md) heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
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
    - Azure Migrate biedt ondersteuning voor de OpenSSH-indeling van het persoonlijke SSH-sleutelbestand, zoals hieronder weergegeven:
    
    ![Ondersteunde indeling voor persoonlijke SSH-sleutels](./media/tutorial-discover-physical/key-format.png)
1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **Meer toevoegen** om meer referenties op te slaan en toe te voegen. Er worden meerdere referenties ondersteund voor detectie van fysieke servers.
1. In **Stap 2: Geef de gegevens voor de fysieke of virtuele server op**, klik op **Detectiebron toevoegen** om het **IP-adres of de FQDN** van de server op te geven en tevens de beschrijvende naam voor de referenties waarmee verbinding wordt gemaakt met de server.
1. U kunt **één item per keer toevoegen** of **meerdere items in één keer toevoegen**. Er is ook een optie om de gegevens van een server op te geven via **CSV importeren**.

    ![Selecties voor het toevoegen van de detectiebron](./media/tutorial-assess-physical/add-discovery-source-physical.png)

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

Probeer [fysieke servers te evalueren](tutorial-assess-physical.md) met Azure migrate server-evaluatie.
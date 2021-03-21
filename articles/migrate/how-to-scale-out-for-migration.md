---
title: Een Azure Migrate scale-out-toestel instellen voor VMware-migratie zonder agent
description: Meer informatie over het instellen van een Azure Migrate scale-out-toestel voor het migreren van virtuele Hyper-V-machines.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 1425eafd92737e08596499e395dc62af3d967207
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607650"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>De migratie van virtuele VMware-machines in een agent schalen naar Azure

In dit artikel leert u hoe u een scale-out-toestel kunt gebruiken voor het migreren van een groot aantal virtuele VMware-machines naar Azure met behulp van de agentloze methode van het Azure Migrate server migratie hulpprogramma voor de migratie van virtuele VMware-machines.

Met de migratie methode zonder agent voor virtuele VMware-machines kunt u het volgende doen:

- U kunt Maxi maal 300 Vm's van één vCenter-Server gelijktijdig repliceren met behulp van een Azure Migrate apparaat.
- U kunt Maxi maal 500 Vm's gelijktijdig repliceren vanaf één vCenter-Server door een tweede scale-out-toestel te implementeren voor migratie.

In dit artikel leert u het volgende:

- Een scale-out-toestel toevoegen voor virtuele VMware-machines die zonder agents worden gemigreerd
- Migreer Maxi maal 500 Vm's tegelijk met behulp van het scale-out-toestel.

##  <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u de volgende stappen uitvoeren:

- Maak het Azure Migrate-project.
- Implementeer het Azure Migrate apparaat (primair apparaat) en voltooi de detectie van virtuele VMware-machines die worden beheerd door uw vCenter-Server.
- Configureer replicatie voor een of meer virtuele machines die moeten worden gemigreerd.
> [!IMPORTANT]
> U moet ten minste één virtuele machine repliceren in het project voordat u een scale-out-toestel voor migratie kunt toevoegen.

Raadpleeg de zelf studie over het [migreren van virtuele VMware-machines naar Azure met de migratie methode zonder agent](./tutorial-migrate-vmware.md)voor meer informatie over het uitvoeren van het bovenstaande.

## <a name="deploy-a-scale-out-appliance"></a>Een scale-out-toestel implementeren

Volg de onderstaande stappen om een scale-out apparaat toe te voegen:

1. Klik op **Discover**  >  **worden computers gevirtualiseerde?** 
1. Selecteer **Ja, met VMware VSphere Hyper Visor.**
1. Selecteer in de volgende stap replicatie zonder agent.
1. Selecteer **een bestaand primair apparaat uitschalen** in het menu Type apparaat selecteren.
1. Selecteer het primaire apparaat (het apparaat dat wordt gebruikt om de detectie uit te voeren) dat u wilt uitschalen.

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="Pagina computers detecteren voor uitgaand uitschalen":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. de Azure Migrate project sleutel genereren

1. Geef in **Azure migrate project sleutel genereren** een achtervoegsel naam op voor het scale-out-toestel. Het achtervoegsel mag alleen alfanumerieke tekens bevatten en mag Maxi maal 14 tekens lang zijn.
2. Klik op **sleutel genereren** om te beginnen met het maken van de vereiste Azure-resources. Sluit de detectie pagina niet tijdens het maken van resources.
3. Kopieer de gegenereerde sleutel. U hebt de sleutel later nodig om de registratie van het scale-out-toestel te volt ooien.

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. down load het installatie programma voor het scale-out-toestel

Klik in **Azure migrate apparaat downloaden** op  **downloaden**. U moet het Power shell-installatie script downloaden om het scale-out-toestel te implementeren op een bestaande server met Windows Server 2016 en met de vereiste hardwareconfiguratie (32 GB RAM, 8 Vcpu's, ongeveer 80 GB aan schijf opslag en Internet toegang, hetzij rechtstreeks, hetzij via een proxy).
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="Script voor scale-out apparaat downloaden":::

> [!TIP]
> U kunt de controlesom van het gedownloade zip-bestand valideren met behulp van de volgende stappen:
>
> 1. Open de opdracht prompt als beheerder
> 2. Gebruik de volgende opdracht om de hash voor het zip-bestand te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld van gebruik voor openbare cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. Down load de nieuwste versie van het installatie programma voor de scale-out-toestel vanuit de portal als de berekende hash-waarde niet overeenkomt met deze teken reeks: e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Voer het Azure Migrate-installatie script uit
Het installatiescript doet het volgende:

- Installeert gateway agent en configuratie beheer van het apparaat om meer gelijktijdige server replicaties uit te voeren.
- Installeer Windows-rollen, waaronder Windows-activeringsservice, IIS en PowerShell ISE.
- Een herschrijfbare module van IIS downloaden en installeren. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een registersleutel (HKLM) bij met permanente instellingsgegevens voor Azure Migrate.
- Maakt de volgende bestanden onder het pad:
    - **Configuratiebestanden**: %Programdata%\Microsoft Azure\Config
    - **Logboekbestanden**: %Programdata%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het scale-out toestel.  Zorg ervoor dat u het script niet uitvoert op een server met een bestaand Azure Migrate apparaat.
2. Start PowerShell op de bovenstaande server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map waarin de inhoud is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script met de naam **AzureMigrateInstaller.ps1**  uit met de volgende opdracht:

    - Voor de openbare cloud: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    Het script start het configuratie beheer van het apparaat wanneer de uitvoering is voltooid.

Als u problemen ondervindt, kunt u de script Logboeken openen in: <br/> C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log voor het oplossen van problemen.


### <a name="4-configure-the-appliance"></a>4. het apparaat configureren

Voordat u begint, moet u ervoor zorgen dat de [Azure-eind punten](migrate-appliance.md#public-cloud-urls) toegankelijk zijn via het scale-out-apparaat.

- Open een browser op een computer die verbinding kan maken met de scale-out toestel server en open de URL van de configuratie manager van het apparaat: **https://*scale-out apparaatnaam of IP-adres*: 44368**.

   U kunt de Configuration Manager ook openen vanaf het bureau blad van de scale-out-toestel server met behulp van de snelkoppeling naar Configuration Manager.
- Accepteer de **licentievoorwaarden** en lees de informatie van derden.
- Ga als volgt te werk in de Configuration Manager-> vereisten in te **stellen**:
   - **Connectiviteit**: het apparaat controleert of de server toegang heeft tot internet. Als de server gebruikmaakt van een proxy:
     1. Klik op **Proxy instellen** en geef het proxyadres (in het formulier http://ProxyIPAddress of http://ProxyFQDN) ) en de controlepoort op.
     2. Geef referenties op als de proxy verificatie nodig heeft.
     3. Alleen HTTP-proxy wordt ondersteund.
     4. Als u proxydetails hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteitscontrole opnieuw te activeren.
   - **Tijdsynchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat detectie goed werkt.
   - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates zijn geïnstalleerd. Nadat de controle is voltooid, kunt u klikken op **apparaten weer geven** om de status en versies van de services die op de toestel server worden uitgevoerd te bekijken.
   - **VDDK installeren**: het apparaat controleert of VMware vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als dat niet het geval is, downloadt u VDDK 6,7 van VMware en extraheert u de gedownloade zip-inhoud naar de opgegeven locatie op het apparaat, zoals is opgegeven in de **installatie-instructies** op het scherm apparaat Configuration Manager.


### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **Azure Migrate-projectsleutel** die u in de portal hebt gekopieerd. Als u de sleutel niet hebt, gaat u naar **Server Assessment> Discover> bestaande apparaten beheren**, selecteert u de naam van het primaire apparaat, zoekt u het scale-out-toestel dat eraan is gekoppeld en kopieert u de bijbehorende sleutel.
1. U hebt een apparaatcode nodig om te verifiëren bij Azure. Als u klikt op **Aanmelden**, wordt er een modaal met de apparaatcode geopend, zoals hieronder weergegeven.
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Modaal waarin de apparaatcode wordt weergegeven":::

1. Klik op **Code kopiëren en aanmelden** om de apparaatcode te kopiëren en een Azure-aanmeldingsprompt te openen op een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Plak de apparaatcode in het nieuwe tabblad en meld u aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Als u het aanmeldingstabblad per ongeluk sluit zonder u aan te melden, vernieuwt u het browsertabblad van Apparaatconfiguratiebeheer om de knop Aanmelden opnieuw in te schakelen.
1. Nadat u bent aangemeld, gaat u terug naar het vorige tabblad met het configuratie beheer van het apparaat.
1. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste machtigingen  heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="Deel 2 van het configuratie beheer van het apparaat":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>Configuratie van het toestel importeren van het primaire apparaat

Als u de registratie van het scale-out apparaat wilt volt ooien, klikt u op **importeren** om de benodigde configuratie bestanden van het primaire apparaat op te halen.

1. Als u op **importeren** klikt, wordt er een pop-upvenster geopend met instructies voor het importeren van de benodigde configuratie bestanden van het primaire apparaat.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="Configuratie modaal importeren":::
1. Meld u aan bij het primaire apparaat (extern bureau blad) en voer de volgende Power shell-opdrachten uit:

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. Kopieer het zip-bestand dat u hebt gemaakt door de bovenstaande opdrachten uit te voeren naar het scale-out-apparaat. Het zip-bestand bevat configuratie bestanden die nodig zijn om het scale-out apparaat te registreren.
1. Selecteer in het pop-upvenster dat u in de vorige stap hebt geopend de locatie van het gekopieerde zip-bestand voor de configuratie en klik op **Opslaan**.

Zodra de bestanden zijn geïmporteerd, wordt de registratie van het Scale-outapparaat voltooid en ziet u de tijds tempel van de laatste geslaagde import bewerking. U kunt de registratie gegevens ook bekijken door te klikken op **Details weer geven**.
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="In de scherm afbeelding wordt de registratie van het uitschalen van een apparaat met Azure Migrate project weer gegeven.":::

Op dit moment moet u opnieuw valideren of het scale-out-apparaat verbinding kan maken met uw vCenter-Server. Klik op opnieuw **valideren** om vCenter Server connectiviteit van scale-out apparaat te valideren.
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="Scherm afbeelding toont de weer gave van referenties en detectie bronnen die moeten worden gevalideerd.":::

> [!IMPORTANT]
> Als u de vCenter Server referenties op het primaire apparaat bewerkt, moet u ervoor zorgen dat u de configuratie bestanden opnieuw importeert naar het scale-out-apparaat om de nieuwste configuratie op te halen en doorlopende replicaties voort te zetten.<br/> Als u het scale-out-apparaat niet langer nodig hebt, moet u ervoor zorgen dat u het scale-out apparaat uitschakelt. Meer [**informatie**](./common-questions-appliance.md) over het uitschakelen van het scale-out toestel wanneer dit niet nodig is.

## <a name="replicate"></a>Repliceren

1. Nadat het uitbreidings apparaat is geregistreerd, klikt u in de tegel Azure Migrate: Server migratie op **repliceren**.

2.  Volg de stappen op het scherm om te beginnen met het repliceren van meer virtuele machines. 

Met het scale-out toestel kunt u nu gelijktijdig 500 Vm's repliceren. U kunt ook Vm's in batches van 200 migreren via de Azure Portal.

Het hulp programma voor migratie van Azure Migrate-server zorgt ervoor dat de virtuele machines tussen het primaire en uitbreid bare apparaat voor replicatie worden verdeeld. Zodra de replicatie is voltooid, kunt u de virtuele machines migreren.

> [!TIP]
> Het is raadzaam virtuele machines in batches 200 te migreren voor optimale prestaties als u een groot aantal virtuele machines wilt migreren.
  
## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u het volgende geleerd:
- Een scale-out-toestel configureren
- Vm's repliceren met behulp van een scale-out apparaat


Meer [informatie](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) over het migreren van servers naar Azure met Azure migrate: hulp programma voor server migratie.
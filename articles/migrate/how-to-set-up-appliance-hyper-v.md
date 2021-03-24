---
title: Een Azure Migrate apparaat instellen voor Hyper-V
description: Meer informatie over het instellen van een Azure Migrate apparaat voor het beoordelen en migreren van servers op Hyper-V.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 71fe30212b31e810bfe3e1ba10f80be6b09ad4fc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863680"
---
# <a name="set-up-an-appliance-for-servers-on-hyper-v"></a>Een apparaat instellen voor servers met Hyper-V

Volg dit artikel om het Azure Migrate-apparaat in te stellen voor detectie en evaluatie van servers op Hyper-V met het Azure Migrate: hulp programma voor [detectie en evaluatie](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) .

Het [Azure migrate apparaat](migrate-appliance.md)  is een licht gewicht dat door Azure migrate wordt gebruikt: detectie en evaluatie/migratie om on-premises servers op Hyper-V te ontdekken en server meta gegevens/prestatie gegevens naar Azure te verzenden.

U kunt het apparaat implementeren met een aantal methoden:

- Ingesteld op een server op Hyper-V met behulp van een gedownloade VHD. Deze methode wordt beschreven in dit artikel.
- Ingesteld op een server op een Hyper-V-of fysieke server met een Power shell-installatie script. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u een server niet kunt instellen met behulp van een VHD of als u zich in azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate: detectie en evaluatie, het voor de eerste keer configureren en het met het project registreren.

## <a name="appliance-deployment-vhd"></a>Implementatie van het apparaat (VHD)

Het apparaat instellen met behulp van een VHD-sjabloon:

- Geef een naam op voor het apparaat en Genereer een project sleutel in de portal.
- Download een gecomprimeerde Hyper-V VHD vanuit de Azure Portal.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate: detectie en evaluatie.
- Configureer het apparaat voor de eerste keer en registreer het met het project met behulp van de project sleutel.

### <a name="generate-the-project-key"></a>De project sleutel genereren

1. In **migratie doelen**  >  **Windows, Linux-en SQL-servers**  >  **Azure migrate: detectie en evaluatie**, selecteer **detecteren**.
2. In **Discover-servers**  >  **zijn uw servers gevirtualiseerd?**, selecteert u **Ja, met Hyper-V**.
3. Geef in **1: project sleutel genereren** een naam op voor het Azure migrate apparaat dat u wilt instellen voor de detectie van servers op Hyper-V. de naam moet alfanumeriek zijn met 14 tekens of minder.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina servers detecteren niet af tijdens het maken van resources.
1. Nadat het maken van de Azure-resources is voltooid, wordt een **project sleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="download-the-vhd"></a>De VHD downloaden

In **2: Azure Migrate-apparaat downloaden**, selecteert u het VHD-bestand en klikt u op **Downloaden**.

   ![Selecties voor discover-servers](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Selecties voor Sleutel genereren](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open een Administrator-opdracht venster op de server waarnaar u het bestand hebt gedownload.
2. Voer de volgende opdracht uit om de hash voor de VHD te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance"></a>Het apparaat maken

Importeer het gedownloade bestand en maak een apparaat.

1. Pak het gecomprimeerde VHD-bestand uit in een map op de Hyper-V-host die als host fungeert voor het apparaat. Er worden drie mappen uitgepakt.
2. Open Hyper-V-beheer. Klik in **Acties** op **Virtuele machine importeren**.

    ![VHD implementeren](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Klik in de wizard Virtuele machine importeren > **Voordat u begint** op **Volgende**.
3. Geef onder **Map zoeken** de map op die de uitgepakte VHD bevat. Klik op **Volgende**.
1. Klik in **Virtuele machine selecteren** op **Volgende**.
2. Klik in **Importtype kiezen** op **De virtuele machine kopiëren (een nieuwe unieke id maken)** . Klik op **Volgende**.
3. Laat in **Bestemming kiezen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
4. Laat in **Opslagmappen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
5. Geef in **netwerk kiezen** de virtuele switch op die door de server wordt gebruikt. De switch heeft internetverbinding nodig om gegevens naar Azure te verzenden.
6. Controleer de instellingen in **Samenvatting**. Klik vervolgens op **Voltooien**.
7. Start de virtuele machine in Hyper-V-beheer > **Virtual Machines**.


### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade VHD, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in Hyper-V-beheer > **virtual machines** met de rechter muisknop op de server > **verbinding maken**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een systeem dat verbinding kan maken met het apparaat en open de URL van de Web-App van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door te klikken op de snelkoppeling naar de app.
1. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
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
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste [machtigingen ](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
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

Maak verbinding van het apparaat met Hyper-V-hosts of-clusters en start de detectie.

1. In **stap 1: referenties opgeven voor hyper-v-host**, klikt u op **referenties toevoegen** om een beschrijvende naam op te geven voor referenties, **gebruikers naam** en **wacht woord** voor een Hyper-V-host/cluster toevoegen die het apparaat gaat gebruiken om servers te detecteren. Klik op **Opslaan**.
1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **Meer toevoegen** om meer referenties op te slaan en toe te voegen. Er worden meerdere referenties ondersteund voor de detectie van servers op Hyper-V.
1. In **Stap 2: Geef de gegevens voor de Hyper-V-host of het Hyper-V-cluster op**, klik op **Detectiebron toevoegen** om het **IP-adres of de FQDN** van de Hyper-V-host of het Hyper-V-cluster op te geven en tevens de beschrijvende naam voor de referenties waarmee verbinding wordt gemaakt met de host of het cluster.
1. U kunt **één item per keer toevoegen** of **meerdere items in één keer toevoegen**. Er is ook een optie om de gegevens van een Hyper-V-host/-cluster op te geven via **CSV importeren**.

    ![Selecties voor het toevoegen van de detectiebron](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Als u kiest voor **Eén item toevoegen**, moet u een beschrijvende naam opgeven voor referenties en tevens het **IP-adres of de FQDN** van de Hyper-V-host of het -cluster. Klik vervolgens op **Opslaan**.
    - Als u **meerdere items toevoegen** _(standaard geselecteerd)_ hebt gekozen, kunt u meerdere records tegelijk toevoegen door een **IP-adres/FQDN** voor de Hyper-V-host/het cluster op te geven met de beschrijvende naam voor de referenties in het tekstvak. Verifieer * * de toegevoegde records en klik op **Opslaan**.
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
2. Klik in **Azure migrate-Windows-, Linux-en SQL-servers**  >  **Azure migrate: de pagina detectie en evaluatie** op het pictogram dat het aantal voor **gedetecteerde servers** weergeeft.

## <a name="next-steps"></a>Volgende stappen

Probeer de [Hyper-V-evaluatie](tutorial-assess-hyper-v.md) uit met Azure migrate: detectie en evaluatie.
---
title: Een Azure Migrate apparaat instellen voor Hyper-V
description: Meer informatie over het instellen van een Azure Migrate apparaat om Hyper-V-Vm's te beoordelen en te migreren.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 5c5fdd1423d806bcc4d2f124310112a3e407e416
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751118"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Een apparaat instellen voor virtuele Hyper-V-machines

Volg dit artikel om het Azure Migrate-apparaat in te stellen voor de evaluatie van virtuele Hyper-V-machines met het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool.

Het [Azure migrate apparaat](migrate-appliance.md)  is een licht gewicht dat door Azure migrate wordt gebruikt: Server evaluatie/migratie om on-premises virtuele Hyper-V-machines te detecteren en VM-meta gegevens/prestatie gegevens naar Azure te verzenden.

U kunt het apparaat implementeren met een aantal methoden:

- Instellen op een Hyper-V-VM met behulp van een gedownloade VHD. Dit is de methode die in dit artikel wordt beschreven.
- Ingesteld op een Hyper-V-VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u geen virtuele machine kunt instellen met behulp van een VHD of als u zich in azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate-serverevaluatie, configureert u het voor de eerste keer en registreert u het bij het Azure Migrate-project.

## <a name="appliance-deployment-vhd"></a>Implementatie van het apparaat (VHD)

Het apparaat instellen met behulp van een VHD-sjabloon:

- Geef een naam op voor het apparaat en genereer een Azure Migrate-projectsleutel in de portal.
- Download een gecomprimeerde Hyper-V VHD vanuit de Azure Portal.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate-serverevaluatie.
- Configureer het apparaat voor het eerst en registreer het bij het Azure Migrate-project met behulp van de Azure Migrate-projectsleutel.

### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate-projectsleutel genereren

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**.
3. In **1: Azure Migrate-projectsleutel genereren** geeft u een naam op voor het Azure Migrate-apparaat dat u voor de detectie van Hyper-V-VM's wilt instellen. De naam moet alfanumeriek zijn en 14 tekens of minder bevatten.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina Computers detecteren niet tijdens het maken van resources.
1. Nadat de Azure-resources zijn gemaakt, wordt er een **Azure Migrate-projectsleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="download-the-vhd"></a>De VHD downloaden

In **2: Azure Migrate-apparaat downloaden**, selecteert u het VHD-bestand en klikt u op **Downloaden**. 

   ![Selecties voor Computers detecteren](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![Selecties voor Sleutel genereren](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor de VHD te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v3.20.09.25.zip -Algorithm SHA256```





## <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak de virtuele machine.

1. Pak het gecomprimeerde VHD-bestand uit naar een map op de Hyper-V-host die als host moet fungeren voor de virtuele machine. Er worden drie mappen uitgepakt.
2. Open Hyper-V-beheer. Klik in **Acties** op **Virtuele machine importeren**.

    ![VHD implementeren](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. Klik in de wizard Virtuele machine importeren > **Voordat u begint** op **Volgende**.
3. Geef onder **Map zoeken** de map op die de uitgepakte VHD bevat. Klik op **Volgende**.
1. Klik in **Virtuele machine selecteren** op **Volgende**.
2. Klik in **Importtype kiezen** op **De virtuele machine kopiëren (een nieuwe unieke id maken)** . Klik op **Volgende**.
3. Laat in **Bestemming kiezen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
4. Laat in **Opslagmappen** de standaardinstelling ongewijzigd. Klik op **Volgende**.
5. Geef in **Netwerk kiezen** de virtuele switch op die door de virtuele machine wordt gebruikt. De switch heeft internetverbinding nodig om gegevens naar Azure te verzenden.
6. Controleer de instellingen in **Samenvatting**. Klik vervolgens op **Voltooien**.
7. Start de virtuele machine in Hyper-V-beheer > **Virtual Machines**.


### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade VHD, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in Hyper-V-beheer > **Virtual Machines** met de rechtermuisknop op de virtuele machine > **Verbinding maken**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de VM en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door te klikken op de snelkoppeling naar de app.
1. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine een proxy gebruikt:
      - Klik op **Proxy instellen** en geef het proxyadres (in het formulier http://ProxyIPAddress of http://ProxyFQDN) ) en de controlepoort op.
      - Geef referenties op als de proxy verificatie nodig heeft.
      - Alleen HTTP-proxy wordt ondersteund.
      - Als u proxydetails hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteitscontrole opnieuw te activeren.
    - **Tijdsynchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internettijd voor VM zodat detectie goed werkt.
    - **Updates installeren**: Azure Migrate-serverevaluatie controleert of de meest recente updates op het apparaat zijn geïnstalleerd. Na de controle kunt u op **Apparaatservices weergeven** om de status en versies te bekijken van de onderdelen die op het apparaat worden uitgevoerd.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **Azure Migrate-projectsleutel** die u in de portal hebt gekopieerd. Als u de sleutel niet hebt, gaat u naar **Serverevaluatie > Detecteren > Bestaande apparaten beheren**, selecteert u de naam van het apparaat die u hebt ingevoerd op het moment dat de sleutel werd gegenereerd en kopieert u de bijbehorende sleutel.
1. Klik op **Aanmelden**. Er wordt een Azure-aanmeldingsprompt geopend in een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app. 
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste [machtigingen ](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratiedetails zien door op **Details weergeven** te klikken.



### <a name="delegate-credentials-for-smb-vhds"></a>Referenties voor SMB-VHD's delegeren

Als u VHD's uitvoert op SMB's, moet u de delegatie van referenties van het apparaat naar de Hyper-V-hosts inschakelen. Als u dit wilt doen vanaf het apparaat:

1. Voer deze opdracht uit op de apparaat-VM. HyperVHost1/HyperVHost2 zijn voorbeelden van hostnamen.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
    ```

2. U kunt dit ook doen in de editor voor Lokaal groepsbeleid op het apparaat:
    - Klik in **Lokaal computerbeleid** > **Computerconfiguratie** op **Administratieve sjablonen** > **Systeem** > **Delegatie van referenties**.
    - Dubbelklik op **Delegeren van nieuwe referenties toestaan** en selecteer **Ingeschakeld**.
    - Klik in **Opties** op **Weergeven** en voeg elke Hyper-V-host toe die u op de lijst wilt detecteren, waarbij u **wsman/** gebruikt als voorvoegsel.
    - Dubbelklik in **Delegatie van referenties** op **Delegeren van nieuwe referenties toestaan met NTLM-serververificatie**. Voeg weer elke Hyper-V-host toe die u op de lijst wilt detecteren, met **wsman/** als voorvoegsel.

## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak vanaf het apparaat verbinding met Hyper-V-hosts of -clusters en start de VM-detectie.

1. In **Stap 1: Geef referenties voor de Hyper-V-host op**, klik op **Referenties toevoegen** om een beschrijvende naam voor de referenties op te geven, voeg een **gebruikersnaam** en een **wachtwoord** toe voor een Hyper-V-host/-cluster die door het apparaat wordt gebruikt voor het detecteren van VM's. Klik op **Opslaan**.
1. Als u meerdere referenties tegelijk wilt toevoegen, klikt u op **Meer toevoegen** om meer referenties op te slaan en toe te voegen. Er worden meerdere referenties ondersteund voor detectie van virtuele Hyper-V-machines.
1. In **Stap 2: Geef de gegevens voor de Hyper-V-host of het Hyper-V-cluster op**, klik op **Detectiebron toevoegen** om het **IP-adres of de FQDN** van de Hyper-V-host of het Hyper-V-cluster op te geven en tevens de beschrijvende naam voor de referenties waarmee verbinding wordt gemaakt met de host of het cluster.
1. U kunt **één item per keer toevoegen** of **meerdere items in één keer toevoegen**. Er is ook een optie om de gegevens van een Hyper-V-host/-cluster op te geven via **CSV importeren**.

    ![Selecties voor het toevoegen van de detectiebron](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Als u kiest voor **Eén item toevoegen**, moet u een beschrijvende naam opgeven voor referenties en tevens het **IP-adres of de FQDN** van de Hyper-V-host of het -cluster. Klik vervolgens op **Opslaan**.
    - Als u kiest voor **Meerdere items toevoegen** _(standaard geselecteerd)_ , kunt u meerdere records tegelijk toevoegen door het **IP-adres of de FQDN** van de Hyper-V-host of het -cluster met de beschrijvende naam voor referenties in het tekstvak op te geven. **Controleer** de toegevoegde records en klik op **Opslaan**.
    - Als u **CSV importeren** kiest, kunt u een CSV-sjabloonbestand downloaden, het bestand vullen met het **IP-adres of de FQDN** van de Hyper-V-host of het -cluster, en een beschrijvende naam voor referenties. Vervolgens importeert u het bestand in het apparaat, **controleert u** de records in het bestand en klikt u op **Opslaan**.

1. Wanneer u op Opslaan klikt, wordt de verbinding met de toegevoegde Hyper-V-hosts/-clusters gevalideerd en wordt de **validatiestatus** in de tabel voor elke host of elk cluster weergegeven.
    - Voor gevalideerde hosts/clusters kunt u meer details weergeven door op het IP-adres of de FQDN te klikken.
    - Als de validatie voor een host mislukt, bekijkt u de fout door in de kolom Status van de tabel op **Validatie mislukt** te klikken. Los het probleem op en valideer opnieuw.
    - Als u hosts of clusters wilt verwijderen, klikt u op **Verwijderen**.
    - U kunt een specifieke host niet verwijderen uit een cluster. U kunt alleen het hele cluster verwijderen.
    - U kunt een cluster toevoegen, zelfs als er problemen zijn met specifieke hosts in het cluster.
1. Voordat u de detectie start, kunt u de connectiviteit van hosts of clusters altijd **opnieuw valideren**.
1. Klik op **Detectie starten** om met VM-detectie vanaf de gevalideerde hosts/clusters te beginnen. Nadat de detectie is gestart, kunt u de detectiestatus controleren voor elke host of elk cluster in de tabel.

De detectie wordt gestart. Het duurt ongeveer twee minuten per host voordat de metagegevens van de gedetecteerde servers worden weergegeven in de Azure-portal.

## <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Nadat de detectie is voltooid, kunt u controleren of de VM's worden weergegeven in de portal.

1. Open het Azure Migrate-dashboard.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** op het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.


## <a name="next-steps"></a>Volgende stappen

Probeer de [Hyper-V-evaluatie](tutorial-assess-hyper-v.md) uit met Azure migrate server-evaluatie.
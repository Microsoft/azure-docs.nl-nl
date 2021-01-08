---
title: Virtuele Hyper-V-machines detecteren met Azure Migrate-serverevaluatie
description: Meer informatie over het detecteren van on-premises virtuele Hyper-V-machines met het Azure Migrate-hulpprogramma voor serverevaluatie.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: e7b4a1b2e1d737dad0054cbdf08443436ac2c181
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705554"
---
# <a name="tutorial-discover-hyper-v-vms-with-server-assessment"></a>Zelfstudie: Virtuele Hyper-V-machines detecteren met Azure Migrate-serverevaluatie

Als onderdeel van de migratie naar Azure, detecteert u uw on-premises inventaris en werkbelastingen. 

In deze zelfstudie leert u meer over het detecteren van virtuele Hyper-V-machines met Azure Migrate- Serverevaluatie en een licht Azure Migrate-apparaat. U implementeert het apparaat als een virtuele Hyper-V-machine, zodat de metagegevens van de computer en de prestaties continu worden gedetecteerd.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure-account instellen
> * De Hyper-V-omgeving voorbereiden voor detectie.
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
**Hyper-V-host** | Hyper-V-hosts waarop virtuele machine zich bevinden kunnen zelfstandig zijn of deel uitmaken van een cluster.<br/><br/> Op deze host moet Windows Server 2019 R2, Windows Server 2016 of Windows Server 2012 R2 worden uitgevoerd.<br/><br/> Controleer of binnenkomende verbindingen zijn toegestaan op WinRM-poort 5985 (HTTP), zodat het apparaat verbinding kan maken om metagegevens en prestatiegegevens van de virtuele machine te verzamelen tijdens een Common Information Model-sessie (CIM).
**Implementatie van het apparaat** | Hyper-V-host heeft resources nodig om een virtuele machine toe te wijzen voor het apparaat:<br/><br/> - Windows Server 2016<br/><br/> \- 16 GB aan RAM-geheugen<br/><br/> - Acht vCPU’s<br/><br/> - Ongeveer 80 GB aan schijfruimte.<br/><br/> - Een externe virtuele switch.<br/><br/> - Internettoegang voor de virtuele machine, rechtstreeks of via een proxy.
**VM's** | Op virtuele machines kan elk Windows- of Linux-besturingssysteem worden uitgevoerd. 

Voordat u begint, kunt u [de gegevens controleren](migrate-appliance.md#collected-data---hyper-v) die door het apparaat worden verzameld tijdens de detectie.

## <a name="prepare-an-azure-user-account"></a>Een Azure-gebruikersaccount voorbereiden

Om een Azure Migrate-project te maken en het Azure Migrate-apparaat te registreren, hebt u een account nodig met:
- Machtigingen op inzender- of eigenaarniveau voor een Azure-abonnement.
- Machtigingen verlenen om Azure Active Directory-apps te registreren.

Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement. Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de volgende machtigingen toe te wijzen:


1. Zoek in de Azure Portal naar 'Abonnementen' en selecteer onder **Services** **Abonnementen**.

    ![Zoekvak om te zoeken naar het Azure-abonnement](./media/tutorial-discover-hyper-v/search-subscription.png)

2. Selecteer op de pagina **Abonnementen** het abonnement waarin u een Azure Migrate-project wilt maken. 
3. Selecteer onder het abonnement de optie **Toegangsbeheer (IAM)**  > **Toegang controleren**.
4. Zoek onder **Toegang controleren** naar het relevante gebruikersaccount.
5. Klik onder **Een roltoewijzing toevoegen** op **Toevoegen**.

    ![Een gebruikersaccount zoeken om toegang te controleren en een rol toe te wijzen](./media/tutorial-discover-hyper-v/azure-account-access.png)

6. Selecteer onder **Roltoewijzing toevoegen** de rol Inzender of Eigenaar en selecteer account (azmigrateuser in ons voorbeeld). Klik vervolgens op **Opslaan**.

    ![Hiermee opent u de pagina Roltoewijzing toevoegen om een rol aan het account toe te wijzen](./media/tutorial-discover-hyper-v/assign-role.png)

7. Zoek in de portal naar gebruikers en selecteer onder **Services** **Gebruikers**.
8. Controleer onder **Gebruikersinstellingen** of Azure AD-gebruikers toepassingen kunnen registreren (standaard ingesteld op **Ja**).

    ![Verifiëren onder Gebruikersinstellingen of gebruikers Active Directory-apps kunnen registreren](./media/tutorial-discover-hyper-v/register-apps.png)

9. Als alternatief kan een tenant/globale beheerder de rol van **toepassingsontwikkelaar** toewijzen aan het account om de registratie van AAD-apps mogelijk te maken. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="prepare-hyper-v-hosts"></a>Hyper-V-hosts voorbereiden

Stel een account in met beheerderstoegang op de Hyper-V-hosts. Het apparaat gebruikt dit account voor detectie.

- Optie 1: Maak een account met beheerderstoegang op de Hyper-V-hostmachine.
- Optie 2: Bereid een lokaal beheerdersaccount of een domeinbeheerdersaccount voor en voeg het account toe aan deze groepen: Gebruikers van extern beheer, Hyper-V-beheerders, en Prestatiemetergebruikers.


## <a name="set-up-a-project"></a>Een project instellen

Stel een nieuw Azure Migrate-project in.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer onder **Overzicht** de optie **Project maken**.
5. Selecteer onder **Project maken** uw Azure-abonnement en resourcegroep. Maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Vakken voor projectnaam en regio](./media/tutorial-discover-hyper-v/new-project.png)

7. Selecteer **Maken**.
8. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd.

Het hulpprogramma **Azure Migrate: Serverevaluatie** wordt standaard toegevoegd aan het nieuwe project.

![Pagina waarop wordt weergegeven dat het hulpprogramma Serverevaluatie standaard wordt toegevoegd](./media/tutorial-discover-hyper-v/added-tool.png)


## <a name="set-up-the-appliance"></a>Het apparaat instellen

In deze zelfstudie wordt het apparaat op een virtuele Hyper-V-machine als volgt ingesteld:

- Geef een naam op voor het apparaat en genereer een Azure Migrate-projectsleutel in de portal.
- Download een gecomprimeerde Hyper-V VHD vanuit de Azure Portal.
- Maak het apparaat en controleer of het verbinding kan maken met Azure Migrate-serverevaluatie.
- Configureer het apparaat voor het eerst en registreer het bij het Azure Migrate-project met behulp van de Azure Migrate-projectsleutel.
> [!NOTE]
> Als u het apparaat om de een of andere reden niet kunt instellen met behulp van een sjabloon, kunt u het instellen met behulp van een PowerShell-script. [Meer informatie](deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).


### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate-projectsleutel genereren

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met Hyper-V**.
3. In **1: Azure Migrate-projectsleutel genereren** geeft u een naam op voor het Azure Migrate-apparaat dat u voor de detectie van Hyper-V-VM's wilt instellen. De naam moet alfanumeriek zijn en 14 tekens of minder bevatten.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina Computers detecteren niet tijdens het maken van resources.
1. Nadat de Azure-resources zijn gemaakt, wordt er een **Azure Migrate-projectsleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="download-the-vhd"></a>De VHD downloaden

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
        Hyper-V (8,91 GB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140422) |  40aa037987771794428b1c6ebee2614b092e6d69ac56d48a2bbc75eeef86c99a

    - Voor Azure Government:

        **Scenario** _ | _ *Downloaden** | **SHA256**
        --- | --- | ---
        Hyper-V (85,8 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140424) |  cfed44bb52c9ab3024a628dc7a5d0df8c624f156ec1ecc3507116bae330b257f

### <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak de virtuele machine.

1. Pak het gecomprimeerde VHD-bestand uit naar een map op de Hyper-V-host die als host moet fungeren voor de virtuele machine. Er worden drie mappen uitgepakt.
2. Open Hyper-V-beheer. Klik in **Acties** op **Virtuele machine importeren**.
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
1. U hebt een apparaatcode nodig om te verifiëren bij Azure. Als u klikt op **Aanmelden**, wordt er een modaal met de apparaatcode geopend, zoals hieronder weergegeven.

    ![Modaal waarin de apparaatcode wordt weergegeven](./media/tutorial-discover-vmware/device-code.png)

1. Klik op **Code kopiëren en aanmelden** om de apparaatcode te kopiëren en een Azure-aanmeldingsprompt te openen op een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Plak de apparaatcode in het nieuwe tabblad en meld u aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Als u het aanmeldingstabblad per ongeluk sluit zonder u aan te melden, vernieuwt u het browsertabblad van Apparaatconfiguratiebeheer om de knop Aanmelden opnieuw in te schakelen.
1. Als u bent aangemeld, gaat u terug naar het vorige tabblad in Apparaatconfiguratiebeheer.
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste machtigingen  heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
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

- [Virtuele Hyper-V-machines](tutorial-assess-hyper-v.md) evalueren voor migratie naar Azure.
- [De gegevens controleren ](migrate-appliance.md#collected-data---hyper-v) die door het apparaat worden verzameld tijdens de detectie.
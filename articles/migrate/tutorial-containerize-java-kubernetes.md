---
title: Azure-app container opslag java; Container opslag en migratie van Java-webtoepassingen naar Azure Kubernetes.
description: 'Zelf studie: container plaatsen & de migratie van Java-webtoepassingen naar de Azure Kubernetes-service.'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 3/2/2021
ms.author: rahugup
ms.openlocfilehash: ea7cdfbd30cf698cecbb14a1d70916764ad3247a
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023109"
---
# <a name="java-web-app-containerization-and-migration-to-azure-kubernetes-service"></a>Container opslag en migratie van Java-Web-app naar Azure Kubernetes service

In dit artikel leert u hoe u Java-webtoepassingen kunt container plaatsen (uitgevoerd op Apache Tomcat) en deze kunt migreren naar [Azure Kubernetes service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) met behulp van de Azure migrate: app container opslag tool. Het container opslag-proces vereist geen toegang tot uw code basis en biedt een eenvoudige manier om bestaande toepassingen te container plaatsen. Het hulp programma werkt met behulp van de actieve status van de toepassingen op een server om de toepassings onderdelen te bepalen en helpt u deze te verpakken in een container installatie kopie. De container toepassing kan vervolgens worden geïmplementeerd in azure Kubernetes service (AKS).

Het Azure Migrate: het hulp programma app container opslag wordt momenteel ondersteund.

- Waarmee ASP.NET Apps en implementeer ze op Windows-containers op AKS. [Meer informatie](./tutorial-containerize-aspnet-kubernetes.md)
- Waarmee Java Web Apps op Apache Tomcat (op Linux-servers) en implementeer ze op Linux-containers op AKS.


Met de Azure Migrate: het container opslag-hulp programma voor apps kunt u

- **Uw toepassing detecteren**: het hulp programma maakt op afstand verbinding met de toepassings servers waarop uw Java-webtoepassing wordt uitgevoerd (op Apache Tomcat) en detecteert de toepassings onderdelen. Het hulp programma maakt een Dockerfile die kan worden gebruikt om een container installatie kopie voor de toepassing te maken.
- **De container installatie kopie bouwen**: u kunt de Dockerfile inspecteren en verder aanpassen conform uw toepassings vereisten en gebruiken om uw installatie kopie van de toepassings container samen te stellen. De installatie kopie van de toepassings container wordt gepusht naar een Azure Container Registry die u opgeeft.
- **Implementeren naar Azure Kubernetes service**: het hulp programma genereert vervolgens de YAML bestanden van de Kubernetes-resource definitie die nodig zijn om de container toepassing te implementeren in uw Azure Kubernetes service-cluster. U kunt de YAML-bestanden aanpassen en gebruiken om de toepassing te implementeren op AKS.

> [!NOTE]
> Met de Azure Migrate: app container opslag tool kunt u specifieke toepassings typen (ASP.NET en Java Web apps op Apache Tomcat) en de bijbehorende onderdelen op een toepassings server detecteren. Als u servers en de inventaris van apps, rollen en functies die worden uitgevoerd op on-premises machines wilt detecteren, gebruikt u Azure Migrate: detectie-en beoordelings mogelijkheden. [Meer informatie](./tutorial-discover-vmware.md)

Hoewel alle toepassingen niet profiteren van een rechte verschuiving naar containers zonder een belang rijke Rearchitectuur, zijn enkele van de voor delen van het verplaatsen van bestaande apps naar containers zonder dat u deze hoeft te herschrijven:

- **Verbeterd infrastructuur gebruik:** Met containers kunnen meerdere toepassingen resources delen en worden gehost op dezelfde infra structuur. Dit kan helpen om de infra structuur te consolideren en het gebruik te verbeteren.
- **Vereenvoudigd beheer:** Door uw toepassingen te hosten op een moderne, beheerde infrastructuur platform zoals AKS, kunt u uw beheer procedures vereenvoudigen, terwijl u de controle over uw infra structuur blijft behouden. U kunt dit doen door het onderhoud en de beheer processen van de infra structuur te verminderen die u normaal gesp roken wilt uitvoeren met de infra structuur in eigendom.
- **Draag baarheid van toepassingen:** Dankzij een verbeterde acceptatie en standaardisering van de indeling van container specificaties en Orchestration-platformen is de draag baarheid van de toepassing niet meer een probleem.
- **Modern beheer met DevOps:** Helpt u bij het aannemen en standaardiseren van moderne prak tijken voor beheer en beveiliging met infra structuur als code en overgang naar DevOps.


In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Azure-account instellen.
> * Installeer de Azure Migrate: app container opslag tool.
> * Ontdek uw Java-webtoepassing.
> * Bouw de container installatie kopie.
> * Implementeer de container toepassing op AKS.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven.

## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

**Vereiste** | **Details**
--- | ---
**Een computer identificeren om het hulp programma te installeren** | Een Windows-computer voor het installeren en uitvoeren van de Azure Migrate: app container opslag-hulp programma. De Windows-computer kan een server (Windows Server 2016 of hoger) of client (Windows 10) besturings systeem zijn, wat betekent dat het hulp programma ook op uw bureau blad kan worden uitgevoerd. <br/><br/> De Windows-computer waarop het hulpprogramma wordt uitgevoerd, moet een netwerkverbinding hebben met de servers/virtuele machines waarop de ASP.NET-toepassingen worden gehost die in containers moeten worden opgeslagen.<br/><br/> Zorg ervoor dat er 6 GB ruimte beschikbaar is op de Windows-computer waarop het Azure Migrate: app container opslag-hulp programma voor het opslaan van toepassings artefacten. <br/><br/> De Windows-computer moet toegang hebben tot internet, rechtstreeks of via een proxy. <br/> <br/>Installeer het hulp programma micro soft Web Deploy op de computer waarop het hulp programma voor de app container opslag helper en de toepassings server worden uitgevoerd als dat nog niet is gebeurd. U kunt het hulp programma [hier](https://aka.ms/webdeploy3.6) downloaden
**Toepassingsservers** | -Schakel de SSH-verbinding (Secure Shell) in op poort 22 op de server (s) waarop de Java-toepassing (en) worden uitgevoerd. <br/>
**Java-webtoepassing** | Het hulp programma ondersteunt momenteel <br/><br/> -Toepassingen die worden uitgevoerd op Tomcat 8 of hoger.<br/> -Toepassings servers op Ubuntu Linux 16.04/18.04/20.04, Debian 7/8, CentOS 6/7, Red Hat Enterprise Linux 5/6/7. <br/> -Toepassingen die gebruikmaken van Java versie 7 of hoger.  <br/><br/> Het hulp programma biedt momenteel geen ondersteuning voor <br/><br/> -Toepassings servers waarop meerdere Tomcat-instanties worden uitgevoerd <br/>  


## <a name="prepare-an-azure-user-account"></a>Een Azure-gebruikersaccount voorbereiden

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

Als uw abonnement eenmaal is ingesteld, hebt u een Azure-gebruikers account nodig met:
- Eigenaars machtigingen voor het Azure-abonnement
- Machtigingen voor het registreren van Azure Active Directory-apps

Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement. Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de volgende machtigingen toe te wijzen:

1. Zoek in de Azure Portal naar 'Abonnementen' en selecteer onder **Services** **Abonnementen**.

    ![Zoekvak om te zoeken naar het Azure-abonnement.](./media/tutorial-discover-vmware/search-subscription.png)

2. Selecteer op de pagina **Abonnementen** het abonnement waarin u een Azure Migrate-project wilt maken.
3. Selecteer onder het abonnement de optie **Toegangsbeheer (IAM)**  > **Toegang controleren**.
4. Zoek onder **Toegang controleren** naar het relevante gebruikersaccount.
5. Klik onder **Een roltoewijzing toevoegen** op **Toevoegen**.

    ![Zoek een gebruikers account om de toegang te controleren en een rol toe te wijzen.](./media/tutorial-discover-vmware/azure-account-access.png)

6. In **roltoewijzing toevoegen** selecteert u de rol eigenaar en selecteert u het account (azmigrateuser in ons voor beeld). Klik vervolgens op **Opslaan**.

    ![Hiermee opent u de pagina functie toewijzing toevoegen om een rol aan het account toe te wijzen.](./media/tutorial-discover-vmware/assign-role.png)

7. Uw Azure-account heeft ook **machtigingen nodig om Azure Active Directory-apps te registreren.**
8.  Ga in azure Portal naar **Azure Active Directory**  >  **gebruikers**  >  **gebruikers instellingen**.
9.  Controleer onder **Gebruikersinstellingen** of Azure AD-gebruikers toepassingen kunnen registreren (standaard ingesteld op **Ja**).

      ![Controleer in gebruikers instellingen of gebruikers Active Directory-apps kunnen registreren.](./media/tutorial-discover-vmware/register-apps.png)

10.  Als de App-registraties-instellingen is ingesteld op Nee, vraagt u de Tenant/globale beheerder om de vereiste machtiging toe te wijzen. De Tenant/globale beheerder kan de rol van **toepassings ontwikkelaar** ook toewijzen aan een account om de registratie van Azure Active Directory-app toe te staan. [Meer informatie](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Down load en installeer Azure Migrate: app container opslag tool

1. [Down load](https://go.microsoft.com/fwlink/?linkid=2134571) de Azure migrate: app container opslag Installer op een Windows-computer.
2. Start Power shell in de Administrator-modus en wijzig de Power shell-map in de map met het installatie programma.
3. Voer het installatie script uit met behulp van de opdracht

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Het app container opslag-hulp programma starten

1. Open een browser op een computer die verbinding kan maken met de Windows-computer waarop het app container opslag-hulp programma wordt uitgevoerd en open de hulp programma-URL: **https://*-computer naam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureau blad door de snelkoppeling naar de app te selecteren.

2. Als er een waarschuwing wordt weer gegeven met de melding dat uw verbinding niet privé is, klikt u op Geavanceerd en kiest u om door te gaan naar de website. Deze waarschuwing wordt weer gegeven als de web-interface een zelfondertekend TLS/SSL-certificaat gebruikt.
3. Gebruik op het aanmeldings scherm het lokale Administrator-account op de computer om u aan te melden.
4. Selecteer voor type toepassing opgeven **Java-Web-apps op Tomcat** als het type toepassing dat u wilt container plaatsen.

    ![Standaard belasting voor het container opslag-hulp programma voor apps.](./media/tutorial-containerize-apps-aks/tool-home.png)


### <a name="complete-tool-pre-requisites"></a>Vereisten voor het hulp programma volt ooien
1. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
6. Voer de volgende stappen uit in de Web-App van het hulp programma > vereisten in te **stellen**:
   - **Connectiviteit**: het hulp programma controleert of de Windows-computer toegang heeft tot internet. Als de computer een proxy gebruikt:
     - Klik op **proxy instellen** om het proxy adres op te geven (in het formulier-IP-adres of de FQDN) en de luister poort.
     - Geef referenties op als de proxy verificatie nodig heeft.
     - Alleen HTTP-proxy wordt ondersteund.
     - Als u proxy Details hebt toegevoegd of de proxy en/of verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteits controle opnieuw te activeren.
   - **Updates installeren**: het hulp programma controleert automatisch op de meest recente updates en installeert deze. U kunt de meest recente [versie van het](https://go.microsoft.com/fwlink/?linkid=2134571)hulp programma ook hand matig installeren.
   - **Enable Secure Shell (SSH)**: het hulp programma waarschuwt u dat Secure Shell (SSH) is ingeschakeld op de toepassings servers waarop de Java-webtoepassingen worden uitgevoerd.


## <a name="login-to-azure"></a>Aanmelden bij Azure

Klik op **Aanmelden** om u aan te melden bij uw Azure-account.

1. U hebt een apparaatcode nodig om te verifiëren bij Azure. Als u op aanmelden klikt, wordt er een modale met de apparaatcode geopend.
2. Klik op **Code kopiëren en aanmelden** om de apparaatcode te kopiëren en een Azure-aanmeldingsprompt te openen op een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.

    ![Modale weer gegeven apparaatcode.](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Plak op het tabblad Nieuw de code van het apparaat en voltooi de aanmelding met uw Azure-account referenties. U kunt het tabblad browser sluiten nadat het aanmelden is voltooid en terug naar de web-interface van het app container opslag-hulp programma.
4. Selecteer de **Azure-Tenant** die u wilt gebruiken.
5. Geef het **Azure-abonnement** op dat u wilt gebruiken.

## <a name="discover-java-web-applications"></a>Java-webtoepassingen detecteren

Het hulp programma app container opslag helper maakt op afstand verbinding met de toepassings servers met behulp van de referenties en probeert Java-webtoepassingen te detecteren (uitgevoerd op Apache Tomcat) die op de toepassings servers worden gehost.

1. Geef het **IP-adres/de FQDN en de referenties** op van de server waarop de Java-webtoepassing wordt uitgevoerd die moet worden gebruikt om extern verbinding te maken met de server voor toepassings detectie.
    - De gegeven referenties moeten voor een hoofd account (Linux) op de toepassings server zijn.
    - Voor domein accounts (de gebruiker moet een beheerder zijn op de toepassings server), voor voegsel van de gebruikers naam met de domein naam in de notatie *<domein\gebruikersnaam>*.
    - U kunt toepassings detectie uitvoeren voor Maxi maal vijf servers tegelijk.

2. Klik op **valideren** om te controleren of de toepassings server bereikbaar is vanaf de computer waarop het hulp programma wordt uitgevoerd en of de referenties geldig zijn. Wanneer de validatie is geslaagd, wordt in de kolom Status de status weer gegeven als **toegewezen**.  

    ![Scherm opname voor Server-IP en referenties.](./media/tutorial-containerize-apps-aks/discovery-credentials.png)

3. Klik op **door gaan** om de detectie van toepassingen op de geselecteerde toepassings servers te starten.

4. Wanneer toepassings detectie is voltooid, kunt u de lijst met toepassingen selecteren om te container plaatsen.

    ![Scherm afbeelding voor gedetecteerde Java-webtoepassing.](./media/tutorial-containerize-apps-aks/discovered-app.png)


4. Gebruik het selectie vakje om de toepassingen te selecteren die u wilt container plaatsen.
5. **Container naam opgeven**: Geef een naam op voor de doel container voor elke geselecteerde toepassing. De container naam moet worden opgegeven als <*name: tag*> waar de tag wordt gebruikt voor container installatie kopie. U kunt bijvoorbeeld de naam van de doel container opgeven als *AppName: v1*.   

### <a name="parameterize-application-configurations"></a>Para meters toepassings configuraties
Parameterizing de configuratie is beschikbaar als para meter voor implementatie tijd. Hierdoor kunt u deze instelling configureren tijdens het implementeren van de toepassing in plaats van een specifieke waarde in de container installatie kopie. Deze optie is bijvoorbeeld handig voor para meters zoals database verbindings reeksen.
1. Klik op **app-configuraties** om gedetecteerde configuraties te controleren.
2. Schakel het selectie vakje in om de gedetecteerde toepassings configuraties te para meters.
3. Klik op **Toep assen** nadat u de configuraties hebt geselecteerd in para meters.

   ![Scherm afbeelding voor app-configuratie parameterisering ASP.NET-toepassing.](./media/tutorial-containerize-apps-aks/discovered-app-configs.png)

### <a name="externalize-file-system-dependencies"></a>Afhankelijkheden van Externalize-bestands systeem

 U kunt andere mappen toevoegen die door uw toepassing worden gebruikt. Geef op of ze moeten deel uitmaken van de container installatie kopie of moeten worden extern via permanente volumes op een Azure-bestands share. Het gebruik van permanente volumes werkt prima voor stateful toepassingen die de status buiten de container opslaan of andere statische inhoud die is opgeslagen op het bestands systeem. [Meer informatie](https://docs.microsoft.com/azure/aks/concepts-storage)

1. Klik op **bewerken** onder app-mappen om de gedetecteerde toepassings mappen te controleren. De gedetecteerde toepassings mappen zijn geïdentificeerd als verplichte artefacten die nodig zijn voor de toepassing en worden gekopieerd naar de container installatie kopie.

2. Klik op **mappen toevoegen** en geef de mappaden op die u wilt toevoegen.
3. Als u meerdere mappen aan hetzelfde volume wilt toevoegen, geeft u een door komma's ( `,` ) gescheiden waarden op.
4. Selecteer **permanent volume** als de opslag optie als u wilt dat de mappen buiten de container worden opgeslagen op een permanent volume.
5. Klik op **Opslaan** nadat u de mappen van de toepassing hebt bekeken.
   ![Scherm opname voor app-volume opslag selectie.](./media/tutorial-containerize-apps-aks/discovered-app-volumes.png)

6. Klik op **door gaan** om door te gaan naar de build-fase van de container installatie kopie.


## <a name="build-container-image"></a>Containerinstallatiekopie maken


1. **Selecteer Azure container Registry**: gebruik de vervolg keuzelijst om een [Azure container Registry](https://docs.microsoft.com/azure/container-registry/) te selecteren dat wordt gebruikt om de container installatie kopieën voor de apps te maken en op te slaan. U kunt een bestaande Azure Container Registry gebruiken of ervoor kiezen om een nieuwe te maken met behulp van de optie nieuwe REGI ster maken.

    ![Scherm afbeelding voor de selectie van de app-ACR.](./media/tutorial-containerize-apps-aks/build-java-app.png)


2. **Bekijk de Dockerfile**: de Dockerfile die nodig zijn voor het bouwen van de container installatie kopieën voor elke geselecteerde toepassing, worden gegenereerd aan het begin van de build-stap. Klik op **controleren** om de Dockerfile te controleren. U kunt ook de nodige aanpassingen toevoegen aan de Dockerfile in de stap beoordeling en de wijzigingen opslaan voordat u het bouw proces start.

3. **Activerings proces**: Selecteer de toepassingen waarvoor u installatie kopieën wilt maken en klik op **bouwen**. Als u op Build klikt, wordt de build van de container installatie kopie gestart voor elke toepassing. Het hulp programma controleert de status van de build continu en laat u door gaan met de volgende stap wanneer de build is voltooid.

4. **Status van build bijhouden**: u kunt ook de voortgang van de build-stap bewaken door te klikken op de koppeling **Build wordt uitgevoerd** onder de kolom Status. Het duurt enkele minuten voordat de koppeling actief is nadat u het bouw proces hebt geactiveerd.  

5. Zodra de build is voltooid, klikt u op **door gaan** om implementatie-instellingen op te geven.

    ![Scherm afbeelding voor het volt ooien van de build van de app-container installatie kopie](./media/tutorial-containerize-apps-aks/build-java-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>De in de container geplaatste app implementeren op AKS

Zodra de container installatie kopie is gemaakt, is de volgende stap het implementeren van de toepassing als een container in [Azure Kubernetes service (AKS)](https://azure.microsoft.com/services/kubernetes-service/).

1. **Selecteer het Azure Kubernetes-service cluster**: Geef het AKS-cluster op waarop de toepassing moet worden geïmplementeerd.

     - Het geselecteerde AKS-cluster moet een Linux-knooppunt groep hebben.
     - Het cluster moet zo worden geconfigureerd dat het ophalen van installatie kopieën van de Azure Container Registry die is geselecteerd voor het opslaan van de installatie kopieën.
         - Voer de volgende opdracht in azure CLI uit om het AKS-cluster aan de ACR te koppelen.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Als u geen AKS-cluster hebt of als u een nieuw AKS-cluster wilt maken om de toepassing te implementeren, kunt u op basis van het hulp programma kiezen door te klikken op **Nieuw AKS-cluster maken**.      
          - Het AKS-cluster dat is gemaakt met het hulp programma, wordt gemaakt met een Linux-knooppunt groep. Het cluster wordt zodanig geconfigureerd dat het installatie kopieën kan ophalen uit het Azure Container Registry dat eerder is gemaakt (als u een nieuwe register optie maken hebt gekozen).
     - Klik op **door gaan** nadat u het AKS-cluster hebt geselecteerd.

2. **Azure-bestands share opgeven**: als u meer mappen hebt toegevoegd en de optie permanent volume hebt geselecteerd, geeft u de Azure-bestands share op die moet worden gebruikt door Azure migrate: app container opslag tool tijdens het implementatie proces. Het hulp programma maakt nieuwe mappen in deze Azure-bestands share om te kopiëren over de toepassings mappen die zijn geconfigureerd voor permanente volume opslag. Zodra de implementatie van de toepassing is voltooid, wordt de Azure-bestands share door het hulp programma opgeschoond door de mappen die zijn gemaakt, te verwijderen.

     - Als u geen Azure-bestands share hebt of een nieuwe Azure-bestands share wilt maken, kunt u ervoor kiezen om vanuit het hulp programma te maken door te klikken op **Nieuw opslag account en bestands share maken**.  

3. **Configuratie van toepassings implementatie**: Zodra u de bovenstaande stappen hebt uitgevoerd, moet u de implementatie configuratie voor de toepassing opgeven. Klik op **configureren** om de implementatie van de toepassing aan te passen. In de stap configureren kunt u de volgende aanpassingen opgeven:
     - **Voorvoegsel teken reeks**: Geef een voorvoegsel teken reeks op die moet worden gebruikt in de naam van alle resources die zijn gemaakt voor de container toepassing in het AKS-cluster.
     - **Replica sets**: Geef het aantal toepassings exemplaren (peul) op dat in de containers moet worden uitgevoerd.
     - **Type Load Balancer**: Selecteer *extern* als de container toepassing bereikbaar moet zijn vanuit open bare netwerken.
     - **Toepassings configuratie**: Geef voor alle toepassings configuraties die para meters zijn, de waarden op die moeten worden gebruikt voor de huidige implementatie.
     - **Opslag**: voor alle toepassings mappen die zijn geconfigureerd voor permanente volume opslag, geeft u op of het volume moet worden gedeeld door instanties van de toepassing of moet afzonderlijk worden geïnitialiseerd met elk exemplaar in de container. Standaard worden alle toepassings mappen op permanente volumes geconfigureerd als gedeeld.
     - Klik op **Toep assen** om de implementatie configuratie op te slaan.
     - Klik op **door gaan** om de toepassing te implementeren.

    ![Scherm opname van de configuratie van de implementatie-app.](./media/tutorial-containerize-apps-aks/deploy-java-app-config.png)

4. **De toepassing implementeren**: zodra de implementatie configuratie voor de toepassing is opgeslagen, genereert het hulp programma de yaml voor Kubernetes-implementatie voor de toepassing.
     - Klik op **bewerken** om de Kubernetes-implementatie yaml voor de toepassingen te bekijken en aan te passen.
     - Selecteer de toepassing die u wilt implementeren.
     - Klik op **implementeren** om de implementaties voor de geselecteerde toepassingen te starten

         ![Scherm opname van configuratie van de app-implementatie.](./media/tutorial-containerize-apps-aks/deploy-java-app-deploy.png)

     - Zodra de toepassing is geïmplementeerd, kunt u op de kolom *Implementatie status* klikken om de resources bij te houden die voor de toepassing zijn geïmplementeerd.

## <a name="download-generated-artifacts"></a>Gegenereerde artefacten downloaden

Alle artefacten die worden gebruikt voor het bouwen en implementeren van de toepassing in AKS, met inbegrip van de Dockerfile-en Kubernetes YAML-specificatie bestanden, worden opgeslagen op de computer waarop het hulp programma wordt uitgevoerd. De artefacten bevinden zich op *C:\ProgramData\Microsoft Azure migrate app container opslag*.

Voor elke toepassings server wordt één map gemaakt. U kunt alle tussenliggende artefacten weer geven en downloaden die in het container opslag-proces worden gebruikt door naar deze map te navigeren. De map die overeenkomt met de toepassings server wordt aan het begin van elke uitvoering van het hulp programma voor een bepaalde server opgeruimd.

## <a name="troubleshoot-issues"></a>Problemen oplossen

Als u problemen met het hulp programma wilt oplossen, kunt u de logboek bestanden bekijken op de Windows-computer waarop het container opslag-hulp programma van de app wordt uitgevoerd. Logboek bestanden voor hulpprogram ma's bevinden zich in *C:\ProgramData\Microsoft Azure migrate app Containerization\Logs* -map.

## <a name="next-steps"></a>Volgende stappen

- Waarmee ASP.NET Apps en implementeer ze op Windows-containers op AKS. [Meer informatie](./tutorial-containerize-aspnet-kubernetes.md)

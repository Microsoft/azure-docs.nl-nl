---
title: Azure Service Fabric-invoeg toepassing voor eclips
description: Meer informatie over het aan de slag gaan met Azure Service Fabric in Java met behulp van eclips en de Service Fabric meegeleverde invoeg toepassing.
author: rapatchi
ms.topic: conceptual
ms.date: 04/06/2018
ms.author: rapatchi
ms.custom: devx-track-java
ms.openlocfilehash: c94f43b74edf5da18acd7e67417f3b7e10a07d5d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590865"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Service Fabric-invoegtoepassing voor de ontwikkeling van Eclipse Java-toepassingen
Eclipse is een van de meest gebruikte Integrated Development Environments (IDE's) voor Java-ontwikkelaars. In dit artikel wordt beschreven hoe u een Eclipse-ontwikkelomgeving instelt voor gebruik met Azure Service Fabric. Ontdek hoe u de Service Fabric-invoegtoepassing installeert en een Service Fabric-toepassing implementeert in een lokaal of extern Service Fabric-cluster in Eclipse. 

> [!NOTE]
> De Eclipse-invoegtoepassing wordt momenteel niet ondersteund in Windows. 

> [!IMPORTANT]
> Zorg ervoor dat JDK 8 is geïnstalleerd op het systeem en is geselecteerd in eclips.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>De Service Fabric-invoegtoepassing installeren of bijwerken in Eclipse
U kunt een Service Fabric-invoegtoepassing in Eclipse installeren. De invoegtoepassing vereenvoudigt het proces voor het maken en implementeren van Java-services.

> [!IMPORTANT]
> Voor de Service Fabric-invoegtoepassing is Eclipse Neon of een hogere versie vereist. Zie de instructies na deze opmerking voor het controleren van uw Eclipse-versie. Als er een oudere versie van Eclipse is geïnstalleerd, kunt u een nieuwere versie downloaden van de [site van Eclipse](https://www.eclipse.org). Het wordt afgeraden een oudere versie van Eclipse te overschrijven met een nieuwere versie. Verwijder de oudere versie voordat u het installatieprogramma uitvoert of installeer de nieuwe versie in een andere map. 
> 
> Voor Ubuntu wordt u aangeraden de installatie rechtstreeks vanaf de site van Eclipse uit te voeren en niet door middel van een installatieprogramma voor pakketten (`apt` of `apt-get`). Daardoor weet u zeker dat u de meest recente versie van Eclipse hebt. 

Installeer Eclipse Neon of hoger vanaf de [Eclipse-site](https://www.eclipse.org).  Installeer ook versie 2.2.1 of hoger van Buildship (de Service Fabric-invoegtoepassing is niet compatibel met oudere versies van Buildship):
-   Als u de versies van geïnstalleerde onderdelen in eclips wilt controleren, gaat u naar **Help**  >  **over** de  >  **installatie Details** van de eclips.
-   Zie [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: Eclipse-invoegtoepassingen voor Gradle) als u Buildship wilt bijwerken.
-   Als u updates voor een eclips wilt controleren en installeren, gaat u naar **Help**  >  **controleren op updates**.

Installeer de service Fabric-invoeg toepassing, in eclips, ga naar **hulp** bij de  >  **installatie van nieuwe software**.
1. Voer in het vak **werken met** https: \/ /DL.Microsoft.com/eclipse in.
2. Klik op **Add**.

   ![De Service Fabric-invoegtoepassing voor Eclipse][sf-eclipse-plugin-install]
3. Selecteer de Fabric Service-invoegtoepassing en klik op **Next**.
4. Voer de installatiestappen uit en accepteer de licentievoorwaarden voor Microsoft-software.

Als u de Service Fabric-invoegtoepassing al hebt geïnstalleerd, zorgt u ervoor dat u de meest recente versie hebt geïnstalleerd. 
1. Als u wilt controleren of er updates beschikbaar zijn, gaat u naar **Help**  >  **over** de  >  **installatie Details** van de eclips. 
2. Selecteer Service Fabric in de lijst met geïnstalleerde invoegtoepassingen en klik op **Update**. Beschikbare updates worden geïnstalleerd.
3. Als u de Service Fabric-invoegtoepassing bijwerkt, moet u ook het Gradle-project vernieuwen.  Klik met de rechtermuisknop op **build.gradle** en selecteer vervolgens **Vernieuwen**.

> [!NOTE]
> Als de installatie of update van de Service Fabric-invoegtoepassing traag verloopt, kan dit het gevolg zijn van een instelling in Eclipse. Eclipse verzamelt metagegevens over alle wijzigingen in updatesites die zijn geregistreerd bij uw exemplaar van Eclipse. Als u het proces voor het controleren op en installeren van updates van Service Fabric-invoegtoepassingen wilt versnellen, gaat u naar **Available Software Sites**. Schakel de selectie vakjes uit voor alle sites, behalve voor de site die verwijst naar de locatie van de Service Fabric-invoeg toepassing (https: \/ /DL.Microsoft.com/Eclipse/Azure/servicefabric).

> [!NOTE]
>Als Eclipse op uw Mac niet werkt zoals u verwacht of als u het moet uitvoeren als supergebruiker), gaat u naar de map **ECLIPSE_INSTALLATION_PATH** en vervolgens naar de submap **Eclipse.app/Contents/MacOS**. Start Eclipse door `./eclipse` uit te voeren.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Een Service Fabric-toepassing maken in Eclipse

1.  Ga in eclips naar **bestand**  >  **Nieuw**  >  . Selecteer **Fabric Service Project** en klik op **Next**.

    ![Nieuw Service Fabric-project pagina 1][create-application/p1]

2.  Voer een naam in voor het project en klik op **Next**.

    ![Nieuw Service Fabric-project pagina 2][create-application/p2]

3.  Selecteer **Service Template** in de lijst met sjablonen. Selecteer het type servicesjabloon (Actor, Stateless, Container of Guest Binary) en klik op **Next**.

    ![Nieuw Service Fabric-project pagina 3][create-application/p3]

4.  Voer de servicenaam en servicegegevens in en klik op **Finish**.

    ![Nieuw Service Fabric-project pagina 4][create-application/p4]

5. Wanneer u uw eerste Service Fabric-project maakt, klikt u in het dialoogvenster **Open Associated Perspective** op **Yes**.

    ![Nieuw Service Fabric-project pagina 5][create-application/p5]

6.  Het nieuwe project ziet er als volgt uit:

    ![Nieuw Service Fabric-project pagina 6][create-application/p6]

## <a name="build-a-service-fabric-application-in-eclipse"></a>Een Service Fabric-toepassing bouwen in een eclips

1.  Klik met de rechtermuisknop op de nieuwe Service Fabric-toepassing en selecteer **Service Fabric**.

    ![Snelmenu van Service Fabric][publish/RightClick]

2. Selecteer een van de volgende opties in het context menu:
    -   Klik op **Build Application** als u de toepassing wilt maken zonder op te schonen.
    -   Klik op **Rebuild Application** als u een schone build van de toepassing wilt maken.
    -   Klik op **Clean Application** als u de gebouwde artefacts uit de toepassing wilt verwijderen.

## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Een Service Fabric-toepassing implementeren in het lokale cluster met eclips

Nadat u uw Service Fabric-toepassing hebt gemaakt, volgt u deze stappen om deze te implementeren in het lokale cluster.

1. Als u het lokale cluster nog niet hebt gestart, volgt u de instructies in [een lokaal cluster instellen](./service-fabric-get-started-linux.md#set-up-a-local-cluster) om uw lokale cluster te starten en ervoor te zorgen dat deze wordt uitgevoerd.
2. Klik met de rechter muisknop op uw Service Fabric-toepassing en selecteer vervolgens **service Fabric**.

    ![Snelmenu van Service Fabric][publish/RightClick]

3.  Klik in het context menu op **toepassing implementeren**.
4.  U kunt de voortgang van de implementatie bewerking volgen in het console venster.
5.  Als u wilt controleren of uw toepassing wordt uitgevoerd, opent u Service Fabric Explorer op uw lokale cluster in een browser venster `http://localhost:19080/Explorer` . Vouw het knoop punt **toepassingen** uit en controleer of uw toepassing wordt uitgevoerd. 

Zie [fouten opsporen in een Java-service in eclips](./service-fabric-debugging-your-application-java.md)voor meer informatie over het opsporen van fouten in uw toepassing in een eclips met behulp van het lokale cluster.

U kunt uw toepassing ook implementeren in het lokale cluster met de opdracht **toepassing publiceren** :

1. Klik met de rechter muisknop op uw Service Fabric-toepassing en selecteer vervolgens **service Fabric**.
2. Klik in het context menu op **toepassing publiceren...**.
3. Kies in het venster **toepassing publiceren** de optie **PublishProfiles/Local.js** als doel profiel en klik op **publiceren**.

    ![Het dialoogvenster Publiceren voor Local](./media/service-fabric-get-started-eclipse/localjson.png)

    Standaard is het Local.jsop het publicatie profiel zo ingesteld dat het wordt gepubliceerd naar het lokale cluster. Zie de volgende sectie voor meer informatie over de verbinding en endpoint-para meters die aanwezig zijn in publicatie profielen.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Uw Service Fabric-toepassing publiceren naar Azure met eclips

Voer de volgende stappen uit om uw toepassing te publiceren in de Cloud:

1. Als u uw toepassing wilt publiceren naar een beveiligd cluster in de Cloud, moet u een X. 509-certificaat gebruiken om te communiceren met uw cluster. In test-en ontwikkelings omgevingen is het gebruikte certificaat vaak het cluster certificaat. In productie omgevingen moet het certificaat een client certificaat zijn dat verschilt van het cluster certificaat. U hebt het certificaat en de persoonlijke sleutel nodig. Het certificaat bestand (en sleutel) moet PEM zijn. U kunt een PEM-bestand maken dat het certificaat en de persoonlijke sleutel van een PFX-bestand bevat met de volgende openssl-opdracht:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Als het PFX-bestand niet is beveiligd met een wacht woord, gebruikt u `--passin pass:` voor de laatste para meter.

2. Open de **Cloud.jsin** het bestand in de map **PublishProfiles** . U moet het cluster eindpunt en de beveiligings referenties op de juiste wijze configureren voor uw cluster.

   - Het `ConnectionIPOrURL` veld bevat het IP-adres of de URL van uw cluster. Houd er rekening mee dat de waarde het URL-schema () niet bevat `https://` .
   - Het `ConnectionPort` veld moet standaard zijn `19080` , tenzij u deze poort expliciet hebt gewijzigd voor uw cluster.
   - Het `ClientKey` veld moet verwijzen naar een PEM. pem-of. key-bestand op uw lokale computer dat de persoonlijke sleutel voor uw client of cluster certificaat bevat.
   - Het `ClientCert` veld moet verwijzen naar een PEM. pem-of. crt-bestand op uw lokale computer dat de certificaat gegevens voor uw client of cluster bevat. certificaat. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Klik met de rechter muisknop op uw Service Fabric-toepassing en selecteer vervolgens **service Fabric**.
3. Klik in het context menu op **toepassing publiceren...**.
3. Kies in het venster **toepassing publiceren** de optie **PublishProfiles/Cloud.js** als doel profiel en klik op **publiceren**.

    ![Het dialoogvenster Publiceren voor Cloud](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. U kunt de voortgang van de publicatie bewerking volgen in het console venster.
5. Als u wilt controleren of uw toepassing wordt uitgevoerd, opent u Service Fabric Explorer op uw Azure-cluster in een browser venster. Voor bovenstaand voor beeld is dit: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer` . Vouw het knoop punt **toepassingen** uit en controleer of uw toepassing wordt uitgevoerd. 


Als uw toepassing Reliable Services services bevat voor beveiligde Linux-clusters, moet u ook een certificaat configureren dat door uw services kan worden gebruikt om Service Fabric runtime-Api's aan te roepen. Zie [een reliable Services-app configureren voor het uitvoeren van Linux-clusters](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)voor meer informatie.

Zie [Quick Start: een Java reliable Services-toepassing implementeren](./service-fabric-quickstart-java-reliable-services.md)voor een snelle procedure voor het implementeren van een service Fabric reliable Services toepassing die is geschreven in Java naar een beveiligd Linux-cluster.

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Een Service Fabric-toepassing implementeren met behulp van de uitvoering van eclips-configuraties

U kunt de Service Fabric-toepassing ook implementeren met behulp van Eclipse-uitvoerconfiguraties.

1. Ga in eclips naar **Run**  >  **Run-configuraties**.
2. Selecteer onder **Gradle Project** de uitvoerconfiguratie **ServiceFabricDeployer**.
3. Controleer in het rechterdeel venster op het tabblad **argumenten** of de para meters **IP**, **Port**, **clientCert** en **clientKey** op de juiste wijze zijn ingesteld voor uw implementatie. Standaard worden de para meters zo ingesteld dat deze op het lokale cluster worden geïmplementeerd, zoals in de volgende scherm afbeelding. Als u uw app wilt publiceren in azure, kunt u de para meters wijzigen zodat deze de eindpunt gegevens en beveiligings referenties voor uw Azure-cluster bevatten. Zie de vorige sectie, [uw service Fabric-toepassing publiceren in azure met eclips](#publish-your-service-fabric-application-to-azure-with-eclipse)voor meer informatie.

    ![Configuratie dialoogvenster lokaal uitvoeren](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Zorg ervoor dat de **werkmap** verwijst naar de toepassing die u wilt implementeren. Als u de toepassing wilt wijzigen, klikt u op de knop **Workspace** en selecteert u de gewenste toepassing.
6. Klik op **Apply** en vervolgens op **Run**.

De toepassing is binnen enkele ogenblikken gemaakt en geïmplementeerd. U kunt de implementatiestatus controleren in Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Een Service Fabric-service toevoegen aan uw Service Fabric-toepassing

Voer de volgende stappen uit als u een Service Fabric-service aan een bestaande Service Fabric-toepassing wilt toevoegen:

1.  Klik met de rechtermuisknop op het project waaraan u een service wilt toevoegen en klik vervolgens op **Service Fabric**.

    ![Service aan Service Fabric toevoegen pagina 1][add-service/p1]

2.  Klik op **Add Service Fabric Service** en voer de stappen uit om een service aan het project toe te voegen.
3.  Selecteer de servicesjabloon die u aan het project wilt toevoegen en klik op **Next**.

    ![Service aan Service Fabric toevoegen pagina 2][add-service/p2]

4.  Voer de servicenaam (en andere gevraagde informatie) in en klik op de knop **Add Service**.  

    ![Service aan Service Fabric toevoegen pagina 3][add-service/p3]

5.  Nadat de service is toegevoegd, ziet de algehele projectstructuur er ongeveer als volgt uit:

    ![Service aan Service Fabric toevoegen pagina 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Manifestversies van uw Service Fabric Java-toepassing bewerken

Als u manifestversies wilt bewerken, klikt u met de rechtermuisknop op het project, gaat u naar **Service Fabric** en selecteert u **Edit Manifest Versions...** in de vervolgkeuzelijst. In de wizard kunt u de manifestversies bijwerken voor het toepassingsmanifest, servicemanifest en de versies voor de pakketten **Code**, **Config** en **Data**.

Als u de optie **Automatically update application and service versions** inschakelt en vervolgens een versie bijwerkt, worden de manifestversies automatisch bijgewerkt. Als u bijvoorbeeld eerst het selectievakje inschakelt, vervolgens de versie van **Code** bijwerkt van 0.0.0 naar 0.0.1 en op **Finish** klikt, worden de versies van het servicemanifest en toepassingsmanifest automatisch bijgewerkt naar 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Uw Service Fabric Java-toepassing upgraden

Stel dat u voor een upgradescenario het project **App1** hebt gemaakt met behulp van de Service Fabric-invoegtoepassing in Eclipse. U hebt dit vervolgens met behulp van de invoegtoepassing geïmplementeerd om een toepassing met de naam **fabric:/App1Application** te maken. Het toepassings type is **App1ApplicationType** en de versie van de toepassing is 1,0. Nu wilt u een toepassingsupgrade uitvoeren zonder de beschikbaarheid van de toepassing te onderbreken.

Breng eerst eventuele wijzigingen aan in de toepassing en bouw vervolgens de gewijzigde service opnieuw op. Werk het manifestbestand van de gewijzigde service (ServiceManifest.xml) bij met de bijgewerkte versies voor de service (en code, configuratie of gegevens, indien van toepassing). Wijzig ook het toepassingsmanifest (ApplicationManifest.xml) met het bijgewerkte versienummer voor de toepassing en de gewijzigde service.  

Als u de toepassingsupgrade wilt uitvoeren met behulp van Eclipse, kunt u een dubbel uitvoerconfiguratieprofiel maken. Vervolgens gebruikt u dit om de toepassingsupgrade uit te voeren.

1.  Ga naar **Run**  >  **Run-configuraties**. Klik in het linkerdeelvenster op de kleine pijl, links van **Gradle Project**.
2.  Klik met de rechtermuisknop op **ServiceFabricDeployer** en selecteer **Duplicate**. Voer een nieuwe naam in voor deze configuratie, bijvoorbeeld **ServiceFabricUpgrader**.
3.  Ga in het rechterdeelvenster naar het tabblad **Arguments** en wijzig **-Pconfig='deploy'** in **-Pconfig=upgrade**. Klik vervolgens op **Apply**.

Met dit proces wordt een uitvoerconfiguratieprofiel gemaakt en opgeslagen dat u op elk gewenst moment kunt gebruiken om een upgrade van uw toepassing uit te voeren. Hiermee wordt ook de laatst bijgewerkte versie van het toepassingstype opgehaald uit het manifest-bestand van de toepassing.

De toepassingsupgrade duurt enkele minuten. U kunt de upgrade van de toepassing bewaken in Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Oude Service Fabric Java-toepassingen migreren die moeten worden gebruikt met Maven
We hebben onlangs de bibliotheken van Java Service Fabric verplaatst van de Service Fabric Java SDK naar de Maven-opslagplaats. De nieuwe toepassingen die u genereert met behulp van Eclipse produceren projecten die u zonder problemen kunt gebruiken met Maven. Uw bestaande stateless Service Fabric- of Java-actortoepassingen zult u echter moeten bijwerken om ze te laten werken met de Service Fabric Java-afhankelijkheden van Maven. Deze oudere toepassingen maken namelijk nog gebruik van de Service Fabric Java SDK. Volg [deze stappen](service-fabric-migrate-old-javaapp-to-use-maven.md) om te controleren of een oudere toepassing werkt met Maven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Quick Start: een java reliable Services-toepassing implementeren](./service-fabric-quickstart-java-reliable-services.md)voor snelle stappen voor het bouwen van een Java-betrouw bare service toepassing en het lokaal en naar Azure implementeren.
- Zie [fouten opsporen in een Java-service in eclips](./service-fabric-debugging-your-application-java.md)voor meer informatie over het opsporen van fouten in een Java-toepassing op uw lokale cluster.
- Zie [Services in een installatie van een lokale machine controleren en diagnosticeren](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)voor meer informatie over het bewaken en diagnosticeren van service Fabric toepassingen.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

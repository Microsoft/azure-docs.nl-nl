---
title: Een Service Fabric-cluster maken in Azure Portal
description: Meer informatie over het instellen van een beveiligd Service Fabric-cluster in azure met behulp van de Azure Portal en Azure Key Vault.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: c679a804db09b1034f31e9d8da1f7d2ad206f684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563723"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Een Service Fabric-cluster maken in azure met behulp van de Azure Portal
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure-portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Dit is een stapsgewijze hand leiding waarmee u de stappen voor het instellen van een Service Fabric cluster (Linux of Windows) in azure kunt door lopen met behulp van de Azure Portal. Deze hand leiding begeleidt u bij de volgende stappen:

* Maak een cluster in azure met behulp van de Azure Portal.
* Beheerders verifiëren met behulp van certificaten.

> [!NOTE]
> Voor meer geavanceerde beveiligings opties, zoals gebruikers verificatie met Azure Active Directory en het instellen van certificaten voor toepassings beveiliging, [maakt u uw cluster met behulp van Azure Resource Manager][create-cluster-arm].
> 
> 

## <a name="cluster-security"></a>Clusterbeveiliging 
Er worden in Service Fabric certificaten gebruikt voor verificatie en versleuteling om verschillende aspecten van een cluster en de bijbehorende toepassingen te beveiligen. Zie [Service Fabric-clusterbeveiligingsscenario's][service-fabric-cluster-security] voor meer informatie over hoe certificaten worden gebruikt in Service Fabric.

Als dit de eerste keer is dat u een service Fabric-cluster maakt of een cluster voor test werkbelastingen implementeert, kunt u door gaan naar de volgende sectie (**cluster maken in de Azure Portal**) en het systeem certificaten laten genereren die nodig zijn voor uw clusters waarop test werkbelastingen worden uitgevoerd. Als u een cluster instelt voor werk belastingen van de productie omgeving, kunt u door gaan met lezen.

#### <a name="cluster-and-server-certificate-required"></a>Cluster-en server certificaat (vereist)
Dit certificaat is vereist om een cluster te beveiligen en onbevoegde toegang te voor komen. Het biedt cluster beveiliging op een aantal manieren:

* **Cluster verificatie:** Verifieert de communicatie tussen knoop punten voor de cluster Federatie. Alleen knoop punten die hun identiteit kunnen bewijzen met dit certificaat kunnen lid worden van het cluster.
* **Server verificatie:** Verifieert de Cluster beheer-eind punten aan een Management-client, zodat de Management-client weet dat deze op het echte cluster is onderpraten. Dit certificaat biedt ook TLS voor de HTTPS-beheer-API en voor Service Fabric Explorer via HTTPS.

Om deze doel einden te kunnen gebruiken, moet het certificaat voldoen aan de volgende vereisten:

* Het certificaat moet een persoonlijke sleutel bevatten.
* Het certificaat moet worden gemaakt voor sleutel uitwisseling, exporteerbaar naar een pfx-bestand (Personal Information Exchange).
* De onderwerpnaam van het certificaat **moet overeenkomen met het domein** dat wordt gebruikt voor toegang tot het service Fabric cluster. Dit is vereist om TLS te bieden voor de HTTPS-beheer eindpunten en Service Fabric Explorer van het cluster. U kunt geen TLS/SSL-certificaat verkrijgen van een certificerings instantie (CA) voor het `.cloudapp.azure.com` domein. Verkrijg een aangepaste domein naam voor uw cluster. Wanneer u een certificaat van een certificerings instantie aanvraagt, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domein naam die voor uw cluster wordt gebruikt.

#### <a name="client-authentication-certificates"></a>Certificaten voor client verificatie
Aanvullende client certificaten verifiëren beheerders voor cluster beheer taken. Service Fabric heeft twee toegangs niveaus: **beheerder** en **alleen-lezen gebruiker**. Er moet mini maal één certificaat voor beheerders toegang worden gebruikt. Voor aanvullende toegang op gebruikers niveau moet er een afzonderlijk certificaat worden gegeven. Zie op [rollen gebaseerd toegangs beheer voor service Fabric-clients][service-fabric-cluster-security-roles]voor meer informatie over toegangs rollen.

U hoeft geen client verificatie certificaten te uploaden naar Key Vault om met Service Fabric te kunnen werken. Deze certificaten moeten alleen worden verleend aan gebruikers die gemachtigd zijn voor cluster beheer. 

> [!NOTE]
> Azure Active Directory is de aanbevolen manier om clients te verifiëren voor cluster beheer bewerkingen. Als u Azure Active Directory wilt gebruiken, moet u [een cluster maken met behulp van Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Toepassings certificaten (optioneel)
Een wille keurig aantal extra certificaten kan worden geïnstalleerd op een cluster voor de beveiliging van toepassingen. Voordat u uw cluster maakt, moet u rekening houden met de beveiligings scenario's voor toepassingen waarvoor een certificaat moet worden geïnstalleerd op de knoop punten, zoals:

* Versleuteling en ontsleuteling van toepassings configuratie waarden
* Versleuteling van gegevens tussen knoop punten tijdens replicatie 

Toepassings certificaten kunnen niet worden geconfigureerd bij [het maken van een cluster via de Azure Portal](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md). Als u toepassings certificaten wilt configureren tijdens de installatie van het cluster, moet u [een cluster maken met behulp van Azure Resource Manager][create-cluster-arm]. U kunt ook toepassings certificaten toevoegen aan het cluster nadat het is gemaakt.

## <a name="create-cluster-in-the-azure-portal"></a>Een cluster maken in de Azure Portal

Het maken van een productie cluster om te voldoen aan de behoeften van uw toepassing vergt enige planning, om u hiervan op de hoogte te houden. u kunt het beste het Service Fabric document over het [plannen van cluster overwegingen][service-fabric-cluster-capacity] lezen en begrijpen. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Zoeken naar de Service Fabric cluster resource

Meld u aan bij [Azure Portal][azure-portal].
Klik op **een resource maken** om een nieuwe resource sjabloon toe te voegen. Zoek de Service Fabric cluster sjabloon op de **Marketplace** onder **Alles**.
Selecteer **service Fabric cluster** in de lijst.

![Zoek Service Fabric cluster sjabloon op de Azure Portal.][SearchforServiceFabricClusterTemplate]

Ga naar de Blade **service Fabric cluster** en klik op **maken**.

De Blade **service Fabric cluster maken** bevat de volgende vier stappen:

### <a name="1-basics"></a>1. basis beginselen
![Scherm opname van het maken van een nieuwe resource groep.][CreateRG]

Op de Blade basis beginselen moet u de basis gegevens voor uw cluster opgeven.

1. Voer de naam van uw cluster in.
2. Voer een **gebruikers naam** en **wacht woord** in voor extern bureaublad voor de vm's.
3. Zorg ervoor dat u het **abonnement** selecteert waarop u het cluster wilt implementeren, met name als u meerdere abonnementen hebt.
4. Maak een nieuwe **resource groep**. Het is aan te raden om deze dezelfde naam als het cluster te geven, omdat u deze later kunt vinden, met name wanneer u wijzigingen wilt aanbrengen in uw implementatie of uw cluster wilt verwijderen.
   
   > [!NOTE]
   > Hoewel u kunt besluiten om een bestaande resource groep te gebruiken, is het een goed idee om een nieuwe resource groep te maken. Zo kunt u eenvoudig clusters verwijderen en alle resources die deze gebruiken.
   > 
   > 
5. Selecteer de **locatie** waarin u het cluster wilt maken. Als u van plan bent een bestaand certificaat te gebruiken dat u al hebt geüpload naar een sleutel kluis, moet u de regio gebruiken waarin uw sleutel kluis zich bevindt. 

### <a name="2-cluster-configuration"></a>2. cluster configuratie
![Een knooppunt type maken][CreateNodeType]

Configureer uw cluster knooppunten. Knooppunt typen definiëren de VM-grootten, het aantal Vm's en de bijbehorende eigenschappen. Uw cluster kan meer dan één knooppunt type hebben, maar het primaire knooppunt type (de eerste die u in de portal definieert) moet ten minste vijf Vm's hebben, aangezien dit het knooppunt type is waar Service Fabric systeem services worden geplaatst. Configureer geen **plaatsings eigenschappen** omdat er automatisch een standaard plaatsings eigenschap van ' NodeTypeName ' wordt toegevoegd.

> [!NOTE]
> Een veelvoorkomend scenario voor meerdere knooppunt typen is een toepassing die een front-end-service en een back-end-service bevat. U wilt de front-end-service op kleinere Vm's (VM-grootten zoals D2_V2) met poorten openen op internet en de back-end-service op grotere Vm's zetten (met VM-grootten zoals D3_V2, D6_V2, D15_V2 enzovoort) zonder open bare poorten op internet.
> 

1. Kies een naam voor het knooppunt type (1-12 tekens die alleen letters en cijfers bevatten).
2. De minimale **grootte** van vm's voor het primaire knooppunt type wordt bepaald door de **duurzaamheids categorie** die u voor het cluster kiest. De standaard waarde voor de duurzaamheids categorie is bronzen. Zie [How to choose the service Fabric cluster duurzaamheid][service-fabric-cluster-durability](Engelstalig) voor meer informatie over duurzaamheid.
3. Selecteer de **grootte van de virtuele machine**. Vm's uit de D-serie hebben SSD-stations en worden ten zeerste aanbevolen voor stateful toepassingen. Gebruik geen VM-SKU met gedeeltelijke kernen of minder dan 10 GB aan beschik bare schijf capaciteit. Raadpleeg het [document plannings overweging van service Fabric-cluster][service-fabric-cluster-capacity] voor hulp bij het selecteren van de VM-grootte.
4.  **Cluster met één knoop punt en drie knooppunt clusters** zijn uitsluitend bedoeld voor test doeleinden. Ze worden niet ondersteund voor actieve werk belastingen voor productie.
5. Kies de **eerste capaciteit van de schaalset voor virtuele machines** voor het knooppunt type. U kunt het aantal Vm's in een knooppunt type later in-of uitschalen, maar op het primaire knooppunt type is het minimum vijf voor de werk belasting van de productie. Andere knooppunt typen kunnen mini maal één virtuele machine hebben. Het minimum **aantal** vm's voor het primaire knooppunt type stuurt de **betrouw baarheid** van uw cluster.  
6. **Aangepaste eind punten** configureren. In dit veld kunt u een door komma's gescheiden lijst met poorten invoeren die u wilt weer geven via de Azure Load Balancer op het open bare Internet voor uw toepassingen. Als u bijvoorbeeld van plan bent een webtoepassing te implementeren op uw cluster, voert u ' 80 ' hier in om verkeer op poort 80 toe te staan in uw cluster. Zie [communicatie met toepassingen][service-fabric-connect-and-communicate-with-services] voor meer informatie over eind punten
7. **Schakel omgekeerde proxy in**.  De [service Fabric reverse-proxy](service-fabric-reverseproxy.md) helpt micro services die worden uitgevoerd in een service Fabric cluster te detecteren en te communiceren met andere services met http-eind punten.
8. Ga terug naar de Blade **cluster configuratie** , onder **+ optionele instellingen weer geven**, cluster **diagnostiek** configureren. Diagnostische gegevens zijn standaard ingeschakeld in uw cluster om te helpen bij het oplossen van problemen. Als u Diagnostische gegevens wilt uitschakelen, wijzigt u de **status** in-en **uitschakelen**. Het uitschakelen van diagnostische gegevens wordt **niet** aanbevolen. Als u Application Insights project al hebt gemaakt, geeft u de bijbehorende sleutel, zodat de toepassings traceringen ernaar worden doorgestuurd.
9. **DNS-service toevoegen**.  De [DNS-service](service-fabric-dnsservice.md) is een optionele service waarmee u andere services kunt vinden met behulp van het DNS-protocol.
10. Selecteer de **infrastructuur upgrade modus** waarvoor u uw cluster wilt instellen. Selecteer **automatisch**, als u wilt dat het systeem automatisch de meest recente beschik bare versie ophaalt en probeert uw cluster ernaar bij te werken. Stel de modus in op **hand matig**, als u een ondersteunde versie wilt kiezen. Zie het [service Fabric cluster upgrade document][service-fabric-cluster-upgrade] voor meer informatie over de infrastructuur upgrade modus.

> [!NOTE]
> We ondersteunen alleen clusters met ondersteunde versies van Service Fabric. Als u de **hand matige** modus selecteert, neemt u de verantwoordelijkheid voor het upgraden van uw cluster naar een ondersteunde versie.
> 

### <a name="3-security"></a>3. beveiliging
![Scherm opname van beveiligings configuraties op Azure Portal.][BasicSecurityConfigs]

We hebben de **basis** optie voor het instellen van een veilig test cluster voor u gemakkelijk maken. Als u al een certificaat hebt en dit hebt geüpload naar uw [sleutel kluis](../key-vault/index.yml) (en de sleutel kluis voor de implementatie hebt ingeschakeld), gebruikt u de optie **aangepast**

#### <a name="basic-option"></a>Basis optie
Volg de schermen om een bestaande sleutel kluis toe te voegen of te hergebruiken en een certificaat toe te voegen. Het toevoegen van het certificaat is een synchroon proces, zodat u moet wachten tot het certificaat is gemaakt.

Het Temptation van de navigatie van het scherm totdat het voor gaande proces is voltooid.

![Scherm afbeelding toont de beveiligings pagina met de basis die is geselecteerd met het deel venster sleutel kluis en het deel venster sleutel kluis maken.][CreateKeyVault]

Nu de sleutel kluis is gemaakt, bewerkt u het toegangs beleid voor uw sleutel kluis. 

![Scherm afbeelding toont het deel venster Service Fabric cluster maken met optie 3-beveiliging geselecteerd en een uitleg dat de sleutel kluis niet is ingeschakeld.][CreateKeyVault2]

Klik op **toegangs beleid bewerken**, **Geef geavanceerd toegangs beleid weer** en schakel toegang tot Azure virtual machines in voor implementatie. Het is raadzaam om de sjabloon implementatie ook in te scha kelen. Wanneer u uw selecties hebt gemaakt, moet u niet verg eten klikken op de knop **Opslaan** om het deel venster **toegangs beleid** te sluiten.

![Scherm afbeelding toont het deel venster Service Fabric cluster maken met het deel venster beveiliging, waarna het deel venster toegangs beleid openen wordt geopend.][CreateKeyVault3]

Voer de naam van het certificaat in en klik op **OK**.

![Scherm afbeelding toont het deel venster Service Fabric cluster maken met de beveiliging die eerder is geselecteerd, maar zonder de uitleg dat de sleutel kluis niet is ingeschakeld.][CreateKeyVault4]

#### <a name="custom-option"></a>Aangepaste optie
Sla deze sectie over als u de stappen in de optie **basis** al hebt uitgevoerd.

![Scherm afbeelding toont het dialoog venster beveiligings instellingen voor cluster beveiliging configureren.][SecurityCustomOption]

U hebt de gegevens van de bron sleutel kluis, de certificaat-URL en de vinger afdruk van het certificaat nodig om de beveiligings pagina te volt ooien. Als u dit niet hebt gedaan, opent u een ander browser venster en voert u de volgende stappen uit in de Azure Portal

1. Navigeer naar uw sleutel kluis service.
2. Selecteer het tabblad ' Eigenschappen ' en kopieer de ' RESOURCE-ID ' naar ' bron sleutel kluis ' in het andere browser venster 

    ![Scherm afbeelding toont de venster Eigenschappen voor de sleutel kluis.][CertInfo0]

3. Selecteer nu het tabblad Certificaten.
4. Klik op vinger afdruk van certificaat, waarmee u naar de pagina versies gaat.
5. Klik op de GUID'S die u onder de huidige versie ziet.

    ![Scherm afbeelding toont het certificaat venster voor de sleutel kluis][CertInfo1]

6. U ziet nu het volgende in het scherm. Kopieer de hexadecimale SHA-1-vinger afdruk naar ' certificaat vingerafdruk ' in het andere browser venster
7. Kopieer de ' geheime id ' naar de ' certificaat-URL ' in het andere browser venster.

    ![Scherm afbeelding toont het dialoog venster certificaat versie met een optie om de certificaat-id te kopiëren.][CertInfo2]

Schakel het selectie vakje **Geavanceerde instellingen configureren** in om client certificaten voor de **admin-client** en de **alleen-lezen client** in te voeren. Voer in deze velden de vinger afdruk van het client certificaat voor de beheerder en de vinger afdruk van uw alleen-lezen gebruikers certificaat in, indien van toepassing. Wanneer beheerders proberen verbinding te maken met het cluster, krijgen ze alleen toegang als ze een certificaat hebben met een vinger afdruk die overeenkomt met de opgegeven vingerafdruk waarden.  

### <a name="4-summary"></a>4. samen vatting

U bent nu klaar om het cluster te implementeren. Voordat u dat doet, moet u het certificaat downloaden, maar in het grote blauwe vak voor de koppeling kijken. Zorg ervoor dat het certificaat op een veilige plaats blijft. u hebt deze nodig om verbinding te maken met uw cluster. Omdat het certificaat dat u hebt gedownload geen wacht woord heeft, wordt u geadviseerd om er een toe te voegen.

Klik op **maken** om het maken van het cluster te volt ooien. U kunt de sjabloon eventueel downloaden.

![Scherm afbeelding toont de pagina overzicht van Service Fabric cluster maken met een koppeling om een certificaat weer te geven en te downloaden.][Summary]

U kunt de voortgang van het maken in de meldingen bekijken. (Klik op het pictogram ' Bell ' bij de status balk rechtsboven in het scherm.) Als u tijdens het maken van het cluster op **aan start Board** vastmaken hebt geklikt, ziet u de **implementatie van service Fabric cluster** is vastgemaakt aan het **Start** Board. Dit proces kan enige tijd duren. 

Als u beheer bewerkingen wilt uitvoeren op uw cluster met behulp van Power shell of CLI, moet u verbinding maken met uw cluster. Lees meer informatie over het [maken van verbinding met uw cluster](service-fabric-connect-to-secure-cluster.md).

## <a name="view-your-cluster-status"></a>De cluster status weer geven
![Scherm opname van cluster Details in het dash board.][ClusterDashboard]

Nadat het cluster is gemaakt, kunt u uw cluster in de portal inspecteren:

1. Ga naar **Bladeren** en klik op **service Fabric clusters**.
2. Zoek het cluster en klik erop.
3. U kunt de gegevens van uw cluster nu bekijken op het dashboard, waaronder het openbare eindpunt van het cluster en een koppeling naar Service Fabric Explorer.

In de sectie **knooppunt controle** op de Blade van het cluster wordt aangegeven hoeveel virtuele machines in orde zijn en niet in orde zijn. U kunt meer informatie over de status van het cluster vinden op [service Fabric status model Inleiding][service-fabric-health-introduction].

> [!NOTE]
> Service Fabric clusters vereisen een bepaald aantal knoop punten om altijd de beschik baarheid te behouden en de status te behouden als ' quorum onderhouden '. Daarom is het doorgaans niet veilig om alle computers in het cluster af te sluiten, tenzij u eerst een [volledige back-up van uw status][service-fabric-reliable-services-backup-restore]hebt uitgevoerd.
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Externe verbinding maken met een virtuele-machine Scale set-exemplaar of een cluster knooppunt
Elk van de NodeTypes die u in uw cluster opgeeft, resulteert in het instellen van een Schaalset voor virtuele machines. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Volgende stappen
Op dit moment hebt u een beveiligd cluster met behulp van certificaten voor beheer authenticatie. Vervolgens [maakt u verbinding met uw cluster](service-fabric-connect-to-secure-cluster.md) en leert u hoe u [toepassings geheimen kunt beheren](service-fabric-application-secret-management.md).  Meer informatie over [service Fabric ondersteunings opties](service-fabric-support.md).

<!-- Links -->
[azure-powershell]: /powershell/azure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png

---
title: Hybride verbindingen
description: Meer informatie over het maken en gebruiken van hybride verbindingen in Azure App Service om toegang te krijgen tot bronnen in verschillende netwerken.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 02/05/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 1b3fc4a254c1157f2c2336e6360ba7621f31364d
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594228"
---
# <a name="azure-app-service-hybrid-connections"></a>Hybride verbindingen in Azure App Service

Hybride verbindingen is zowel een service in azure als een functie in Azure App Service. Als service heeft het gebruik en de mogelijkheden die groter zijn dan de functies die worden gebruikt in App Service. Zie [Azure Relay hybride verbindingen][HCService]voor meer informatie over hybride verbindingen en het gebruik ervan buiten app service.

In App Service kan Hybride verbindingen worden gebruikt om toegang te krijgen tot toepassings bronnen in elk netwerk waarmee uitgaande oproepen naar Azure via poort 443 kunnen worden gemaakt. Hybride verbindingen biedt toegang vanuit uw app tot een TCP-eind punt en biedt geen nieuwe manier om toegang te krijgen tot uw app. De hybride verbinding wordt gebruikt in App Service en is afgestemd op één combi natie van TCP-host en poort. Hiermee kunnen uw apps toegang krijgen tot bronnen in elk besturings systeem, op voor waarde dat het een TCP-eind punt is. Met de functie Hybride verbindingen weet u niet of u weet wat het toepassings protocol is of wat u opent. Het biedt gewoon toegang tot het netwerk.  

## <a name="how-it-works"></a>Uitleg ##
Hybride verbindingen moet een relay-agent worden geïmplementeerd, waar deze zowel het gewenste eind punt als aan Azure kan bereiken. De relay-agent, Hybrid Connection Manager (HCM), belt naar Azure Relay via poort 443. Vanuit de Web-App-site maakt de App Service-infra structuur ook verbinding met Azure Relay namens de toepassing. Via de gekoppelde verbindingen kan uw app toegang krijgen tot het gewenste eind punt. De verbinding maakt gebruik van TLS 1,2 voor beveiliging en SAS-sleutels (Shared Access Signature) voor verificatie en autorisatie.    

![Diagram van de stroom op hoog niveau van hybride verbinding][1]

Als uw app een DNS-aanvraag maakt die overeenkomt met een geconfigureerd hybride verbindings eindpunt, wordt het uitgaande TCP-verkeer omgeleid via de hybride verbinding.  

> [!NOTE]
> Dit betekent dat u altijd een DNS-naam moet gebruiken voor uw hybride verbinding. Sommige client software voert geen DNS-zoek opdracht uit als het eind punt in plaats daarvan een IP-adres gebruikt. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Voor delen van hybride verbinding App Service ###

Er zijn een aantal voor delen ten opzichte van de Hybride verbindingen mogelijkheid, waaronder:

- Apps hebben veilig toegang tot on-premises systemen en services.
- Voor de functie is geen eind punt via internet toegankelijk.
- Het is snel en eenvoudig in te stellen. Er zijn geen gateways vereist
- Elke hybride verbinding komt overeen met één host: poort combinatie, handig voor beveiliging.
- Normaal gesp roken is geen firewall-gaten vereist. De verbindingen zijn alle uitgaande via standaard webpoorten.
- Omdat de functie netwerk niveau is, wordt deze neutraal in de taal die wordt gebruikt door uw app en de technologie die door het eind punt wordt gebruikt.
- Het kan worden gebruikt om toegang te bieden in meerdere netwerken vanuit één app. 
- Het wordt ondersteund in GA voor Windows-apps en Linux-apps. Het wordt niet ondersteund voor Windows-container-apps.

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Wat u niet kunt doen met Hybride verbindingen ###

Wat u niet kunt doen met Hybride verbindingen zijn onder andere:

- Koppel een station.
- Gebruik UDP.
- Toegang tot TCP-services die gebruikmaken van dynamische poorten, zoals de passieve FTP-modus of de uitgebreide passieve modus.
- Ondersteuning voor LDAP, omdat hiervoor UDP vereist is.
- Ondersteuning Active Directory, omdat u geen domein kunt toevoegen aan een App Service-werk nemer. 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Hybride verbindingen toevoegen en maken in uw app ##

Als u een hybride verbinding wilt maken, gaat u naar de [Azure Portal][portal] en selecteert u uw app. Selecteer **netwerken**  >  **Configureer uw hybride verbindings eindpunten**. Hier ziet u de Hybride verbindingen die zijn geconfigureerd voor uw app.  

![Scherm opname van de lijst met hybride verbindingen][2]

Selecteer **[+] hybride verbinding toevoegen** om een nieuwe hybride verbinding toe te voegen.  U ziet een lijst met de Hybride verbindingen die u al hebt gemaakt. Als u een of meer ervan aan uw app wilt toevoegen, selecteert u de gewenste items en selecteert u **geselecteerde hybride verbinding toevoegen**.  

![Scherm opname van de portal voor hybride verbindingen][3]

Als u een nieuwe hybride verbinding wilt maken, selecteert u **nieuwe hybride verbinding maken**. Geef het volgende op: 

- Naam van de hybride verbinding.
- Hostnaam van eind punt.
- Eindpunt poort.
- Service Bus naam ruimte die u wilt gebruiken.

![Scherm afbeelding van het dialoog venster nieuwe hybride verbinding maken][4]

Elke hybride verbinding is gekoppeld aan een Service Bus naam ruimte en elke Service Bus naam ruimte bevindt zich in een Azure-regio. Het is belang rijk om een Service Bus naam ruimte in dezelfde regio als uw app te gebruiken om te voor komen dat de netwerk latentie wordt geduurd.

Als u uw hybride verbinding uit uw app wilt verwijderen, klikt u er met de rechter muisknop op en selecteert u **verbinding verbreken**.  

Wanneer een hybride verbinding wordt toegevoegd aan uw app, kunt u de details ervan weer geven door deze te selecteren. 

![Scherm opname van de details van hybride verbindingen][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Een hybride verbinding maken in de Azure Relay Portal ###

Naast de portal-ervaring vanuit uw app, kunt u in de Azure Relay Portal Hybride verbindingen maken. Voor een hybride verbinding die wordt gebruikt door App Service moet het volgende:

* Client autorisatie vereist.
* Een meta gegevens item met een naam eind punt hebben dat een host: poort combinatie als waarde bevat.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybride verbindingen-en App Service-plannen ##

App Service Hybride verbindingen zijn alleen beschikbaar in Sku's Basic, Standard, Premium en alleenstaande prijzen. Er zijn limieten gebonden aan het prijs plan.  

| Prijs plan | Aantal Hybride verbindingen bruikbaar in het plan |
|----|----|
| Basic | 5 per abonnement |
| Standard | 25 per abonnement |
| PremiumV2 | 200 per app |
| Geïsoleerd | 200 per app |

In de gebruikers interface van het App Service plan wordt weer gegeven hoeveel Hybride verbindingen er worden gebruikt en door welke apps.  

![Scherm afbeelding van de eigenschappen van het App Service plan][6]

Selecteer de hybride verbinding om de details te bekijken. U kunt alle informatie zien die u hebt gezien in de app-weer gave. U kunt ook zien hoeveel andere apps in hetzelfde abonnement gebruikmaken van die hybride verbinding.

Er geldt een limiet voor het aantal hybride verbindings eindpunten dat kan worden gebruikt in een App Service plan. Elke hybride verbinding die wordt gebruikt, kan echter worden gebruikt in elk wille keurig aantal apps in dat plan. Een enkele hybride verbinding die wordt gebruikt in vijf afzonderlijke apps in een App Service abonnement is bijvoorbeeld een hybride verbinding.

### <a name="pricing"></a>Prijzen ###

Naast de vereiste van een App Service plan SKU, zijn er extra kosten verbonden aan het gebruik van Hybride verbindingen. Er worden kosten in rekening gebracht voor elke listener die wordt gebruikt door een hybride verbinding. De listener is de Hybrid Connection Manager. Als er vijf Hybride verbindingen worden ondersteund door twee hybride verbindings beheer, zou dat 10 listeners zijn. Zie [Service Bus prijzen][sbpricing]voor meer informatie.

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager ##

De functie Hybride verbindingen vereist een relay-agent in het netwerk dat als host fungeert voor uw hybride verbindings eindpunt. Deze relay-agent wordt de Hybrid Connection Manager (HCM) genoemd. Als u HCM wilt downloaden, selecteert u vanuit uw app in het [Azure Portal][portal] **netwerken**  >  **uw hybride verbindings-eind punten configureren**.  

Dit hulp programma kan worden uitgevoerd op Windows Server 2012 en hoger. De HCM wordt uitgevoerd als een service en maakt verbinding met het uitgaande verkeer naar Azure Relay op poort 443.  

Nadat u HCM hebt geïnstalleerd, kunt u HybridConnectionManagerUi.exe uitvoeren om de gebruikers interface voor het hulp programma te gebruiken. Dit bestand bevindt zich in de installatiemap van Hybrid Connection Manager. In Windows 10 kunt u ook alleen zoeken naar *Hybrid Connection Manager gebruikers interface* in het zoekvak.  

![Scherm opname van Hybrid Connection Manager][7]

Wanneer u de HCM-gebruikers interface start, is het eerste wat u ziet een tabel met een lijst met alle Hybride verbindingen die zijn geconfigureerd met dit exemplaar van de HCM. Als u wijzigingen wilt aanbrengen, moet u eerst verifiëren met Azure. 

Een of meer Hybride verbindingen toevoegen aan uw HCM:

1. Start de HCM-gebruikers interface.
2. Selecteer **een andere hybride verbinding configureren**.
![Scherm opname van nieuwe Hybride verbindingen configureren][8]

1. Meld u aan met uw Azure-account om uw Hybride verbindingen beschikbaar te stellen voor uw abonnementen. De HCM blijft uw Azure-account verder gebruiken dan dat. 
1. Kies een abonnement.
1. Selecteer het Hybride verbindingen dat u door de HCM wilt laten door sturen.
![Scherm opname van Hybride verbindingen][9]

1. Selecteer **Opslaan**.

U kunt nu de Hybride verbindingen zien die u hebt toegevoegd. U kunt ook de geconfigureerde hybride verbinding selecteren om de details te bekijken.

![Scherm afbeelding van de details van de hybride verbinding][10]

Ter ondersteuning van de Hybride verbindingen deze is geconfigureerd met, vereist HCM:

- TCP-toegang tot Azure via poort 443.
- TCP-toegang tot het hybride verbindings eindpunt.
- De mogelijkheid om DNS-Zoek-ups uit te voeren op de eindpunt-host en de Service Bus naam ruimte.

> [!NOTE]
> Azure Relay is afhankelijk van websockets voor connectiviteit. Deze functie is alleen beschikbaar in Windows Server 2012 of hoger. Als gevolg hiervan wordt HCM niet ondersteund op een eerdere versie dan Windows Server 2012.
>

### <a name="redundancy"></a>Redundantie ###

Elke HCM kan meerdere Hybride verbindingen ondersteunen. Daarnaast kan elke gegeven hybride verbinding worden ondersteund door meerdere HCMs. Het standaard gedrag is het routeren van verkeer via de geconfigureerde HCMs voor een bepaald eind punt. Als u een hoge Beschik baarheid op uw Hybride verbindingen van uw netwerk wilt, voert u meerdere HCMs uit op afzonderlijke machines. De taakverdelings algoritme die door de Relay-service wordt gebruikt voor het distribueren van verkeer naar de HCMs is een wille keurige toewijzing. 

### <a name="manually-add-a-hybrid-connection"></a>Een hybride verbinding hand matig toevoegen ###

Als u wilt dat iemand buiten uw abonnement als host voor een HCM-exemplaar voor een bepaalde hybride verbinding beschikt, moet u de gateway connection string voor de hybride verbinding delen. U kunt de gateway connection string zien in de eigenschappen van de hybride verbinding in de [Azure Portal][portal]. Als u deze teken reeks wilt gebruiken, selecteert u **hand matig invoeren** in de HCM en plakt u de gateway Connection String.

![Een hybride verbinding hand matig toevoegen][11]

### <a name="upgrade"></a>Upgraden ###

De Hybrid Connection Manager bevatten periodieke updates om problemen op te lossen of verbeteringen aan te brengen. Wanneer er upgrades worden uitgebracht, wordt een pop-upvenster weer gegeven in de HCM-gebruikers interface. Wanneer de upgrade wordt toegepast, worden de wijzigingen toegepast en wordt de HCM opnieuw gestart. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Een hybride verbinding met uw app via een programma toevoegen ##

Er is ondersteuning voor Azure CLI voor Hybride verbindingen. De opdrachten die worden uitgevoerd, worden zowel toegepast op de app als op het niveau van het App Service plan.  De opdrachten op app-niveau zijn:

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

Met de App Service plan opdrachten kunt u instellen welke sleutel een bepaalde hybride verbinding zal gebruiken. Er zijn twee sleutels ingesteld op elke hybride verbinding, een primaire en een secundaire. U kunt ervoor kiezen om de primaire of secundaire sleutel met de onderstaande opdrachten te gebruiken. Zo kunt u scha kelen tussen sleutels voor wanneer u uw sleutels periodiek opnieuw wilt genereren. 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>Uw Hybride verbindingen beveiligen ##

Een bestaande hybride verbinding kan worden toegevoegd aan andere App Service Web Apps door elke gebruiker die voldoende machtigingen heeft voor de onderliggende Azure Service Bus relay. Dit betekent dat als u wilt voor komen dat andere gebruikers dezelfde hybride verbinding opnieuw gebruiken (bijvoorbeeld wanneer de doel resource een service is zonder extra beveiligings maatregelen om onbevoegde toegang te voor komen), moet u de toegang tot de Azure Service Bus relay vergren delen.

Iedereen met `Reader` toegang tot de relay kan de hybride verbinding _zien_ wanneer deze probeert toe te voegen aan hun Web-App in de Azure Portal, maar ze kunnen deze niet _toevoegen_ omdat ze geen machtigingen hebben om de Connection String op te halen die wordt gebruikt om de relay-verbinding tot stand te brengen. Om de hybride verbinding toe te voegen, moeten ze de `listKeys` machtiging ( `Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action` ) hebben. Met de `Contributor` rol of een andere rol die deze machtiging voor de relay bevat, kunnen gebruikers de hybride verbinding gebruiken en toevoegen aan hun eigen web apps.

## <a name="troubleshooting"></a>Problemen oplossen ##

De status verbonden betekent dat er ten minste één HCM is geconfigureerd met die hybride verbinding en dat Azure kan worden bereikt. Als de status voor uw hybride verbinding niet **verbonden** is, is uw hybride verbinding niet geconfigureerd op een HCM die toegang heeft tot Azure. Wanneer uw HCM **niet zijn verbonden** , zijn er enkele dingen die u kunt controleren:

* Heeft uw host uitgaande toegang tot Azure op poort 443? U kunt testen vanaf uw HCM-host met behulp van de Power shell *-opdracht test-NetConnection-bestemming-P poort* 
* Ligt uw HCM mogelijk in een slechte staat? Probeer de lokale service Azure Hybrid Connection Manager-service opnieuw te starten.

Als uw status **is verbonden** , maar uw app uw eind punt niet kan bereiken, geldt het volgende:

* Zorg ervoor dat u een DNS-naam gebruikt in uw hybride verbinding. Als u een IP-adres gebruikt, kan de vereiste client-DNS-zoek actie niet plaatsvinden. Als de client die in uw web-app wordt uitgevoerd geen DNS-zoek opdracht heeft, werkt de hybride verbinding niet
* Controleer of de DNS-naam die wordt gebruikt in uw hybride verbinding kan worden omgezet vanaf de HCM-host. Controleer de oplossing met behulp van *nslookup EndpointDNSname* waarbij EndpointDNSname een exacte overeenkomst is voor wat wordt gebruikt in uw hybride verbindings definitie.
* Test de toegang vanaf uw HCM-host naar uw eind punt met behulp van de Power shell *-opdracht test-NetConnection EndpointDNSname-P-poort*  als u het eind punt niet kunt bereiken vanaf uw HCM-host, en vervolgens firewalls tussen de twee hosts, inclusief op hosts gebaseerde firewalls op de doelhost.

In App Service kan het opdracht regel programma **tcpping** worden aangeroepen vanuit de console geavanceerde hulp middelen (kudu). Met dit hulp programma kunt u aangeven of u toegang hebt tot een TCP-eind punt, maar u krijgt hier geen informatie over als u toegang hebt tot een hybride verbindings eindpunt. Wanneer u het hulp programma gebruikt in de-console op basis van een hybride verbindings eindpunt, bevestigt u alleen dat het gebruikmaakt van een host: poort combinatie.  

Als u een opdracht regel-client voor uw eind punt hebt, kunt u de verbinding testen via de app-console. U kunt bijvoorbeeld de toegang tot webserver eindpunten testen met behulp van krul.


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/

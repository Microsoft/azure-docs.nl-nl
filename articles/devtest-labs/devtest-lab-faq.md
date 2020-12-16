---
title: Azure DevTest Labs Veelgestelde vragen | Microsoft Docs
description: In dit artikel vindt u antwoorden op enkele veelgestelde vragen over Azure DevTest Labs.
ms.topic: article
ms.date: 07/17/2020
ms.openlocfilehash: 9fcdc160754822d5c6f22b7349d0e72f0cf22633
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590270"
---
# <a name="azure-devtest-labs-faq"></a>Veelgestelde vragen over Azure DevTest Labs
Krijg antwoorden op enkele van de meest voorkomende vragen over Azure DevTest Labs.

## <a name="blog-post"></a>Blog bericht
Onze DevTest Labs-team blog is vanaf 20 maart 2019 buiten gebruik gesteld. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Waar kan ik de onderdelen updates nu volgen?
Vanaf nu gaan we onderdelen updates en informatieve blog berichten plaatsen op de Azure-blog en Azure-updates. Deze blog berichten worden ook op elke gewenst plek gekoppeld aan onze documentatie.

Abonneer u op de [DevTest Labs Azure-blog](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) en [DevTest Labs Azure-updates](https://azure.microsoft.com/updates/?product=devtest-lab) om op de hoogte te blijven van nieuwe functies in DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Wat gebeurt er met de bestaande blog berichten?
We werken momenteel aan het migreren van bestaande blog berichten (met uitzonde ring van uitval updates) naar onze [DevTest Labs-documentatie](devtest-lab-overview.md). Wanneer de MSDN-blog wordt afgeschaft, wordt deze omgeleid naar het overzicht van de documentatie voor DevTest Labs. Wanneer u de omleiding hebt uitgevoerd, kunt u zoeken naar het artikel dat u zoekt in de titel filteren op. We hebben nog geen berichten gemigreerd, maar moeten aan het einde van deze maand worden gedaan. 


### <a name="where-do-i-see-outage-updates"></a>Waar kan ik de uitval updates bekijken?
Er worden nu uitval updates geboekt via onze Twitter-handle. Volg ons op Twitter om de nieuwste updates te ontvangen op storingen en bekende bugs.

### <a name="twitter"></a>Twitter
Onze Twitter-ingang: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Algemeen
### <a name="what-if-my-question-isnt-answered-here"></a>Wat gebeurt er als mijn vraag hier niet wordt beantwoord?
Als uw vraag hier niet wordt vermeld, laat het ons dan weten, zodat we u kunnen helpen om een antwoord te vinden.

- Plaats een vraag aan het einde van deze veelgestelde vragen.
- Als u een breder publiek wilt bereiken, plaatst u een vraag op de [pagina micro soft Q&een vraag voor Azure DevTest Labs](/answers/topics/azure-devtestlabs.html). Neem contact op met het Azure DevTest Labs team en andere leden van de community.
- Voor functie aanvragen stuurt u uw aanvragen en ideeën naar [Azure DevTest Labs gebruikers stem](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Wat is een Microsoft-account?
Een Microsoft-account is een account dat u gebruikt voor bijna alles wat u met micro soft-apparaten en-services doet. Het is een e-mail adres en wacht woord dat u gebruikt om u aan te melden bij Skype, Outlook.com, OneDrive, Windows Phone, Azure en Xbox Live. Eén account houdt in dat uw bestanden, Foto's, contact personen en instellingen u op elk apparaat kunnen volgen.

> [!NOTE]
> Een Microsoft-account een Windows Live ID wordt genoemd.

### <a name="why-should-i-use-azure-devtest-labs"></a>Waarom moet ik Azure DevTest Labs gebruiken?
Azure DevTest Labs kunt uw team tijd en geld besparen. Ontwikkel aars kunnen hun eigen omgevingen maken met behulp van verschillende bases. Ze kunnen ook artefacten gebruiken om snel toepassingen te implementeren en te configureren. Door aangepaste installatie kopieën en formules te gebruiken, kunt u virtuele machines (Vm's) opslaan als sjablonen en deze eenvoudig reproduceren in het team. DevTest Labs biedt ook verschillende Configureer bare beleids regels die Lab-beheerders kunnen gebruiken om verspilling te verminderen en de omgevingen van een team te beheren. Dit beleid omvat automatisch afsluiten, kosten drempel, maximum aantal Vm's per gebruiker en maximale VM-grootte. Zie het [overzicht](devtest-lab-overview.md) of de [Introductie video](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs)voor een uitgebreidere uitleg van DevTest Labs.

### <a name="what-does-worry-free-self-service-mean"></a>Wat betekent ' beschik bare self-service '?
De gratis selfservice-service houdt in dat ontwikkel aars en testers hun eigen omgevingen maken waar nodig. Beheerders hebben de zekerheid dat DevTest Labs u kan helpen bij het instellen van de juiste toegang, het beperken van verspilling en beheer kosten. Beheerders kunnen opgeven welke VM-grootten zijn toegestaan, het maximum aantal Vm's en wanneer Vm's worden gestart en afgesloten. Met DevTest Labs kunt u ook eenvoudig kosten bewaken en waarschuwingen instellen, zodat u op de hoogte blijft van de manier waarop Lab-bronnen worden gebruikt.

### <a name="how-can-i-use-devtest-labs"></a>Hoe kan ik DevTest Labs gebruiken?
DevTest Labs is handig wanneer u ontwikkel-of test omgevingen nodig hebt en u deze snel opnieuw wilt produceren, of als u ze wilt beheren met behulp van beleid voor het besparen van kosten.
Hier volgen enkele scenario's waarin onze klanten DevTest Labs gebruiken voor:

- Ontwikkel-en test omgevingen op één plek beheren. Gebruik beleids regels om de kosten te verlagen en aangepaste installatie kopieën te maken voor het delen van builds in het team.
- Ontwikkel een toepassing met behulp van aangepaste installatie kopieën om de schijf status in de ontwikkelings fases op te slaan.
- Houd kosten bij met betrekking tot de voortgang.
- Grootschalige test omgevingen maken voor het testen van kwaliteits bewaking.
- Gebruik artefacten en formules om een toepassing in verschillende omgevingen eenvoudig te configureren en te reproduceren.
- Distribueer Vm's voor hackathons trappen (samen werking of test werk) en pas ze vervolgens eenvoudig toe wanneer de gebeurtenis wordt beëindigd.

### <a name="how-am-i-billed-for-devtest-labs"></a>Hoe word ik gefactureerd voor DevTest Labs?
DevTest Labs is een gratis service. Het maken van Labs en het configureren van beleids regels, sjablonen en artefacten in DevTest Labs is gratis. U betaalt alleen voor de Azure-resources die worden gebruikt in uw Labs, zoals Vm's, opslag accounts en virtuele netwerken. Zie [Azure DevTest Labs prijzen](https://azure.microsoft.com/pricing/details/devtest-lab/)voor meer informatie over de kosten van Lab-resources.

## <a name="security"></a>Beveiliging

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Wat zijn de verschillende beveiligings niveaus in DevTest Labs?
Beveiligings toegang wordt bepaald door Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Meer informatie over de werking van Access vindt u in de verschillen tussen een machtiging, een rol en een bereik, zoals gedefinieerd door Azure RBAC.

- **Machtiging**: een machtiging is een gedefinieerde toegang tot een specifieke actie. Een machtiging kan bijvoorbeeld lees toegang zijn tot alle virtuele machines.
- **Rol**: een rol is een set machtigingen die kan worden gegroepeerd en toegewezen aan een gebruiker. Een gebruiker met de rol eigenaar van abonnement heeft bijvoorbeeld toegang tot alle resources in een abonnement.
- **Bereik**: een bereik is een niveau binnen de hiërarchie van een Azure-resource. Een bereik kan bijvoorbeeld een resource groep, één Lab of het hele abonnement zijn.

Binnen het bereik van DevTest Labs zijn er twee soorten rollen die gebruikers machtigingen definiëren:

- **Lab-eigenaar**: een Lab-eigenaar heeft toegang tot alle resources in het lab. Een Lab-eigenaar kan beleid wijzigen, lezen en schrijven naar Vm's, het virtuele netwerk wijzigen, enzovoort.
- **Lab-gebruiker**: een Lab-gebruiker kan alle Lab-resources weer geven, zoals vm's, beleids regels en virtuele netwerken. Een test gebruiker kan echter geen beleids regels of Vm's wijzigen die door andere gebruikers zijn gemaakt.

U kunt ook aangepaste rollen maken in DevTest Labs. Zie [gebruikers machtigingen verlenen aan specifieke Lab-beleids regels](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)voor meer informatie over het maken van aangepaste rollen in DevTest Labs.

Omdat scopes hiërarchisch zijn, wanneer een gebruiker machtigingen heeft voor een bepaald bereik, wordt de gebruiker automatisch die machtigingen verleend op elk bereik op lager niveau binnen het bereik. Als een gebruiker bijvoorbeeld de rol van abonnements eigenaar heeft toegewezen, heeft de gebruiker toegang tot alle resources in een abonnement. Deze resources omvatten Vm's, virtuele netwerken en Labs. De eigenaar van een abonnement neemt automatisch de rol van de Lab-eigenaar over. Het tegenovergestelde is echter niet waar. Een Lab-eigenaar heeft toegang tot een lab. Dit is een lager bereik dan het abonnements niveau. Een Lab-eigenaar kan dus geen Vm's, virtuele netwerken of andere bronnen die zich buiten het lab bevinden, kunnen zien.

### <a name="how-do-i-define-azure-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Hoe kan ik op rollen gebaseerd toegangs beheer voor mijn DevTest Labs definiëren om ervoor te zorgen dat IT kan bepalen terwijl ontwikkel aars/tests hun werk kunnen doen?
Er is een breed patroon, maar de details zijn afhankelijk van uw organisatie.

U moet er alleen voor zorgen dat de gegevens van het project en toepassings teams het benodigde beheer niveau hebben. Normaal gesp roken betekent het dat de centrale IT eigenaar is van het abonnement en dat de kern IT-functies, zoals netwerk configuraties, worden afgehandeld. De set **eigen aars** voor een abonnement moet klein zijn. Deze eigen aren kunnen extra eigen aren benoemen wanneer dit nodig is, of beleids regels op abonnements niveau Toep assen, bijvoorbeeld ' geen openbaar IP-adres '.

Mogelijk is er een subset van gebruikers die toegang nodig hebben tot meerdere abonnementen, zoals Tier1 of Tier 2-ondersteuning. In dit geval wordt u aangeraden deze gebruikers de toegang tot de **Inzender** te geven, zodat ze de resources kunnen beheren, maar geen gebruikers toegang bieden of beleids regels aanpassen.

De DevTest Labs-resource moet eigendom zijn van eigen aren die dicht bij het project/toepassings team vallen. Dit is omdat ze inzicht hebben in hun vereisten voor machines en de vereiste software. In de meeste organisaties is de eigenaar van deze DevTest Labs resource meestal het project/de ontwikkelings lead. Deze eigenaar kan gebruikers en beleids regels in de test omgeving beheren en alle virtuele machines in de DevTest Labs-omgeving beheren.

De leden van het project/het toepassings team moeten worden toegevoegd aan de gebruikersrol **DevTest Labs** . Deze gebruikers kunnen virtuele machines maken (op basis van de beleids regels Lab en abonnement op abonnements niveau). Ze kunnen ook hun eigen virtuele machines beheren. Ze kunnen geen virtuele machines beheren die deel uitmaken van andere gebruikers.

Zie voor meer informatie [Azure Enter prise-steigers-documentatie over het beheer van voorgeschreven abonnementen](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Hoe kan ik een rol maken om gebruikers toe te staan een specifieke taak uit te voeren?
Zie [gebruikers machtigingen verlenen aan specifieke Lab-beleids regels](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)voor een uitgebreid artikel over het maken van aangepaste rollen en het toewijzen van machtigingen aan een rol. Hier volgt een voor beeld van een script dat de functie **DevTest Labs Advanced User** maakt, die toestemming heeft om alle virtuele machines in het lab te starten en te stoppen:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Hoe kan een organisatie ervoor zorgen dat beveiligings beleid voor ondernemingen wordt toegepast?
Een organisatie kan dit doen door de volgende acties uit te voeren:

- Het ontwikkelen en publiceren van een uitgebreid beveiligings beleid. Het beleid heeft betrekking op de regels van acceptabel gebruik die zijn gekoppeld aan het gebruik van software, Cloud activa. Ook wordt gedefinieerd wat het beleid duidelijk schendt.
- Ontwikkel een aangepaste installatie kopie, aangepaste artefacten en een implementatie proces dat indeling toestaat binnen de beveiligings-realm die is gedefinieerd met Active Directory. Deze benadering dwingt de bedrijfs grens af en stelt een gemeen schappelijke set omgevings controles in. Deze besturings elementen voor de omgeving van een ontwikkelaar kunnen overwegen bij het ontwikkelen en volgen van een beveiligde ontwikkelings levenscyclus als onderdeel van hun algemene proces. Het doel is ook om een omgeving te bieden die niet meer beperkend is en die de ontwikkeling kan belemmeren, maar een redelijke set besturings elementen. De groeps beleidsregels in de organisatie-eenheid (OE) die virtuele machines van het Lab bevat, kunnen een subset zijn van het totale groeps beleid dat in productie is gevonden. Het is ook mogelijk dat ze een extra set zijn om geïdentificeerde Risico's goed te kunnen beperken.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Hoe kan een organisatie gegevens integriteit garanderen om ervoor te zorgen dat externe ontwikkel aars de code niet kunnen verwijderen of malware of niet-goedgekeurde software introduceren?
Er zijn verschillende lagen waarmee u de dreiging kunt beperken van externe consultants, aannemers of werk nemers die extern zijn in om samen te werken in DevTest Labs.

Zoals eerder vermeld, moet de eerste stap een acceptabel beleid voor gebruik hebben opgesteld en gedefinieerd, waardoor duidelijk de gevolgen worden aangegeven wanneer iemand het beleid schendt.

De eerste laag van besturings elementen voor externe toegang is het Toep assen van een beleid voor externe toegang via een VPN-verbinding die niet rechtstreeks is verbonden met het lab.

De tweede laag van besturings elementen is het Toep assen van een set groeps beleidsobjecten die het kopiëren en plakken via extern bureau blad verhinderen. Een netwerk beleid kan worden geïmplementeerd om uitgaande services van de omgeving, zoals FTP-en RDP-Services, niet uit de omgeving toe te staan. Door de gebruiker gedefinieerde route ring kan alle Azure-netwerk verkeer terugbrengen naar on-premises, maar de route ring kan niet worden ingesteld voor alle Url's die het uploaden van gegevens mogelijk toestaan, tenzij deze worden beheerd via een proxy om inhoud en sessies te scannen. Open bare Ip's kunnen worden beperkt binnen het virtuele netwerk dat DevTest Labs ondersteunt, zodat er geen externe netwerk bron kan worden overbrugd.

Uiteindelijk moet hetzelfde type beperkingen worden toegepast in de hele organisatie, waarbij rekening wordt gehouden met alle mogelijke methoden van Verwissel bare media of externe Url's die een bericht van inhoud kunnen accepteren. Neem contact op met uw Security Professional om een beveiligings beleid te controleren en te implementeren. Zie [micro soft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)(Engelstalig) voor meer aanbevelingen.

## <a name="lab-configuration"></a>Lab-configuratie

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Hoe kan ik een lab maken op basis van een resource manager-sjabloon?
We bieden een [github-opslag plaats van lab Azure Resource Manager-sjablonen](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) die u kunt implementeren of aanpassen om aangepaste sjablonen voor uw Labs te maken. Elke sjabloon bevat een koppeling voor het implementeren van het lab in uw eigen Azure-abonnement. Of u kunt de sjabloon aanpassen en [implementeren met behulp van Power shell of Azure cli](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Kan ik alle virtuele machines in een gemeen schappelijke resource groep maken in plaats van elke machine in een eigen resource groep?
Ja, als eigenaar van het lab kunt u de toewijzing van resource groepen door de Lab laten verwerken of alle virtuele machines die zijn gemaakt in een gemeen schappelijke resource groep die u opgeeft.

Scenario voor afzonderlijke resource groep:
-   In DevTest Labs wordt een nieuwe resource groep gemaakt voor elke open bare en persoonlijke IP-virtuele machine die u omhoog draait
-   DevTest Labs maakt een resource groep voor gedeelde IP-computers die tot dezelfde grootte behoren.

Scenario voor een gemeen schappelijke resource groep:
-   Alle virtuele machines zijn in de gemeen schappelijke resource groep die u opgeeft. Meer informatie over [de toewijzing van resource groepen voor het lab](./resource-group-control.md).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Hoe kan ik een naamgevings Conventie in mijn DevTest Labs-omgeving behouden?
U kunt de huidige ondernemings naamgevings conventies uitbreiden naar Azure-bewerkingen en ze consistent maken in de DevTest Labs-omgeving. Wanneer u DevTest Labs implementeert, is het raadzaam om specifiek start beleid te gebruiken. U implementeert dit beleid door een centraal script en JSON-sjabloon om consistentie af te dwingen. Naamgevings beleid kan worden geïmplementeerd via Azure-beleid dat wordt toegepast op het abonnements niveau. Zie Azure Policy-voor [beelden](../governance/policy/samples/index.md)voor json-voor beelden voor Azure Policy.

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Hoeveel Labs kan ik onder hetzelfde abonnement maken?
Er is geen specifieke limiet voor het aantal Labs dat per abonnement kan worden gemaakt. De hoeveelheid resources die per abonnement wordt gebruikt, is echter beperkt. Meer informatie over de [limieten en quota voor Azure-abonnementen](../azure-resource-manager/management/azure-subscription-service-limits.md) en [hoe u deze limieten kunt verhogen](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Hoeveel Vm's kan ik per Lab maken?
Er is geen specifieke limiet voor het aantal Vm's dat per Lab kan worden gemaakt. De bronnen (VM-kernen, open bare IP-adressen, enzovoort) die worden gebruikt, zijn echter beperkt per abonnement. Meer informatie over de [limieten en quota voor Azure-abonnementen](../azure-resource-manager/management/azure-subscription-service-limits.md) en [hoe u deze limieten kunt verhogen](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Hoe kan ik de verhouding van gebruikers per Lab en het totale aantal Labs dat nodig is voor een hele organisatie bepalen?
We raden u aan om de bedrijfs units en ontwikkelings groepen die zijn gekoppeld aan hetzelfde ontwikkel project te koppelen aan hetzelfde lab. U kunt dezelfde typen beleids regels, installatie kopieën en afsluit beleid Toep assen op beide groepen.

Mogelijk moet u ook geografische grenzen overwegen. Ontwikkel aars in het Noordoost-Verenigde Staten (Verenigde Staten) kunnen bijvoorbeeld een Lab gebruiken dat is ingericht in Oost-VS2. En ontwikkel aars in Amsterdam, Texas en Denver kunnen Colorado worden omgeleid om een resource in VS Zuid-Centraal te gebruiken. Als er sprake is van gezamenlijke inspanningen met een externe derde partij, kunnen ze worden toegewezen aan een lab dat niet wordt gebruikt door interne ontwikkel aars.

U kunt ook een Lab voor een specifiek project in Azure DevOps Projects gebruiken. Vervolgens past u beveiliging toe via een opgegeven Azure Active Directory groep, waarmee u toegang kunt krijgen tot beide sets resources. Het virtuele netwerk dat aan het lab is toegewezen, kan een andere grens zijn om gebruikers te consolideren.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Hoe kan ik voor komen dat resources binnen een Lab worden verwijderd?
We raden u aan om de juiste machtigingen op het lab-niveau in te stellen, zodat alleen geautoriseerde gebruikers resources kunnen verwijderen of het lab-beleid wijzigen. Ontwikkel aars moeten worden geplaatst in de **DevTest Labs-gebruikers** groep. De lead ontwikkelaar of de lead van de infra structuur moet de eigenaar van de **DevTest Labs** zijn. We raden u aan slechts twee Lab-eigen aars te hebben. Dit beleid strekt zich naar de code opslagplaats om beschadiging te voor komen. Lab-gebruikers hebben rechten om resources te gebruiken, maar kunnen geen Lab-beleids regels bijwerken. Raadpleeg het volgende artikel waarin de rollen en rechten worden vermeld die elke ingebouwde groep binnen een Lab heeft: [eigen aren en gebruikers toevoegen in azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Hoe kan ik een directe koppeling naar mijn lab delen?

1. Ga in het [Azure Portal](https://portal.azure.com)naar het lab.
2. Kopieer de **Lab-URL** vanuit uw browser en deel deze met uw test gebruikers.

> [!NOTE]
> Als een Lab-gebruiker een externe gebruiker is die een Microsoft-account heeft, maar die geen lid is van de Active Directory instantie van uw organisatie, ziet de gebruiker mogelijk een fout bericht wanneer ze proberen toegang te krijgen tot de gedeelde koppeling. Als een externe gebruiker een fout bericht ziet, vraagt u de gebruiker eerst de naam te selecteren in de rechter bovenhoek van de Azure Portal. Vervolgens kan de gebruiker in de sectie Directory van het menu de map selecteren waarin het lab zich bevindt.

## <a name="virtual-machines"></a>Virtuele machines

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Waarom kan ik geen Vm's zien op de Virtual Machines-pagina die ik in DevTest Labs Zie?
Wanneer u een virtuele machine in DevTest Labs maakt, krijgt u toegang tot die virtuele machine. U kunt de virtuele machine weer geven op de pagina Labs en op de pagina **virtual machines** . Gebruikers die zijn toegewezen aan de rol van de eigenaar van de **DevTest Labs** kunnen alle virtuele machines zien die zijn gemaakt in de test omgeving op de pagina **alle virtual machines** van Lab. Gebruikers met de gebruikersrol **DevTest Labs** krijgen echter niet automatisch Lees toegang tot VM-bronnen die andere gebruikers hebben gemaakt. Deze Vm's worden dus niet weer gegeven op de pagina **virtual machines** .


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Hoe kan ik meerdere Vm's tegelijk maken op basis van dezelfde sjabloon?
U hebt twee opties voor het tegelijkertijd maken van meerdere Vm's vanuit dezelfde sjabloon:

- U kunt de [extensie Azure DevOps-taken](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)gebruiken.
- U kunt [een resource manager-sjabloon genereren](devtest-lab-add-vm.md#save-azure-resource-manager-template) tijdens het maken van een virtuele machine en [de Resource Manager-sjabloon implementeren vanuit Windows Power shell](../azure-resource-manager/templates/deploy-powershell.md).
- U kunt ook meer dan één exemplaar van een machine opgeven die moet worden gemaakt tijdens het maken van de virtuele machine. Zie voor meer informatie over het maken van meerdere exemplaren van virtuele machines het document over het [maken van een Lab-virtuele machine](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Hoe kan ik mijn bestaande Azure-Vm's verplaatsen naar mijn DevTest Labs Lab?
Uw bestaande Vm's kopiëren naar DevTest Labs:

1.  Kopieer het VHD-bestand van uw bestaande virtuele machine met behulp van een [Windows Power shell-script](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Maak de [aangepaste installatie kopie](devtest-lab-create-template.md) in uw DevTest Labs Lab.
3.  Maak een virtuele machine in het lab vanuit uw aangepaste installatie kopie.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Kan ik meerdere schijven aan mijn Vm's koppelen?

Ja, u kunt meerdere schijven koppelen aan uw Vm's.

### <a name="are-gen-2-images-supported-by-devtest-labs"></a>Worden gen 2-afbeeldingen ondersteund door DevTest Labs?
Ja. De DevTest Labs-service ondersteunt [gen 2-installatie kopieën](../virtual-machines/generation-2.md). Als zowel de versies van 1 en Diversen 2 beschikbaar zijn voor een installatie kopie, wordt in DevTest Labs echter alleen de versie 1 van de installatie kopie weer gegeven bij het maken van een virtuele machine. U ziet de installatie kopie als er slechts een versie van gen 2 beschikbaar is. 

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Als ik een installatie kopie van een Windows-besturings systeem wil gebruiken voor mijn tests, moet ik dan een MSDN-abonnement aanschaffen?
Voer een van de volgende stappen uit om Windows-client installatie kopieën (Windows 7 of een hogere versie) te gebruiken voor het ontwikkelen of testen in Azure:

- [Koop een MSDN-abonnement](https://www.visualstudio.com/products/how-to-buy-vs).
- Als u een Enterprise Agreement hebt, maakt u een Azure-abonnement met de [Enterprise dev/test aanbieding](https://azure.microsoft.com/offers/ms-azr-0148p).

Voor meer informatie over de Azure-tegoeden voor elke MSDN-aanbieding raadpleegt u het [maandelijkse Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Hoe kan ik het proces van het verwijderen van alle virtuele machines in mijn lab automatiseren?
Als eigenaar van het lab kunt u Vm's uit uw Lab verwijderen in de Azure Portal. U kunt ook alle virtuele machines in uw Lab verwijderen met behulp van een Power shell-script. In het volgende voor beeld, onder de **waarde voor het wijzigen** van de opmerking, wijzigt u de parameter waarden. U kunt de `subscriptionId` waarden, `labResourceGroup` en, ophalen `labName` uit het deel venster lab in de Azure Portal.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Omgevingen

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Hoe kan Ik Resource Manager-sjablonen in mijn DevTest Labs-omgeving gebruiken?
U implementeert uw Resource Manager-sjablonen in een DevTest Labs-omgeving met behulp van de stappen die worden beschreven in de [functie omgevingen in DevTest Labs](devtest-lab-test-env.md) -artikel. In principe controleert u uw Resource Manager-sjablonen in een Git-opslag plaats (Azure opslag plaatsen of GitHub) en voegt u een [privé-opslag plaats voor uw sjablonen](devtest-lab-test-env.md) toe aan het lab. Dit scenario is mogelijk niet nuttig als u DevTest Labs gebruikt om ontwikkel machines te hosten, maar dit kan handig zijn als u een faserings omgeving bouwt die representatief is voor productie.

Het is ook een goed idee dat voor het aantal virtuele machines per Lab of per gebruiker alleen het aantal machines wordt beperkt dat systeem eigen is gemaakt in het lab zelf, en niet door een wille keurige omgeving (Resource Manager-sjablonen).

## <a name="custom-images"></a>Aangepaste installatiekopieën

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Hoe kan ik een eenvoudig herhaalbaar proces instellen om mijn aangepaste organisatie-installatie kopieën naar een DevTest Labs-omgeving te brengen?
Bekijk deze [video over het patroon van de afbeeldings fabriek](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Dit scenario is een geavanceerd scenario en de meegeleverde scripts zijn alleen voorbeeld scripts. Als er wijzigingen zijn vereist, moet u de scripts die in uw omgeving worden gebruikt, beheren en onderhouden.

Voor gedetailleerde informatie over het maken van een image Factory, Zie [een aangepaste installatie kopie maken Factory in azure DevTest Labs](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Wat is het verschil tussen een aangepaste installatie kopie en een formule?
Een aangepaste installatie kopie is een beheerde installatie kopie. Een formule is een installatie kopie die u kunt configureren met aanvullende instellingen en vervolgens opslaat en reproduceert. Een aangepaste installatie kopie kan de voor keur hebben als u snel verschillende omgevingen wilt maken met behulp van dezelfde basis, onveranderlijke afbeelding. Een formule kan beter zijn als u de configuratie van uw virtuele machine wilt reproduceren met de meest recente bits, als onderdeel van een virtueel netwerk of subnet, of als een virtuele machine van een bepaalde grootte. Zie [aangepaste afbeeldingen en formules vergelijken in DevTest Labs](devtest-lab-comparing-vm-base-image-types.md)voor een uitgebreidere uitleg.

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Wanneer moet ik een formule versus aangepaste afbeelding gebruiken?
De beslissings factor in dit scenario is doorgaans kosten en hergebruik. Als u een scenario hebt waarbij veel gebruikers/Labs een installatie kopie met veel software boven op de basis installatie kopie vereisen, kunt u de kosten verlagen door een aangepaste installatie kopie te maken. Dit betekent dat de installatie kopie eenmaal is gemaakt. Het vermindert de instel tijd van de virtuele machine en de kosten die zijn gemaakt door de virtuele machine die wordt uitgevoerd wanneer de installatie plaatsvindt.

Een extra te noteren factor is echter de frequentie van wijzigingen in uw software pakket. Als u dagelijks bouwt en vereist dat de software op de virtuele machines van uw gebruikers wordt uitgevoerd, kunt u overwegen een formule te gebruiken in plaats van een aangepaste installatie kopie.

Zie [aangepaste afbeeldingen en formules vergelijken](devtest-lab-comparing-vm-base-image-types.md) in DevTest Labs voor een uitgebreidere uitleg.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Hoe kan ik het proces van het uploaden van VHD-bestanden automatiseren om aangepaste installatie kopieën te maken?

Als u het uploaden van VHD-bestanden voor het maken van aangepaste installatie kopieën wilt automatiseren, hebt u twee opties:

- Gebruik [AzCopy](../storage/common/storage-use-azcopy-v10.md) om VHD-bestanden te kopiëren of te uploaden naar het opslag account dat is gekoppeld aan het lab.
- Gebruik [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer is een zelfstandige app die wordt uitgevoerd op Windows, OS X en Linux.

Zoeken naar het doel-opslag account dat is gekoppeld aan uw Lab:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com).
2.  Selecteer **resource groepen** in het menu links.
3.  Zoek en selecteer de resource groep die is gekoppeld aan uw Lab.
4.  Selecteer een van de opslag accounts onder **overzicht**.
5.  Selecteer **blobs**.
6.  Zoek naar uploads in de lijst. Als er geen bestaat, keert u terug naar stap 4 en probeert u een ander opslag account.
7.  Gebruik de **URL** als doel in uw AzCopy-opdracht.

Wanneer moet ik een Azure Marketplace-installatie kopie gebruiken versus mijn eigen aangepaste organisatie-installatie kopie?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Wanneer moet ik een Azure Marketplace-installatie kopie gebruiken versus mijn eigen aangepaste organisatie-installatie kopie?
Azure Marketplace moet standaard worden gebruikt, tenzij u specifieke problemen of organisatorische vereisten hebt. Enkele algemene voor beelden zijn:

- Installatie van complexe software waarvoor een toepassing moet worden opgenomen als onderdeel van de basis installatie kopie.
- Installatie en installatie van een toepassing kan veel uur duren, wat geen efficiënt gebruik van reken tijd kan worden toegevoegd aan een Azure Marketplace-installatie kopie.
- Ontwikkel aars en testers hebben snel toegang tot een virtuele machine nodig en willen de instel tijd van een nieuwe virtuele machine minimaliseren.
- Nalevings-of regelgeving (bijvoorbeeld beveiligings beleid) die voor alle machines aanwezig moet zijn.
- Het gebruik van aangepaste installatie kopieën mag niet lichter worden beschouwd. Ze introduceren extra complexiteit, omdat u nu VHD-bestanden moet beheren voor de onderliggende basis installatie kopieën. U moet deze basis installatie kopieën ook regel matig patchen met software-updates. Deze updates omvatten nieuwe besturings systemen (OS)-updates en eventuele updates of configuratie wijzigingen die nodig zijn voor het software pakket zelf.

## <a name="artifacts"></a>Artifacts

### <a name="what-are-artifacts"></a>Wat zijn artefacten?
Artefacten zijn aanpas bare elementen die u kunt gebruiken om uw nieuwste bits te implementeren of uw ontwikkel hulpprogramma's te implementeren op een VM. Koppel artefacten aan uw virtuele machine wanneer u de virtuele machine maakt. Nadat de virtuele machine is ingericht, worden de artefacten geïmplementeerd en geconfigureerd voor uw VM. Verschillende bestaande artefacten zijn beschikbaar in onze [open bare github-opslag plaats](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). U kunt ook [uw eigen artefacten ontwerpen](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mijn artefact is mislukt tijdens het maken van de VM. Hoe kan ik problemen oplossen?
Zie [artefact fouten in DevTest Labs diagnosticeren](devtest-lab-troubleshoot-artifact-failure.md)voor meer informatie over het ophalen van Logboeken voor uw mislukte artefact.

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Wanneer moet een organisatie een open bare artefact opslagplaats versus een persoonlijke artefact opslagplaats in DevTest Labs gebruiken?
De [opslag plaats voor open bare artefacten](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) bevat een initiële set software pakketten die het meest worden gebruikt. Het helpt bij snelle implementatie zonder dat u tijd hoeft te investeren om algemene ontwikkel hulpprogramma's en-invoeg toepassingen te reproduceren. U kunt ervoor kiezen om hun eigen privé-opslag plaats te implementeren. U kunt een open bare en een privé opslag plaats parallel gebruiken. U kunt er ook voor kiezen om de open bare opslag plaats uit te scha kelen. De criteria voor het implementeren van een privé-opslag plaats moeten worden aangestuurd door de volgende vragen en overwegingen:

- Heeft de organisatie een vereiste om software met een bedrijfs licentie te hebben als onderdeel van hun DevTest Labs-aanbieding? Als het antwoord ja is, moet er een privé opslagplaats worden gemaakt.
- Ontwikkelt de organisatie aangepaste software die een specifieke bewerking biedt, die vereist is als onderdeel van het algemene inrichtings proces? Als het antwoord ja is, moet een privé opslagplaats worden geïmplementeerd.
- Als het governance beleid van de organisatie isolatie vereist en externe opslag plaatsen niet worden beheerd door de organisatie, moet een opslag plaats voor persoonlijke artefacten worden geïmplementeerd. Als onderdeel van dit proces kan een eerste kopie van de open bare opslag plaats worden gekopieerd en geïntegreerd met de privé-opslag plaats. Vervolgens kan de open bare opslag plaats worden uitgeschakeld, zodat niemand binnen de organisatie er meer toegang toe heeft. Deze aanpak zorgt ervoor dat alle gebruikers binnen de organisatie slechts één opslag plaats hebben die door de organisatie is goedgekeurd en de configuratie drift minimaliseert.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Moet een organisatie plannen voor één opslag plaats of meerdere opslag plaatsen toestaan?
Als onderdeel van de algehele beheer-en configuratie strategie van uw organisatie wordt u aangeraden een gecentraliseerde opslag plaats te gebruiken. Wanneer u meerdere opslag plaatsen gebruikt, kunnen ze gedurende de periode een silo van onbeheerde software worden. Met een centrale opslag plaats kunnen meerdere teams artefacten uit deze opslag plaats gebruiken voor hun projecten. Het dwingt de standaardisatie af, de beveiliging, het gemak van het beheer en elimineert het dupliceren van inspanningen. Als onderdeel van de gecentraliseerde zijn de volgende acties aanbevolen procedures voor het beheer en duurzaamheid op lange termijn:

- Koppel de Azure-opslag plaatsen aan dezelfde Azure Active Directory-Tenant die door het Azure-abonnement wordt gebruikt voor verificatie en autorisatie.
- Maak een groep met de naam `All DevTest Labs Developers` in azure Active Directory die centraal wordt beheerd. Ontwikkel aars die bijdraagt aan het ontwikkelen van artefacten, moeten in deze groep worden geplaatst.
- Dezelfde Azure Active Directory groep kan worden gebruikt om toegang te bieden tot de Azure opslag plaatsen-opslag plaats en het lab.
- In azure opslag plaatsen moet vertakkingen of forking worden gebruikt voor het scheiden van een opslag plaats in ontwikkeling van de primaire productie opslagplaats. Inhoud wordt alleen toegevoegd aan de hoofd vertakking met een pull-aanvraag nadat de juiste code is gecontroleerd. Zodra de code revisor de wijziging heeft goedgekeurd, wordt een lead ontwikkelaar, die verantwoordelijk is voor het onderhoud van de hoofd vertakking, samengevoegd met de bijgewerkte code.

## <a name="cicd-integration"></a>CI/CD-integratie

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Is DevTest Labs geïntegreerd met mijn CI/CD-hulpprogramma keten?
Als u Azure DevOps gebruikt, kunt u een [uitbrei ding van DevTest Labs-taken](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) gebruiken om uw release pijplijn in DevTest Labs te automatiseren. Enkele van de taken die u met deze extensie kunt uitvoeren, zijn onder andere:

- Maak en implementeer automatisch een virtuele machine. U kunt de virtuele machine ook configureren met de nieuwste versie met behulp van Azure file copy of Power shell Azure DevOps Services-taken.
- Automatisch de status van een virtuele machine vastleggen na het testen om een fout op dezelfde VM te reproduceren voor verder onderzoek.
- Verwijder de virtuele machine aan het einde van de release pijplijn, wanneer deze niet meer nodig is.

De volgende blog berichten bieden richt lijnen en informatie over het gebruik van de Azure DevOps Services-extensie:

- [DevTest Labs en de Azure DevOps-extensie](integrate-environments-devops-pipeline.md)
- [Een nieuwe virtuele machine implementeren in een bestaand DevTest Labs Lab van Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Release beheer van Azure DevOps Services gebruiken voor continue implementaties naar DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Voor de/Continuous Delivery (CD) toolchains van andere doorlopende integratie (CI), kunt u dezelfde scenario's gebruiken door [Azure Resource Manager sjablonen](https://azure.microsoft.com/resources/templates/) te implementeren met behulp van [Azure PowerShell-cmdlets](../azure-resource-manager/templates/deploy-powershell.md) en [.net sdk's](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). U kunt ook [rest-api's gebruiken voor DevTest Labs](https://aka.ms/dtlrestapis) om te integreren met uw hulpprogramma keten.

## <a name="networking"></a>Netwerken

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Wanneer moet ik een nieuw virtueel netwerk maken voor mijn DevTest Labs-omgeving versus een bestaand virtueel netwerk gebruiken?
Als uw Vm's moeten communiceren met de bestaande infra structuur, kunt u overwegen om een bestaand virtueel netwerk in uw DevTest Labs-omgeving te gebruiken. Als u ExpressRoute gebruikt, wilt u mogelijk het aantal virtuele netwerken/subnetten minimaliseren, zodat u de IP-adres ruimte die wordt toegewezen voor gebruik in de abonnementen niet hoeft te fragmenteren.

Overweeg hier het gebruik van het peering-patroon van het virtuele netwerk ([hub-spoke model](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) te gebruiken. Deze aanpak maakt vnet/subnet-communicatie mogelijk tussen abonnementen. Anders kan elke DevTest Labs-omgeving een eigen virtueel netwerk hebben.

Er zijn [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md) voor het aantal virtuele netwerken per abonnement. De standaard waarde is 50, maar deze limiet kan worden verhoogd naar 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Wanneer moet ik een gedeeld IP-adres versus een openbaar IP-adres versus particulier IP-adres gebruiken?

Als u een site-naar-site-VPN of Express route gebruikt, kunt u het gebruik van privé IP-adressen overwegen, zodat uw computers toegankelijk zijn via uw interne netwerk en niet toegankelijk zijn via het open bare Internet.

> [!NOTE]
> Lab-eigen aars kunnen dit subnetbeleid wijzigen om ervoor te zorgen dat niemand per ongeluk open bare IP-adressen voor hun virtuele machines maakt. De eigenaar van het abonnement moet een abonnements beleid maken voor komen dat open bare IP-adressen worden gemaakt.

Wanneer u gedeelde open bare Ip's gebruikt, delen de virtuele machines in een lab een openbaar IP-adres. Deze aanpak kan handig zijn wanneer u wilt voor komen dat de limieten voor open bare IP-adressen voor een bepaald abonnement worden geschonden.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Hoe kan ik ervoor te zorgen dat de ontwikkelings-en test-virtuele machines het open bare Internet niet kunnen bereiken? Zijn er aanbevolen patronen voor het instellen van de netwerk configuratie?

Ja. Er zijn twee aspecten die u kunt overwegen: inkomend en uitgaand verkeer.

- **Binnenkomend verkeer** : als de virtuele machine geen openbaar IP-adres heeft, kan het niet worden bereikt door Internet. Een veelvoorkomende aanpak is om ervoor te zorgen dat er een beleid op abonnements niveau is ingesteld, zodat geen enkele gebruiker een openbaar IP-adres kan maken.
- **Uitgaand verkeer** : als u wilt voor komen dat virtuele machines rechtstreeks toegang krijgen tot het open bare Internet en verkeer via een bedrijfs firewall af te dwingen, kunt u verkeer on-premises via Express route of VPN routeren met behulp van geforceerde route ring.

> [!NOTE]
> Als u een proxy server hebt die verkeer blokkeert zonder proxy instellingen, vergeet dan niet om uitzonde ringen toe te voegen aan het account voor artefact opslag van het lab.

U kunt ook netwerk beveiligings groepen gebruiken voor virtuele machines of subnetten. Deze stap voegt een extra beveiligingslaag toe om verkeer toe te staan of te blok keren.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Waarom wordt mijn bestaande virtuele netwerk niet op de juiste wijze opgeslagen?
Een van de mogelijkheden is dat de naam van uw virtuele netwerk punten bevat. Als dat het geval is, kunt u de peri Oden verwijderen of vervangen door afbreek streepjes. Probeer vervolgens opnieuw om het virtuele netwerk op te slaan.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Waarom wordt de fout ' bovenliggende resource niet gevonden ' weer gegeven wanneer ik een virtuele machine inrich vanuit Power shell?
Wanneer een resource een bovenliggend item is voor een andere resource, moet de bovenliggende resource bestaan voordat u de onderliggende resource maakt. Als de bovenliggende resource niet bestaat, wordt er een **ParentResourceNotFound** -bericht weer gegeven. Als u geen afhankelijkheid opgeeft voor de bovenliggende resource, kan de onderliggende resource worden geïmplementeerd vóór het bovenliggende item.

Vm's zijn onderliggende resources onder een lab in een resource groep. Wanneer u Resource Manager-sjablonen gebruikt om Vm's te implementeren met behulp van Power shell, moet de naam van de resource groep in het Power shell-script de naam van de resource groep van het lab zijn. Zie [problemen met algemene Azure-implementatie fouten oplossen](../azure-resource-manager/templates/common-deployment-errors.md)voor meer informatie.

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Waar vind ik meer fout informatie als een VM-implementatie mislukt?
VM-implementatie fouten worden vastgelegd in activiteiten Logboeken. U kunt activiteiten logboeken voor Lab-VM'S vinden onder **audit logboeken** of **virtuele machine Diagnostics** in het menu resource op de VM-pagina van het lab (de pagina wordt weer gegeven nadat u de virtuele machine hebt geselecteerd in de lijst mijn vm's).

Soms treedt er een implementatie fout op voordat de implementatie van de VM begint. Een voor beeld is het overschrijden van de abonnements limiet voor een resource die is gemaakt met de virtuele machine. In dit geval worden de fout Details vastgelegd in de activiteiten logboeken op Lab-niveau. Activiteiten logboeken bevinden zich onder aan de instellingen voor **configuratie en beleid** . Zie [activiteiten logboeken weer geven om acties op resources te controleren](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over het gebruik van activiteiten Logboeken in Azure.

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Waarom krijg ik de fout ' locatie is niet beschikbaar voor resource type ' bij het maken van een Lab?
Er wordt mogelijk een fout bericht van de volgende strekking weer gegeven wanneer u probeert een lab te maken:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

U kunt deze fout oplossen door een van de volgende stappen uit te voeren:

#### <a name="option-1"></a>Optie 1
Controleer de beschik baarheid van het resource type in azure-regio's op de pagina [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/) . Als het resource type niet beschikbaar is in een bepaalde regio, biedt DevTest Labs geen ondersteuning voor het maken van een lab in die regio. Selecteer een andere regio bij het maken van uw Lab.

#### <a name="option-2"></a>Optie 2
Als het bron type beschikbaar is in uw regio, controleert u of het is geregistreerd bij uw abonnement. U kunt dit doen op het niveau van de abonnements eigenaar, zoals wordt weer gegeven in [dit artikel](../azure-resource-manager/management/resource-providers-and-types.md).
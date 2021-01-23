---
title: Resource Health voor Cloud Services (klassiek)
description: Dit artikel spreekt over Resource Health Check-ondersteuning (RHC) voor Microsoft Azure Cloud Services (klassiek)
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 37294e681066eb27ace69bcacee3a813b750b8eb
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743505"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Resource Health Check-ondersteuning (RHC) voor Azure Cloud Services (klassiek)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

Dit artikel spreekt over Resource Health Check-ondersteuning (RHC) voor [Microsoft Azure Cloud Services (klassiek)](https://azure.microsoft.com/services/cloud-services)

[Azure resource Health](../service-health/resource-health-overview.md) voor Cloud Services helpt u bij het vaststellen en verkrijgen van ondersteuning voor service problemen die van invloed zijn op de implementatie van uw Cloud service, rollen & rolinstanties. Het rapporteert over de huidige en eerdere status van uw Cloud Services tijdens de implementatie, Role & Role-instantie niveau.

Azure-status rapporten over problemen die van invloed zijn op een groot aantal Azure-klanten. Resource Health geeft u een aangepast dash board van de status van uw resources. Resource Health toont de tijden dat uw resources niet beschikbaar zijn vanwege Azure-Service problemen. Met deze gegevens kunt u eenvoudig zien of een SLA is geschonden.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Afbeelding toont de Blade resource status controle in de Azure Portal.":::

## <a name="how-health-is-checked-and-reported"></a>Hoe wordt de status gecontroleerd en gerapporteerd?
De resource status wordt gerapporteerd op het niveau van een implementatie of rol. De status controle wordt uitgevoerd op het niveau van de rolinstantie, het samen voegen en rapporteren op rollen niveau. Bijvoorbeeld Als alle rolinstanties beschikbaar zijn, is de functie status beschikbaar. Op dezelfde manier worden de integriteits status van alle rollen geaggregeerd en gerapporteerd op implementatie niveau. Bijvoorbeeld Als alle rollen beschikbaar zijn, wordt de status van de implementatie beschikbaar. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Waarom kan ik de status niet zien voor mijn implementatie van staging-sleuven?
Status controles van resources werken alleen voor implementatie van productie site. Implementatie van staging-sleuf wordt nog niet ondersteund. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>Controleert Resource Health controle ook de status van de toepassing?
Nee, de status controle is alleen van toepassing op rolinstanties en de status van toepassingen wordt niet bewaakt. Bijvoorbeeld Zelfs als 1 van de drie rolinstanties beschadigd is, kan de toepassing nog steeds beschikbaar zijn. RHC maakt geen gebruik van [Load Balancer tests](../load-balancer/load-balancer-custom-probe-overview.md) of de test van de gast agent. Daarom moeten klanten load balancer tests blijven gebruiken om de status van hun toepassing te bewaken. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Wat zijn de aantekeningen voor Cloud Services?
Annotaties zijn de integriteits status van de implementatie of rollen. Er zijn verschillende aantekeningen op basis van de status, de reden voor de status wijziging enzovoort. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>Wat betekent het dat het Role-exemplaar niet beschikbaar is?
Dit betekent dat de rolinstantie geen gezond signaal naar het platform verzendt. Controleer de status van het rolinstantie voor gedetailleerde uitleg over waarom een gezond signaal niet wordt uitgezonden.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>Wat betekent dat de implementatie ' onbekend ' is?
Onbekend betekent dat de geaggregeerde status van de Cloud service-implementatie niet kan worden bepaald. Dit betekent meestal dat er geen productie-implementatie is gemaakt voor de Cloud service, dat de implementatie nieuw is gemaakt (en dat Azure begint met het verzamelen van status gebeurtenissen) of het platform problemen ondervindt bij het verzamelen van status gebeurtenissen voor deze implementatie.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Waarom worden in de annotaties van Role-instanties Vm's vermeld in plaats van Rolinstanties?
Aangezien Rolinstanties voornamelijk Vm's zijn en de status controle voor Vm's opnieuw wordt gebruikt voor Rolinstanties, wordt de VM-term gebruikt om Rolinstanties te vertegenwoordigen. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Cloud Services (implementatie niveau) aantekeningen & hun betekenis
| Aantekening | Beschrijving | 
| --- | --- | 
| Beschikbaar| Er zijn geen bekende problemen met het Azure-platform die van invloed zijn op deze Cloud service-implementatie |
| Onbekend | Het is momenteel niet mogelijk de status van deze Cloud service-implementatie te bepalen | 
| Resource Health instellen | De resource status voor deze resource instellen. Resource Health houdt uw Azure-resources in de gaten met details over lopende en eerdere gebeurtenissen die invloed hebben op de activiteiten|
| Verminderd beschikbaar | Uw cloudservice-implementatie is gedegradeerd. Wij proberen uw cloudservice-implementatie automatisch te herstellen en de oorzaak van het probleem te bepalen. Er is op dit moment geen verdere actie vereist |
| Niet in orde | De implementatie van de Cloud service is niet in orde omdat {0} {1} er geen rolinstanties meer beschikbaar zijn |
| Verminderd beschikbaar | De implementatie van de Cloud service is verslechterd omdat {0} er geen {1} rolinstanties beschikbaar zijn | 
| Beschikbaar en mogelijk beïnvloed | De implementatie van de Cloud service wordt uitgevoerd, maar een voortdurende onderbreking van de Azure-service kan ervoor zorgen dat u geen verbinding kunt maken. De verbinding wordt hersteld zodra de onderbreking is opgelost |
| Niet beschikbaar en mogelijk van invloed | De status van deze Cloud service-implementatie kan worden beïnvloed door een onderbreking van de Azure-service. De implementatie van de Cloud service wordt automatisch hersteld wanneer de storing is opgelost |
| Onbekend en mogelijk van invloed | Het is momenteel niet mogelijk de status van deze Cloud service-implementatie te bepalen. Dit kan worden veroorzaakt door een voortdurende onderbreking van de Azure-service die van invloed kan zijn op deze virtuele machine, die automatisch wordt hersteld wanneer de storing is opgelost |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Cloud Services (niveau van Rolinstantie) aantekeningen & hun betekenis
| Aantekening | Beschrijving | 
| --- | --- | 
| Beschikbaar | Er zijn geen bekende problemen met het Azure-platform die van invloed zijn op deze virtuele machine | 
| Onbekend | Het is momenteel niet mogelijk de status van deze virtuele machine te bepalen |
| Gestopt en toewijzing ongedaan | Deze virtuele machine stopt en maakt de toewijzingen ongedaan zodra dit wordt aangevraagd door een geautoriseerde gebruiker of een proces |
| Resource Health instellen | De resource status voor deze resource instellen. Resource Health houdt uw Azure-resources in de gaten met details over lopende en eerdere gebeurtenissen die invloed hebben op de activiteiten |
| Niet beschikbaar | Uw virtuele machine is niet beschikbaar. Wij proberen om uw virtuele machine automatisch te herstellen en gaan na wat de oorzaak van het probleem is. Er is op dit moment geen verdere actie vereist |
| Verminderd beschikbaar | Uw virtuele machine is verminderd beschikbaar. Wij proberen om uw virtuele machine automatisch te herstellen en gaan na wat de oorzaak van het probleem is. Er is op dit moment geen verdere actie vereist |
| Hardwarestoring van hostserver | Deze virtuele machine wordt beïnvloed door een onherstelbare fout {HardwareCategory} op de hostserver. De virtuele machine wordt door Azure opnieuw geïmplementeerd op een goede hostserver |
| Migratie gepland vanwege gedegradeerde hardware | Er is vastgesteld dat de hostserver een gedegradeerde {0} heeft die naar verwachting binnenkort zal falen. Zo mogelijk wordt uw virtuele machine zo snel mogelijk live gemigreerd of na {1} UTC opnieuw geïmplementeerd. Om het risico voor uw service tot een minimum te beperken, raden we u aan om uw virtuele machine zo snel mogelijk zelf opnieuw te implementeren als de hardware uitvalt voordat de migratie wordt gestart. |
| Beschikbaar en mogelijk beïnvloed | Uw virtuele machine wordt uitgevoerd. een voortdurende onderbreking van de Azure-service kan er echter voor zorgen dat u geen verbinding kunt maken. De verbinding wordt hersteld zodra de onderbreking is opgelost |
| Niet beschikbaar en mogelijk van invloed | De status van deze virtuele machine kan worden beïnvloed door een onderbreking van de Azure-service. De virtuele machine wordt automatisch hersteld wanneer de storing is opgelost |
| Onbekend en mogelijk van invloed | Het is momenteel niet mogelijk de status van deze virtuele machine te bepalen. Dit kan worden veroorzaakt door een voortdurende onderbreking van de Azure-service die van invloed kan zijn op deze virtuele machine, die automatisch wordt hersteld wanneer de storing is opgelost |
| Toegewezen hardwarebronnen | Er zijn hardwarebronnen toegewezen aan de virtuele machine en deze is binnenkort online |
| Stoppen en toewijzing ongedaan te worden | Deze virtuele machine stopt en maakt de toewijzingen ongedaan zodra dit wordt aangevraagd door een geautoriseerde gebruiker of een proces |
| De configuratie wordt bijgewerkt | De configuratie van deze virtuele machine wordt bijgewerkt zodra dit wordt aangevraagd door een geautoriseerde gebruiker of een proces |
| Opnieuw opgestart door gebruiker | Deze virtuele machine start opnieuw op zodra dit wordt aangevraagd door een geautoriseerde gebruiker of een proces. Deze is weer online nadat het opnieuw opstarten is voltooid |
| Opnieuw implementeren naar een andere host | Deze virtuele machine wordt opnieuw geïmplementeerd op een andere host, zoals aangevraagd door een geautoriseerde gebruiker of geautoriseerd proces. Het is weer online nadat de implementatie is voltooid |
| Gestopt door gebruiker | Deze virtuele machine stopt zodra dit wordt aangevraagd door een geautoriseerde gebruiker of een proces |
| Gestopt door gebruiker of proces | Deze virtuele machine wordt gestopt zodra dit wordt aangevraagd door een geautoriseerde gebruiker of door een proces dat intern op de virtuele machine wordt uitgevoerd |
| Gestart door gebruiker | Deze virtuele machine start zodra dit wordt aangevraagd door een geautoriseerde gebruiker of een proces. Het is binnenkort online |
| Onderhoud opnieuw implementeren op een andere host | Deze virtuele machine wordt opnieuw geïmplementeerd op een andere hostserver als onderdeel van een geplande onderhoudsactiviteit. Het is weer online nadat de implementatie is voltooid |
| Opnieuw opstarten geïnitieerd vanaf de computer | Er is een herstart geactiveerd vanuit de virtuele machine. Dit kan het gevolg zijn van een storing in het besturingssysteem van een virtuele machine ofwel een aanvraag door een geautoriseerde gebruiker of een proces. De virtuele machine wordt weer online nadat de computer opnieuw is opgestart |
| Grootte gewijzigd door gebruiker | Het formaat van deze virtuele machine wordt gewijzigd zodra dit wordt aangevraagd door een geautoriseerde gebruiker of een proces. Het is weer online nadat het formaat is gewijzigd |
| De computer is vastgelopen | Er is een herstart geactiveerd vanuit de virtuele machine. Dit kan het gevolg zijn van een storing in het besturingssysteem van een virtuele machine ofwel een aanvraag door een geautoriseerde gebruiker of een proces. De virtuele machine wordt weer online nadat de computer opnieuw is opgestart |
| Onderhoud wordt opnieuw gestart voor een update van de host | Onderhoudsupdates worden toegepast op de hostserver waarop deze virtuele machine wordt uitgevoerd. U hoeft op dit moment geen aanvullende actie te ondernemen. Het is weer online nadat het onderhoud is voltooid |
| Onderhoud opnieuw implementeren naar nieuwe hardware | Deze virtuele machine is niet beschikbaar omdat deze opnieuw wordt geïmplementeerd op nieuwere hardware als onderdeel van een geplande onderhoudsgebeurtenis. U hoeft op dit moment geen aanvullende actie te ondernemen. Het is weer online nadat het geplande onderhoud is voltooid |
| Computer met lage prioriteit afgebroken | Deze virtuele machine is vervallen. Deze virtuele machine met lage prioriteit wordt gestopt en de toewijzing wordt ongedaan gemaakt |
| Hostserver opnieuw opstarten | Uw virtuele machine is niet beschikbaar, omdat de hostserver onverwacht opnieuw is opgestart. De hostserver wordt momenteel opnieuw opgestart. De virtuele machine zal weer online zijn nadat de herstart is voltooid. Er is op dit moment geen verdere actie vereist |
| Opnieuw implementeren vanwege een fout met de host | Uw virtuele machine is helaas niet beschikbaar en wordt opnieuw geïmplementeerd vanwege een onverwachte fout op de hostserver. Het automatische herstelproces van Azure is gestart en de virtuele machine wordt momenteel op een andere host gestart. Er is op dit moment geen verdere actie vereist |
| Onverwachte host-fout | Uw virtuele machine is helaas niet beschikbaar vanwege een onverwachte fout op de hostserver. Azure is begonnen met het automatische herstelproces en is momenteel bezig met het herstarten van de hostserver. U hoeft op dit moment geen aanvullende actie te ondernemen. De virtuele machine wordt weer online nadat de computer opnieuw is opgestart |
| Opnieuw implementeren vanwege ongepland host onderhoud | Uw virtuele machine is helaas niet beschikbaar en wordt opnieuw geïmplementeerd vanwege een onverwachte fout op de hostserver. Het automatische herstelproces van Azure is gestart en de virtuele machine wordt momenteel op een andere hostserver gestart. Er is op dit moment geen verdere actie vereist |
| Inrichtings fout | De virtuele machine is niet beschikbaar omdat er onverwachte problemen zijn met de inrichting. De inrichting van de virtuele machine is mislukt vanwege een onverwachte fout |
| Livemigratie | Deze virtuele machine wordt onderbroken vanwege een livemigratie-bewerking met geheugenbehoud. De virtuele machine wordt doorgaans binnen 10 seconden hervat. Er is op dit moment geen verdere actie vereist |
| Livemigratie | Deze virtuele machine wordt onderbroken vanwege een livemigratie-bewerking met geheugenbehoud. De virtuele machine wordt doorgaans binnen 10 seconden hervat. Er is op dit moment geen verdere actie vereist | 
| Externe schijf is niet verbonden | De virtuele machine is helaas niet beschikbaar omdat de verbinding met de externe schijf is verbroken. Er wordt aan gewerkt om de verbinding met de schijf te herstellen. Er is op dit moment geen verdere actie vereist |
| Probleem met de Azure-service | Uw virtuele machine wordt beïnvloed door een probleem met de Azure-service |
| Netwerk probleem | Deze virtuele machine wordt beïnvloed door een top-of-rack netwerk apparaat |
| Niet beschikbaar | Uw virtuele machine is niet beschikbaar. De reden voor deze downtime kan momenteel niet worden vastgesteld |
| Hostserver opnieuw opstarten | Uw virtuele machine is niet beschikbaar, omdat de hostserver onverwacht opnieuw is opgestart. Uw virtuele machine kan niet automatisch worden hersteld vanwege een onverwacht probleem met de hostserver |
| Opnieuw implementeren vanwege een fout met de host | Uw virtuele machine is helaas niet beschikbaar vanwege een onverwachte fout op de hostserver. Uw virtuele machine kan niet automatisch worden hersteld vanwege een onverwacht probleem met de host |
| Onverwachte host-fout | Uw virtuele machine is helaas niet beschikbaar vanwege een onverwachte fout op de hostserver. Uw virtuele machine kan niet automatisch worden hersteld vanwege een onverwacht probleem met de host |
| Opnieuw implementeren vanwege ongepland host onderhoud | Uw virtuele machine is helaas niet beschikbaar vanwege een onverwachte fout op de hostserver. Uw virtuele machine kan niet automatisch worden hersteld vanwege een onverwacht probleem met de host |
| Inrichtings fout | De virtuele machine is niet beschikbaar omdat er onverwachte problemen zijn met de inrichting. De inrichting van de virtuele machine is mislukt vanwege een onverwachte fout |
| Externe schijf is niet verbonden | De virtuele machine is helaas niet beschikbaar omdat de verbinding met de externe schijf is verbroken. Uw virtuele machine kan niet automatisch worden hersteld vanwege een onverwacht probleem |
| Opnieuw opstarten vanwege een update van het gast besturingssysteem | Opnieuw opstarten is gestart door het Azure-platform om een nieuwe update voor het gast besturingssysteem toe te passen. De virtuele machine wordt weer online nadat de computer opnieuw is opgestart |
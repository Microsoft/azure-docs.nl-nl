---
title: Inleiding tot het bijwerken van apparaten voor Azure IoT Hub | Microsoft Docs
description: Update van het apparaat voor IoT Hub is een service waarmee u over-the-Air-updates (OTA) kunt implementeren voor uw IoT-apparaten.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 17674c1a5d7acff50a3dd17b9d98f5295c2e1b19
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633033"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Overzicht van updates voor apparaten voor IoT Hub (preview-versie)

Update van het apparaat voor IoT Hub is een service waarmee u over-the-Air-updates (OTA) kunt implementeren voor uw IoT-apparaten.

Als organisaties meer productiviteit en operationele efficiëntie willen inschakelen, worden oplossingen voor Internet of Things (IoT) nog steeds toegepast op toenemende tarieven. Dit maakt het essentieel dat de apparaten die deze oplossingen vormen, zijn gebouwd op basis van betrouw baarheid en beveiliging, en eenvoudig kunnen worden verbonden en beheerd op schaal. Update van het apparaat voor IoT Hub is een end-to-end platform dat klanten kunnen gebruiken om over-the-Air-updates te publiceren, distribueren en beheren voor alles van kleine Sens oren tot apparaten op Gateway niveau. 

Om de volledige voor delen van het gebruik van IoT-Digital Transformation te realiseren, hebben klanten de mogelijkheid om apparaten op schaal te bedienen, te onderhouden en bij te werken. Bekijk de voor delen van het implementeren van updates voor het apparaat voor IoT Hub, waaronder het snel reageren op beveiligings Risico's en het implementeren van nieuwe functies voor het verkrijgen van zakelijke doel stellingen zonder de extra ontwikkelings-en onderhouds kosten van het bouwen van uw eigen update platforms.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Ondersteuning voor een breed scala aan IoT-apparaten

Update van het apparaat voor IoT Hub is ontworpen om geoptimaliseerde update-implementaties en gestroomlijnde bewerkingen te bieden via integratie met [Azure IOT hub](https://azure.microsoft.com/en-us/services/iot-hub/). Met deze integratie kunt u eenvoudig een update van het apparaat op een bestaande oplossing aanbrengen. Het biedt een in de Cloud gehoste oplossing om verbinding te maken met vrijwel elk apparaat. Device update biedt ondersteuning voor een breed scala aan IoT-besturings systemen, waaronder Linux en [Azure rto's](https://azure.microsoft.com/en-us/services/rtos/) (realtime besturings systeem), en is uitbreidbaar via open source. Er wordt een update van het apparaat voor IoT Hub-aanbiedingen met onze halfgeleider partners, waaronder STMicroelectronics, NXP, Renesas en micro chip. Bekijk de voor [beelden](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) van de evaluatie boards voor belangrijkste halfgeleiders die de aan de slag-hand leidingen bevatten voor meer informatie over het configureren, bouwen en implementeren van de over-the-Air (OTA)-updates voor MCU Class-apparaten.

Er worden zowel de binaire als de Raspberry Pi Reference yocto-installatie kopieën van het apparaat bijgewerkt.
Update van het apparaat voor IoT Hub biedt ook ondersteuning voor het bijwerken van Azure IoT Edge apparaten. Er wordt een update agent voor apparaten gegeven voor Ubuntu Server 18,04 amd64-platform. Update van het apparaat voor IoT Hub biedt ook open-source code als u een van de bovenstaande platformen niet uitvoert. U kunt de agent overbrengen naar de distributie die u uitvoert.

Het bijwerken van het apparaat werkt met IoT Plug en Play (PnP) en kan elk apparaat beheren dat de vereiste PnP-interfaces ondersteunt. Zie voor meer informatie [apparaat bijwerken voor IOT hub en IoT Plug en Play](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Ondersteuning voor een breed scala aan update artefacten

Update van het apparaat voor IoT Hub ondersteunt twee soorten updates: op installatie kopie gebaseerd en op basis van een pakket.

Updates op basis van pakketten zijn gerichte updates waarmee alleen een specifiek onderdeel of een specifieke toepassing op het apparaat wordt gewijzigd. Dit leidt tot een lager verbruik van de band breedte en vermindert de tijd voor het downloaden en installeren van de update. Pakket updates bieden doorgaans minder downtime van apparaten bij het Toep assen van een update en vermijden de overhead van het maken van installatie kopieën.

Installatie kopieën bieden een hoger vertrouwens niveau in de eind toestand van het apparaat. Het is doorgaans eenvoudiger om de resultaten van een installatie kopie te repliceren: update tussen een pre-productie omgeving en een productie omgeving, omdat deze niet dezelfde uitdagingen als pakketten en hun afhankelijkheden vormt.
Als gevolg van hun atoom karakter kan een model van een/B-failover ook eenvoudig worden aangenomen.

Er is niemand die een antwoord heeft en u kunt anders kiezen op basis van uw specifieke use cases. Update van het apparaat voor IoT Hub ondersteunt zowel afbeelding als pakket vorm van bijwerken, zodat u het juiste update model kunt kiezen voor uw omgeving.

## <a name="flexible-features-for-updating-devices"></a>Flexibele functies voor het bijwerken van apparaten

De update van het apparaat voor IoT Hub functies biedt een krachtige en flexibele ervaring, waaronder:

* Update beheer UX geïntegreerd met Azure IoT Hub
* Geleidelijke update-implementatie via apparaten groeperen en besturings elementen voor update planning
* Programmatische Api's om automatiserings-en aangepaste Portal-ervaringen in te scha kelen
* In één oogopslag update vereisten en status weergaven op heterogene-apparaten
* Ondersteuning voor flexibele updates van apparaten (A/B) voor het leveren van naadloze terugdraai actie
* Abonnementen en op rollen gebaseerde toegangs controles die beschikbaar zijn via de Azure.com-Portal
* On-premises inhouds cache en geneste EDGE-ondersteuning om het bijwerken van niet-verbonden apparaten in de Cloud mogelijk te maken
* Gedetailleerde hulpprogram ma's voor update beheer en rapportage 

Met update van het apparaat voor IoT Hub beheer-en implementatie besturings elementen kunnen gebruikers hun productiviteit maximaliseren en kost bare tijd besparen. Apparaat bijwerken voor IoT Hub bevat de mogelijkheid om apparaten te groeperen en op te geven op welke apparaten een update moet worden geïmplementeerd. Gebruikers kunnen ook de status van update-implementaties bekijken en controleren of de updates zijn toegepast op elk apparaat.

Wanneer er een update fout is opgetreden, kunnen gebruikers met apparaat-update voor IoT Hub ook de apparaten identificeren die de update niet kunnen Toep assen en zie gerelateerde fout Details. De mogelijkheid om te bepalen welke apparaten niet kunnen worden bijgewerkt betekent talloze hand matige uren die zijn opgeslagen tijdens het lokaliseren van de bron.

### <a name="best-in-class-security-at-global-scale"></a>Eersteklas beveiliging op wereld wijde schaal

Microsoft Azure ondersteunt meer dan een miljard IoT-apparaat over de hele wereld: een getal dat snel op de dag groeit. Update van het apparaat voor IoT Hub bouwt voort op deze manier en de bewezen betrouw baarheid die wordt gedemonstreerd door het Windows Update platform, zodat apparaten naadloos kunnen worden bijgewerkt op een wereld wijde schaal.

Apparaat bijwerken voor IoT Hub maakt gebruik van uitgebreide Cloud-naar-Edge-beveiliging die is ontwikkeld voor Microsoft Azure, zodat klanten geen tijd hoeven te best Eden aan het bepalen van de manier waarop hij zelf aan de slag kan bouwen.


## <a name="device-update-workflows"></a>Werk stromen voor apparaat bijwerken

De functionaliteit voor het bijwerken van apparaten kan worden onderverdeeld in drie gebieden: integratie van agents, importeren en beheren.

### <a name="device-update-agent"></a>Update-Agent van apparaat

Wanneer een update opdracht wordt ontvangen op een apparaat, wordt de aangevraagde fase van het bijwerken uitgevoerd (downloaden, installeren en Toep assen). Tijdens elke fase wordt de status van het apparaat via IoT Hub geretourneerd, zodat u de huidige status van een implementatie kunt bekijken. Als er geen updates worden uitgevoerd, wordt de status geretourneerd als ' inactief '. Een implementatie kan op elk gewenst moment worden geannuleerd.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Diagram van werk stroom voor het bijwerken van de apparaats agent." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Meer informatie](device-update-agent-overview.md) over de Update Agent voor apparaten. 

### <a name="importing"></a>Importeren

Importeren is de mogelijkheid om uw update te importeren in het bijwerken van het apparaat. Het bijwerken van het apparaat ondersteunt het implementeren van één update per apparaat. Dit is ideaal voor updates met volledige installatie kopieën die een volledige OS-partitie tegelijk bijwerken, of een apt-manifest dat alle pakketten beschrijft die u op uw apparaat wilt bijwerken. Als u updates wilt importeren in een update van het apparaat, maakt u eerst een import manifest met een beschrijving van de update en uploadt u de update bestanden en het import manifest naar een locatie die toegankelijk is via internet. Daarna kunt u de Azure Portal of de REST API update voor het bijwerken van het apparaat gebruiken om het asynchrone proces van het importeren van updates te initiëren. Het bijwerken van het apparaat uploadt de bestanden, verwerkt ze en maakt ze beschikbaar voor distributie naar IoT-apparaten.

Voor gevoelige inhoud kunt u de down load beveiligen met behulp van een Shared Access Signature (SAS), zoals een ad-hoc SAS voor Azure Blob Storage. [Meer informatie over SAS](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Diagram van apparaat bijwerken voor het importeren van IoT Hub werk stroom." lightbox="media/understand-device-update/import-update.png":::

[Meer informatie](import-concepts.md) over het importeren van updates. 

### <a name="grouping-and-deployment"></a>Groepering en implementatie

Nadat u een update hebt geïmporteerd, kunt u compatibele updates voor uw apparaten en apparaatklassen weer geven.

Het bijwerken van het apparaat ondersteunt het concept van **groepen** via tags in IOT hub. Het implementeren van een update naar een test groep is een goede manier om het risico op problemen tijdens een productie-implementatie te verminderen.

Bij het bijwerken van het apparaat is implementaties een manier om de juiste inhoud te koppelen aan een specifieke set compatibele apparaten. Met de update van het apparaat wordt het proces van het verzenden van opdrachten naar elk apparaat door gegeven, zodat u de updates kunt downloaden en installeren en de status weer kunt ophalen.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Diagram van het bijwerken van het apparaat voor IoT Hub groepering en implementatie werk stroom." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[Meer informatie](device-update-compliance.md) over implementatie concepten

[Meer informatie](device-update-groups.md) over update groepen voor apparaten


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Account en exemplaar voor het bijwerken van het apparaat maken](create-device-update-account.md)

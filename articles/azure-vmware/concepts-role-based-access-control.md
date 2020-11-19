---
title: Concepten-vSphere op rollen gebaseerd toegangs beheer (vSphere RBAC)
description: Meer informatie over de belangrijkste mogelijkheden van toegangs beheer op basis van rollen voor Azure VMware-oplossing
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: fbd5b48ce30a6612c45ebd97f08812efd29c50b3
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888399"
---
# <a name="vsphere-role-based-access-control-vsphere-rbac-for-azure-vmware-solution"></a>vSphere op rollen gebaseerd toegangs beheer (vSphere RBAC) voor Azure VMware-oplossing

In de Azure VMware-oplossing heeft vCenter een ingebouwde lokale gebruiker met de naam cloudadmin en toegewezen aan de ingebouwde CloudAdmin-rol. De lokale cloudadmin-gebruiker wordt gebruikt om gebruikers in AD in te stellen. In het algemeen maakt en beheert de rol CloudAdmin werk belastingen in uw privécloud. In azure VMware-oplossing heeft de rol CloudAdmin vCenter-bevoegdheden die verschillen van andere VMware-cloud oplossingen.     

> [!NOTE]
> De Azure VMware-oplossing biedt momenteel geen aangepaste rollen in vCenter of de Azure VMware-oplossings Portal. 

In een installatie van vCenter en ESXi on-premises heeft de beheerder toegang tot het vCenter- administrator@vsphere.local account. Er kunnen ook aanvullende Active Directory (AD) gebruikers/groepen worden toegewezen. 

In een implementatie van een Azure VMware-oplossing heeft de beheerder geen toegang tot het gebruikers account van de beheerder. Maar ze kunnen AD-gebruikers en-groepen toewijzen aan de CloudAdmin-rol in vCenter.  

De gebruiker van de privécloud heeft geen toegang tot en kan geen specifieke beheer onderdelen configureren die door micro soft worden ondersteund en beheerd. Bijvoorbeeld clusters, hosts, gegevens opslag en gedistribueerde virtuele switches.




## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>Azure VMware-oplossing CloudAdmin Role in vCenter

U kunt de bevoegdheden weer geven die zijn verleend aan de Azure VMware Solution CloudAdmin-rol in uw Azure VMware-oplossing privécloud-vCenter.

1. Meld u aan bij de SDDC vSphere-client en ga naar **menu**  >  **beheer**.
1. Onder **Access Control** selecteert u **rollen**.
1. Selecteer **CloudAdmin** in de lijst met rollen en selecteer vervolgens **bevoegdheden**. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="De bevoegdheden voor CloudAdmin in vSphere-client weer geven":::

De CloudAdmin-rol in de Azure VMware-oplossing heeft de volgende bevoegdheden op vCenter. Raadpleeg de [VMware-product documentatie](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) voor een gedetailleerde uitleg van elke bevoegdheid.

| Bevoegdheid | Beschrijving |
| --------- | ----------- |
| **Waarschuwingen** | Waarschuwing bevestigen<br />Alarm maken<br />Waarschuwings actie uitschakelen<br />Waarschuwing wijzigen<br />Waarschuwing verwijderen<br />Waarschuwings status instellen |
| **Machtigingen** | Machtigingen wijzigen |
| **Inhoudsbibliotheek** | Bibliotheek item toevoegen<br />Een abonnement voor een gepubliceerde bibliotheek maken<br />Lokale bibliotheek maken<br />Geabonneerde bibliotheek maken<br />Bibliotheek item verwijderen<br />Lokale bibliotheek verwijderen<br />Geabonneerde bibliotheek verwijderen<br />Het abonnement van een gepubliceerde bibliotheek verwijderen<br />Bestanden downloaden<br />Bibliotheek items verwijderen<br />Geabonneerde bibliotheek verwijderen<br />Opslag importeren<br />Abonnements gegevens testen<br />Een bibliotheek item publiceren naar de abonnee servers<br />Een bibliotheek publiceren naar de abonnee servers<br />Opslag lezen<br />Bibliotheek item synchroniseren<br />Geabonneerde bibliotheek synchroniseren<br />Type introspectie<br />Configuratie-instellingen bijwerken<br />Update bestanden<br />Bibliotheek bijwerken<br />Bibliotheek item bijwerken<br />Lokale bibliotheek bijwerken<br />Geabonneerde bibliotheek bijwerken<br />Abonnement van een gepubliceerde bibliotheek bijwerken<br />Configuratie-instellingen weer geven |
| **Cryptografische bewerkingen** | Directe toegang |
| **Gegevensarchief** | Ruimte toewijzen<br />Door gegevensarchief bladeren<br />Gegevens opslag configureren<br />Bestands bewerkingen op laag niveau<br />Bestanden verwijderen<br />Meta gegevens van de virtuele machine bijwerken |
| **Map** | Map maken<br />Map verwijderen<br />Map verplaatsen<br />Mapnaam wijzigen |
| **Globaal** | Taak annuleren<br />Global-Tag<br />Gezondheidszorg<br />Logboek gebeurtenis<br />Aangepaste kenmerken beheren<br />Service managers<br />Aangepast kenmerk instellen<br />Systeem label |
| **Host** | vSphere-replicatie<br />Replicatie &#160;&#160;&#160;&#160;beheren |
| **Tags voor vSphere** | VSphere-tag toewijzen en de toewijzing opheffen<br />VSphere-tag maken<br />VSphere-label categorie maken<br />VSphere-tag verwijderen<br />VSphere-label categorie verwijderen<br />VSphere-tag bewerken<br />Categorie vSphere-code bewerken<br />Het veld UsedBy voor categorie wijzigen<br />UsedBy veld voor label wijzigen |
| **Netwerk** | Netwerk toewijzen |
| **Resource** | Aanbeveling Toep assen<br />VApp toewijzen aan resource groep<br />Virtuele machine aan resource groep toewijzen<br />Resource groep maken<br />Virtuele machine die is uitgeschakeld migreren<br />De ingeschakelde virtuele machine wordt gemigreerd<br />Resource groep wijzigen<br />Resource groep verplaatsen<br />Query's van vMotion<br />Resource groep verwijderen<br />Naam van resource groep wijzigen |
| **Geplande taak** | Taak maken<br />Taak wijzigen<br />Taak verwijderen<br />Taak uitvoeren |
| **Sessies** | Bericht<br />Sessie valideren |
| **Profiel** | Weer gave voor profiel gerichte opslag |
| **Opslag weergave** | Weergave |
| **vApp** | Virtuele machine toevoegen<br />Resource groep toewijzen<br />VApp toewijzen<br />Klonen<br />Maken<br />Verwijderen<br />Exporteren<br />Importeren<br />Verplaatsen<br />Uitschakelen<br />Inschakelen<br />Naam wijzigen<br />Onderbreken<br />Registratie ongedaan maken<br />OVF-omgeving weer geven<br />configuratie van vApp-toepassing<br />configuratie van vApp-exemplaar<br />configuratie van vApp managedBy<br />vApp-resource configuratie |
| **Virtuele machine** | Configuratie wijzigen<br />&#160;&#160;&#160;&#160;-adreslease ophalen<br />Bestaande schijf &#160;&#160;&#160;&#160;toevoegen<br />Nieuwe schijf &#160;&#160;&#160;&#160;toevoegen<br />&#160;&#160;&#160;&#160;apparaat toevoegen of verwijderen<br />Geavanceerde configuratie van &#160;&#160;&#160;&#160;<br />Aantal CPU-&#160;&#160;&#160;&#160;wijzigen<br />&#160;&#160;&#160;&#160;geheugen wijzigen<br />Instellingen &#160;&#160;&#160;&#160;wijzigen<br />Swapfile-plaatsing &#160;&#160;&#160;&#160;wijzigen<br />Resource &#160;&#160;&#160;&#160;wijzigen<br />USB-apparaat van host &#160;&#160;&#160;&#160;configureren<br />&#160;&#160;&#160;&#160;onbewerkt apparaat configureren<br />ManagedBy configureren &#160;&#160;&#160;&#160;<br />Verbindings instellingen &#160;&#160;&#160;&#160;weer geven<br />Virtuele schijf &#160;&#160;&#160;&#160;uitbreiden<br />Apparaatinstellingen &#160;&#160;&#160;&#160;wijzigen<br />Compatibiliteit van de query fout tolerantie &#160;&#160;&#160;&#160;<br />Bestanden &#160;&#160;&#160;&#160;oneigendom van de query<br />&#160;&#160;&#160;&#160;opnieuw laden vanaf paden<br />Schijf &#160;&#160;&#160;&#160;verwijderen<br />&#160;&#160;&#160;&#160;naam wijzigen<br />Gast gegevens &#160;&#160;&#160;&#160;opnieuw instellen<br />Aantekening &#160;&#160;&#160;&#160;instellen<br />&#160;&#160;&#160;&#160;schijf wijzigingen bijhouden uitschakelen<br />&#160;&#160;&#160;&#160;Fork-bovenliggend element in-/uitschakelen<br />Compatibiliteit met de upgrade van virtuele machines &#160;&#160;&#160;&#160;<br />Inventaris bewerken<br />&#160;&#160;&#160;&#160;maken op basis van bestaande<br />&#160;&#160;&#160;&#160;nieuwe maken<br />&#160;&#160;&#160;&#160;verplaatsen<br />&#160;&#160;&#160;&#160;registreren<br />&#160;&#160;&#160;&#160;verwijderen<br />&#160;&#160;&#160;&#160;registratie ongedaan maken<br />Gast bewerkingen<br />Wijziging van alias voor &#160;&#160;&#160;&#160;van gast bewerking<br />Alias query &#160;&#160;&#160;&#160;gast bewerking<br />Wijzigingen in de gast bewerking &#160;&#160;&#160;&#160;<br />Uitvoering van programma voor gast bewerking &#160;&#160;&#160;&#160;<br />Query's voor de gast bewerking &#160;&#160;&#160;&#160;<br />Interactie<br />Vraag &#160;&#160;&#160;&#160;beantwoorden<br />Back-upbewerking &#160;&#160;&#160;&#160;op virtuele machine<br />&#160;&#160;&#160;&#160;CD-media configureren<br />&#160;&#160;&#160;&#160;configureren van diskette media<br />&#160;&#160;&#160;&#160;apparaten verbinden<br />Interactie &#160;&#160;&#160;&#160;-console<br />Scherm opname &#160;&#160;&#160;&#160;maken<br />&#160;&#160;&#160;&#160;alle schijven defragmenteren<br />&#160;&#160;&#160;&#160;slepen en neerzetten<br />VIX-API voor het beheer van gast besturingssystemen &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;invoeren van USB-HID-scan codes<br />&#160;&#160;&#160;&#160;VMware-hulpprogram ma's installeren<br />&#160;&#160;&#160;&#160;onderbreken of onderbreken<br />&#160;&#160;&#160;&#160;bewerkingen voor wissen of verkleinen uitvoeren<br />&#160;&#160;&#160;&#160;uitschakelen<br />&#160;&#160;&#160;&#160;inschakelen<br />Sessie &#160;&#160;&#160;&#160;record op virtuele machine<br />&#160;&#160;&#160;&#160;sessie opnieuw afspelen op de virtuele machine<br />&#160;&#160;&#160;&#160;onderbreken<br />Fout tolerantie &#160;&#160;&#160;&#160;uitstellen<br />Testfailover &#160;&#160;&#160;&#160;<br />Secundaire VM &#160;&#160;&#160;&#160;test opnieuw starten<br />&#160;&#160;&#160;&#160;fout tolerantie uitschakelen<br />Fout tolerantie &#160;&#160;&#160;&#160;inschakelen<br />Inrichten<br />&#160;&#160;&#160;&#160;schijf toegang toestaan<br />Bestands toegang &#160;&#160;&#160;&#160;toestaan<br />&#160;&#160;&#160;&#160;alleen-lezen schijf toegang toestaan<br />Downloaden van virtuele machine &#160;&#160;&#160;&#160;toestaan<br />&#160;&#160;&#160;&#160;kloon sjabloon<br />Virtuele machine &#160;&#160;&#160;&#160;klonen<br />&#160;&#160;&#160;&#160;sjabloon maken op basis van de virtuele machine<br />Gast &#160;&#160;&#160;&#160;aanpassen<br />Sjabloon &#160;&#160;&#160;&#160;implementeren<br />&#160;&#160;&#160;&#160;markeren als sjabloon<br />Specificatie van aanpassing van &#160;&#160;&#160;&#160;wijzigen<br />&#160;&#160;&#160;&#160;promo veren van schijven<br />Specificaties van &#160;&#160;&#160;&#160;lezen<br />Serviceconfiguratie<br />Meldingen &#160;&#160;&#160;&#160;toestaan<br />&#160;&#160;&#160;&#160;polling van globale gebeurtenis meldingen toestaan<br />Service configuratie &#160;&#160;&#160;&#160;beheren<br />Service configuratie &#160;&#160;&#160;&#160;wijzigen<br />Configuraties van &#160;&#160;&#160;&#160;query service<br />Configuratie van &#160;&#160;&#160;&#160;-Lees service<br />Beheer van moment opnamen<br />Moment opname &#160;&#160;&#160;&#160;maken<br />Moment opname &#160;&#160;&#160;&#160;verwijderen<br />&#160;&#160;&#160;&#160;naam van moment opname wijzigen<br />&#160;&#160;&#160;&#160;moment opname herstellen<br />vSphere-replicatie<br />Replicatie &#160;&#160;&#160;&#160;configureren<br />Replicatie &#160;&#160;&#160;&#160;beheren<br />Replicatie &#160;&#160;&#160;&#160;controleren |
| **vService** | Afhankelijkheid maken<br />Afhankelijkheid vernietigen<br />Afhankelijkheids configuratie opnieuw configureren<br />Afhankelijkheid bijwerken |



## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [VMware-product documentatie](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) voor een gedetailleerde uitleg van elke bevoegdheid.

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html


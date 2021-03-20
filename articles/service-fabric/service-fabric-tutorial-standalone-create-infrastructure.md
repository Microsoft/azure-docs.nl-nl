---
title: Infrastructuur maken voor een cluster op AWS
description: In deze zelfstudie leert u hoe u de AWS-infrastructuur instelt voor het uitvoeren van een Service Fabric-cluster.
ms.topic: tutorial
ms.date: 05/11/2018
ms.custom: mvc
ms.openlocfilehash: c7a18b0dcdc04bdf66ac4b36ce7376ee018eb238
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842900"
---
# <a name="tutorial-create-aws-infrastructure-to-host-a-service-fabric-cluster"></a>Zelfstudie: AWS-infrastructuur maken voor het hosten van een Service Fabric-cluster

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze reeks zelfstudies maakt u een zelfstandig cluster dat in AWS wordt gehost en installeert u vervolgens een toepassing in het cluster.

Deze zelfstudie is deel één van een serie. In dit artikel genereert u de AWS-resources die vereist zijn voor het hosten van uw zelfstandige cluster van Service Fabric. In andere artikelen uit deze serie gaat u een zelfstandig Service Fabric-cluster installeren, een voorbeeldtoepassing in het cluster installeren en ten slotte het cluster opschonen.

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Een set van EC2-exemplaren maken
> * De beveiligingsgroep wijzigen
> * Aanmelden bij een van de exemplaren
> * Het exemplaar voorbereiden voor Service Fabric

## <a name="prerequisites"></a>Vereisten

U hebt een AWS-account nodig om deze zelfstudie te voltooien.  Als u nog geen account hebt, gaat u naar de [AWS-console](https://aws.amazon.com/) om er een te maken.

## <a name="create-ec2-instances"></a>EC2-exemplaren maken

Meld u aan bij de AWS-console > typ **EC2** in het zoekvak > **EC2 Virtual Servers in the Cloud**.

![Zoeken in AWS-console][aws-console]

Selecteer **Launch Instance** en kies in het volgende scherm **Select** naast Microsoft Windows Server 2016 Base.

![EC2-exemplaar selecteren][aws-ec2instance]

Selecteer **t2.medium**, selecteer **Next: Configure Instance Details**, wijzig op het volgende scherm het aantal exemplaren in `3` en selecteer **Advanced Details** om die sectie uit te vouwen.

Om uw virtuele machines te verbinden in Service Fabric, moeten de VM's die uw infrastructuur hosten dezelfde referenties hebben.  Er zijn twee manieren om consistente referenties te hanteren: alle machines toevoegen aan hetzelfde domein of op elke VM hetzelfde beheerderswachtwoord instellen.  Voor deze zelfstudie gebruikt u een script met gebruikersgegevens om voor alle EC2-exemplaren hetzelfde wachtwoord in te stellen.  In een productieomgeving is het veiliger om de hosts lid te maken van een Windows-domein.

Neem het volgende script over in het veld User data van de console:

```powershell
<powershell>
$user = [adsi]"WinNT://localhost/Administrator,user"
$user.SetPassword("serv1ceF@bricP@ssword")
$user.SetInfo()
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
</powershell>
```

Als u het PowerShell-script hebt ingevoerd, selecteert u **Review and Launch**.

![EC2-invoer controleren en starten][aws-ec2configure2]

Selecteer **Launch** op het scherm Review.  Wijzig de optie in de vervolgkeuzelijst in **Proceed without a key pair** en schakel het selectievakje in om aan te geven dat u het wachtwoord kent.

![Selectie van AWS-sleutelpaar][aws-keypair]

Selecteer ten slotte **Launch Instances** en vervolgens **View Instances**.  Hiermee hebt u de basis voor uw Service Fabric-cluster gemaakt. U moet nu nog enkele laatste configuraties aan de exemplaren zelf toevoegen om deze voor te bereiden op de configuratie van Service Fabric.

## <a name="modify-the-security-group"></a>De beveiligingsgroep wijzigen

Voor Service Fabric moeten verschillende poorten zijn geopend tussen de hosts in het cluster. Om deze poorten te openen in de infrastructuur van AWS, selecteert u een van de exemplaren die u hebt gemaakt. Selecteer vervolgens de naam van de beveiligingsgroep, bijvoorbeeld **launch-wizard-1**. Selecteer nu het tabblad **Inbound**.

Om te voorkomen dat deze poorten voor iedereen worden geopend, opent u ze alleen voor hosts in dezelfde beveiligingsgroep. Noteer de id van de netwerkbeveiligingsgroep. In het voorbeeld is de id **sg-c4fb1eba**.  Selecteer vervolgens **Edit**.

Voeg nu vier regels toe aan de beveiligingsgroep voor serviceafhankelijkheden en dan nog drie voor Service Fabric zelf. De eerste regel is om ICMP-verkeer toe te staan, voor essentiële connectiviteitscontroles. Met de andere regels worden de vereiste poorten geopend om SMB en Remote Registry in te schakelen.

Selecteer voor de eerste regel **Add Rule** en selecteer vervolgens in de vervolgkeuzelijst **All ICMP - IPv4**. Selecteer het invoervak naast Custom en voer de id van de beveiligingsgroep in die u eerder hebt genoteerd.

Voor de laatste drie afhankelijkheden moet u een vergelijkbaar proces volgen.  Selecteer **Add Rule**, selecteer **Custom TCP Rule** in de vervolgkeuzelijst en voer bij Port Range het poortbereik `135`, `137-139` of `445` in voor elke regel. Typ ten slotte in het vak Source de id van de beveiligingsgroep.

![Poorten voor beveiligingsgroep][aws-ec2securityports]

Nu de poorten voor de afhankelijkheden zijn geopend, moet u hetzelfde doen voor de poorten die door Service Fabric zelf worden gebruikt om te communiceren. Selecteer **Add Rule**, selecteer **Custom TCP Rule** in de vervolgkeuzelijst, voer bij Port Range het poortbereik `20001-20031` in en voer bij Source de beveiligingsgroep in.

Voeg nu een regel toe voor het tijdelijke poortbereik.  Selecteer **Add Rule**, selecteer **Custom TCP Rule** in de vervolgkeuzelijst en voer bij Port Range het poortbereik `20606-20861` in. Typ ten slotte in het vak Source de id van de beveiligingsgroep.

Met de laatste twee regels maakt u Service Fabric vanaf elke locatie toegankelijk, zodat u het Service Fabric-cluster vanaf uw eigen computer kunt beheren. Selecteer **Add Rule**, selecteer **Custom TCP Rule** in de vervolgkeuzelijst, voer bij Port Range het poortbereik `19000-19003` of `19080-19081` in en selecteer Anywhere in de vervolgkeuzelijst Source.

Als laatste hoeft alleen nog maar poort 8080 te worden geopend, zodat u de toepassing kunt zien wanneer deze wordt geïmplementeerd. Selecteer **Add Rule**, selecteer **Custom TCP Rule** in de vervolgkeuzelijst, voer bij Port Range het poortbereik `8080` in en selecteer Anywhere in de vervolgkeuzelijst Source.

Alle regels zijn nu ingevoerd. Selecteer **Opslaan**.

## <a name="connect-to-an-instance-and-validate-connectivity"></a>Verbinding maken met een exemplaar en de connectiviteit valideren

Ga naar het tabblad Security Group en selecteer **Instances** in het menu aan de linkerkant.  Selecteer elk van de exemplaren die u hebt gemaakt en noteer de bijbehorende privé-IP-adressen. Voor de onderstaande voorbeelden wordt `172.31.21.141` en `172.31.20.163` gebruikt.

Als u alle IP-adressen hebt genoteerd, selecteert u een van de exemplaren om hiermee verbinding te maken, klikt u met de rechtermuisknop op het exemplaar en selecteert u **Connect**.  U kunt nu het RDP-bestand voor dit exemplaar downloaden.  Selecteer **Download Remote Desktop File** en open vervolgens het bestand dat is gedownload om een extern-bureaubladverbinding (RDP) op te zetten met dit exemplaar.  Als dit wordt gevraagd, voert u het wachtwoord `serv1ceF@bricP@ssword` in.

![Extern-bureaubladbestand downloaden][aws-rdp]

Als er verbinding is met het exemplaar, controleert u of u zonder problemen verbinding kunt maken en of u bestanden kunt delen.  U hebt de IP-adressen voor alle exemplaren verzameld. Selecteer een exemplaar waarmee geen verbinding is gemaakt. Ga naar **Start**, voer `cmd` in en selecteer **Opdrachtprompt**.

In deze voorbeelden is een RDP-verbinding gemaakt met het volgende IP-adres: 172.31.21.141. Alle connectiviteitstesten vinden vervolgens plaats naar het andere IP-adres: 172.31.20.163.

Als u wilt controleren of de basisconnectiviteit werkt, gebruikt u de ping-opdracht.

```
ping 172.31.20.163
```

Als de uitvoer er gedurende vier pogingen uitziet zoals `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`, werkt de verbinding tussen de exemplaren.  Gebruik nu de volgende opdracht om te controleren of delen via SMB werkt:

```
net use * \\172.31.20.163\c$
```

De uitvoer moet gelijk zijn aan `Drive Z: is now connected to \\172.31.20.163\c$.`.

## <a name="prep-instances-for-service-fabric"></a>Exemplaren voorbereiden voor Service Fabric

Als u alles helemaal zelf hebt gedaan, moet u nog een paar extra stappen uitvoeren.  U moet namelijk valideren of het externe register actief was, SMB inschakelen en de vereiste poorten openen voor SMB en het externe register.

Om deze stappen te vereenvoudigen, hebt u al deze taken ingesloten toen u de exemplaren via een bootstrap hebt geladen met het script met gebruikersgegevens.

Dit is de PowerShell-opdracht die u hebt gebruikt voor het inschakelen van SMB:

```powershell
netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
```

Dit is de PowerShell-opdracht voor het openen van de poorten in de firewall:

```powershell
New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
```

## <a name="next-steps"></a>Volgende stappen

In deel één van de reeks hebt u geleerd hoe u drie EC2-exemplaren start en deze configureert voor de installatie van Service Fabric:

> [!div class="checklist"]
> * Een set van EC2-exemplaren maken
> * De beveiligingsgroep wijzigen
> * Aanmelden bij een van de exemplaren
> * Het exemplaar voorbereiden voor Service Fabric

Ga naar deel twee van de reeks om Service Fabric te configureren in uw cluster.

> [!div class="nextstepaction"]
> [Service Fabric installeren](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[aws-console]: ./media/service-fabric-tutorial-standalone-cluster/aws-console.png
[aws-ec2instance]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2instance.png
[aws-ec2configure2]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2configure2.png
[aws-rdp]: ./media/service-fabric-tutorial-standalone-cluster/aws-rdp.png
[aws-ec2securityports]: ./media/service-fabric-tutorial-standalone-cluster/aws-ec2securityports.png
[aws-keypair]: ./media/service-fabric-tutorial-standalone-cluster/aws-keypair.png
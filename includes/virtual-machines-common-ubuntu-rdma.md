---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016119"
---
1. Dapl, rdmacm, ibverbs en mlx4 installeren

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. In/etc/waagent.conf schakelt u RDMA in door de volgende configuratie regels op te heffen. U hebt toegang tot de hoofdmap nodig om dit bestand te bewerken.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Voeg de volgende geheugen instellingen in KB toe of wijzig deze in het/etc/security/limits.conf-bestand. U hebt toegang tot de hoofdmap nodig om dit bestand te bewerken. Voor test doeleinden kunt u memlock instellen op onbeperkt. Bijvoorbeeld: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Installeer de Intel MPI-bibliotheek. [Koop en down load](https://software.intel.com/intel-mpi-library/) de bibliotheek van Intel of down load de [gratis evaluatie versie](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Alleen Intel MPI 5. x-Runtimes worden ondersteund.
 
   Zie de installatie handleiding voor de [Intel mpi-bibliotheek](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)voor installatie stappen.

5. Schakel ptrace in voor niet-hoofd processen zonder fout opsporing (vereist voor de meest recente versies van Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```

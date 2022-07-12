# shr-mfg-platform


by Tomaž:
* HTTP server za robotsko roko: https://github.com/fsprojekti/shr-mfg-robotic-arm-http-server
	* aplikacija narejena in stestirana
* Arduino aplikacija za vožnjo avtomobilčkov: https://github.com/fsprojekti/df_micro_maqueen-mbits-esp32_arduino_app
	* koda v glavnem napisana, ni pa še resno stestirana - čaka se dobava esp32 boardov
* Node.js nadzorna aplikacija za avtomobilčke: https://github.com/fsprojekti/df-micro-maqueen-robot-cars-control-app
	* koda skoraj napisana, delno stestirana
	
```mermaid
graph TD
subgraph ML[Application layer]
    MO[Origin plant management]
    MC[Carriers management ]
end
subgraph CL[Communication layer]
    subgraph CL1[HTTP servers]
    CC[Carrier]
    CO[Origin plant]
    CW[Warehouse]
    CM[Manufacturing plant]
    end
    subgraph CL2[Socket servers]
    CT[Track]
    end
end
subgraph PL[Physical layer]
 T[Track]
    subgraph PL1[ESP Microbit]
    C[Carrier]
    end
   
    subgraph PL2[JetMax robotic arm]
        W[Warehouse]
        M[Manufacturing plant]
        O[Origin plant]
    end
end
CC---C
MO---|HTTP API|CO
MC---|HTTP API|CC
CO---O
CM---M
CW---W
CT---T


style CL fill:#FAD7A0, stroke:#F5B041
style PL fill:#AED6F1, stroke:#3498DB
style ML fill:#ABEBC6, stroke:#28B463

```

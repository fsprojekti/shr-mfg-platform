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
graph TD
subgraph ML[Management layer]
    MO[Origin plant management]
    MC[Multiple cariers management ]
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
subgraph AL[Control layer]
    AO[Origin plant app]
    AC[Carier app]
    AW[Warehouse app]
    AM[Manufacturing plant app]
    AT[Track app]
end
subgraph PL[Physical layer]
    subgraph PL3 [Camera tracking]
        T[Track]
    end
    subgraph PL1[ESP Microbit]
    C[Carrier]
    end
   
    subgraph PL2[JetMax robotic arm]
        W[Warehouse]
        M[Manufacturing plant]
        O[Origin plant]
    end
end
AT---T
AO---O
AC---C
AW---W
AM---M

CO---AO
CC---AC
CW---AW
CM---AM
CT---AT



MC---|HTTP API|CC
MO---|HTTP API|CO



style CL fill:#FAD7A0, stroke:#F5B041
style PL fill:#AED6F1, stroke:#3498DB
style ML fill:#ABEBC6, stroke:#28B463
style AL fill:#D7BDE2, stroke:#884EA0


```

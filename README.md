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

subgraph ML[Management layer]
    MC[Multiple cariers management ]
    MPP[Multiple packages management]
end
subgraph CL[Communication layer]
    subgraph CL1[HTTP servers]
    CC[Carrier]
    CO[Master plant]
    CM[Manufacturing plant]

    MCM[Multiple cariers]
    CPP[Multiple packages]


    end
    
end
subgraph AL[Control layer]
    AO[Master plant app]
    AC[Carrier app]
    AM[Manufacturing plant app]
   
end
subgraph PL[Physical layer]
    subgraph PL1[ESP Microbit]
    C[Carrier]
    end
   
    subgraph PL2[JetMax robotic arm]
        M[Manufacturing plant]
        O[Master plant]
    end
    P[Packages]
end

AO---O
AC---C
AM---M

CO---AO
CC---AC
CM---AM


MPP---CPP---P

MC---|HTTP API|CC



style CL fill:#FAD7A0, stroke:#F5B041
style PL fill:#AED6F1, stroke:#3498DB
style ML fill:#ABEBC6, stroke:#28B463
style AL fill:#D7BDE2, stroke:#884EA0


```
# Blockchain layer

# Management layer
## Multiple carriers managment
## Multiple packages managment

# Communication layer

## Carrier HTTP API
## Multiple carriers HTTP API
## Master plant HTTP API
## Manufacturing plant HTTP API
## Multiple packages HTTP API

# Control layer
## Carrier control app
## Master plant control app
## Manufacturing plant control app



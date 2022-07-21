# Shared Manufacturing Platform

```mermaid
graph TD

subgraph ML[Management layer]
    MCM[Multiple carriers management ]
    MPM[Multiple packages management]
end
subgraph CL[Communication layer]
    subgraph CL1[HTTP servers]
    CC[Carrier]
    CM[Master plant]
    CMFG[Manufacturing plant]
    CMC[Multiple carriers]
    CMP[Multiple packages]
    end  
end
subgraph AL[Control layer]
    CTM[Master plant app]
    CTC[Carrier app]
    CTMFG[Manufacturing plant app]
   
end
subgraph PL[Physical layer]
    subgraph PL1[ESP Microbit]
    PC[Carrier]
    end
   
    subgraph PL2[JetMax robotic arm]
        PMFG[Manufacturing plant]
        PM[Master plant]
    end
    P[Packages]
end

MCM---|HTTP API|CM
MCM---|HTTP API|CMFG
CMFG---CTMFG---PMFG
CM---CTM---PM
MPM---|HTTP API|CMC

MPM---CMP---P

MCM---|HTTP API|CMC---|HTTP API|CC---CTC---PC



style CL fill:#FAD7A0, stroke:#F5B041
style PL fill:#AED6F1, stroke:#3498DB
style ML fill:#ABEBC6, stroke:#28B463
style AL fill:#D7BDE2, stroke:#884EA0


```

# Communication between nodes

The diagram shows an example of communication when the package is transported from the master plant (warehouse) to one of the manufacturing plants. A similar sequence is used when the package is transported in the opposite direction.

```mermaid

sequenceDiagram
participant P as Package
Note over P: Order for mfg has<br> been confirmed
participant MC as Multiple carriers <br>management
participant C as Carrier
participant M as Master plant 
participant MFG as Manufacturing plant 

P->>MC: request(packageId, source <br>location, target location)
MC-->>P: response(status (accept/reject), queueIndex, taskId)
MC->>C: move(source location)
C-->>MC: response(accept/reject)
Note over C: Carrier moving to <br>source location
C->>MC: report(success/error)
MC-->>C: response(success/error)
MC->>M: dispatch(packageId)
M-->>MC: response(accept/reject, dispatch task id)
Note over M: dispatch process
M->>MC: dispatchFinished(taskId)
MC-->>M: response(success/error)

MC->>C: move(manufacturer location)
C-->>MC: response(accept/reject)
Note over C: Carrier moving to <br>target location
C->>MC: report(success/error)
MC-->>C: response(success/error)
MC->>MFG: dispatch(packageId)
MFG-->>MC: response(accept/reject, dispatch task id)
Note over MFG: dispatch process
MFG->>MC: dispatchFinished(taskId)
MC-->>MFG: response(success/error)
MC->>P: transportFinished <br>(taskId)

MC->>C: move(next task/parking)
C-->>MC: response(accept/reject)
Note over C: Carrier moving to <br>parking area
C->>MC: report(success/error)
Note over MC: Delete request<br>from queue


```

# Blockchain layer

# Management layer
## Multiple carriers management
* maintains a queue of active requests
* maintains data of active robot cars, manufacturing plants and master plant (warehouse)
* periodically processes requests on a first-come, first-served basis
* randomly selects an available car for transport and requests the move
* requests a dispatch at a plant
* after the transport finishes sends the car to the parking area

## Multiple packages management

# Communication layer

## Carrier HTTP API

| API endpoint | description | parameter(s) | returns |
| ------------ | ----------- | ------------ | ------- |
| <code>/move</code> | Move from source to target location | <code>msg={"source": a, "target" = b, "taskId"=? }</code> |{accept/reject}|

## Multiple carriers HTTP API

| API endpoint | description | parameter(s) | returns |
| ------------ | ----------- | ------------ | ------- |
| <code>/request</code> | Request transportation of package. Request goes to queue as task | <code>msg={"source": a, "target" = b, "packageId" = c}</code> |{status (accept, reject), queueIndex, taskId}|
| <code>/report</code> | Report on state of the task | <code>msg={"taskId": a, "state" = error/done }</code> ||
| <code>/getTask</code> | Get state of the task | <code>msg={"taskId": a }</code> |{task state}|

## Master plant HTTP API
## Manufacturing plant HTTP API
## Multiple packages HTTP API

| API endpoint | description | parameter(s) | returns |
| ------------ | ----------- | ------------ | ------- |
| <code>/transportFinished</code> | Infor the package that the transport was successfully finished | <code>msg={"taskId": a</code> |{success/error}|

# Control layer
## Carrier control app
* Arduino application for DFRobot micro:Maqueen Plus controlled by Mbits ESP32 microcontroller
* manages the movement of the robot car
* movements depend on a pre-defined grid
* each move is defined by a start (source) and end (target) location
* the car always drives in anti-clockwise direction
* the app assumes one of six crossroad variants as seen below
![grid-krizisca](https://user-images.githubusercontent.com/87708323/180209941-a22c9453-83d5-4831-95d7-5888382e7fcc.png)
	* variant 1: compulsory left turn (grid outside corners)
	* variant 2: compulsory right turn (car coming out of the parking area)
	* variant 3: turn left (if coming out of the parking area) or turn right (if coming out of the plant area)
	* variant 4: turn left if this is the correct parking area (counter)
	* variant 5: turn right if this is the correct plant (counter)
	* variant 6: stop (at the plant or at the parking area
	

## Master plant control app
## Manufacturing plant control app

---

# Applications
## JetMax Robotic Arm HTTP server: 

Repository: https://github.com/fsprojekti/shr-mfg-robotic-arm-http-server
## Carrier management Node.js application: 

Repository: https://github.com/fsprojekti/df-micro-maqueen-robot-cars-control-app

## Carrier Arduino app:

Repository: https://github.com/fsprojekti/df_micro_maqueen-mbits-esp32_arduino_app


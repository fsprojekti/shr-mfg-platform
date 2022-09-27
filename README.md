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
participant A as Package
participant B as Pool
participant C as Manufacturer
participant D as Car controller
participant E as Warehouse
A->>B: sendOffer(price, endDate) BC
note over B: Offer waiting to be accepted or exipred
C->>B: acceptOffer(offerId) BC
loop Checking offers
    A-->B: offer accepted?
end
A->>D: request(packageId, offerId, source, target) WEB
note over D: transport moves to warehouse
D->>E:dispatch(offerId, mode) WEB
note over E: Dispatching package <br>from warehouse to trasnsport
E->>D: dispatchFinished(offerId) WEB
note over D: transport moving to manufacturer

D->>C:dispatch(packageAddress, offerId, mode) WEB
note over C: Dispatching package
C->>D: dispatchFinished(offerId) WEB
D-->>A: transportFinished(pakageAddress, offerId) WEB
note over C: Processing package 3min
C->>A: processingFinished(offerId) WEB
A->>D: request(packageAddress, offerId, source, target) WEB
note over D: transport moves to manufacturer

D->>C:dispatch(packageAddress, offerId, mode) WEB
note over C: Dispatching package <br>from warehouse to trasnsport
C->>D: dispatchFinished(offerId) WEB
note over D: transport moving to warehouse

D->>E:dispatch(packageAddress, offerId, mode) WEB
note over E: Dispatching package <br>from trasnsport to warehouse
E->>D: dispatchFinished(offerId) WEB

D-->>A: transportFinished(pakageAddress, offerId) WEB

```

# Blockchain layer

# Management layer

## Multiple carriers management

* maintains a queue of active transport requests
* maintains data of active robot cars, parking areas, manufacturing plants and master plant (warehouse)
* periodically processes requests on a first-come, first-served basis
* randomly selects an available car for transport and requests the move
* requests a dispatch at a manufacturing plant
* after the transport finishes sends the car to randomly selected available parking area

## Multiple packages management

# Communication layer

## Carrier HTTP API

| API endpoint | description | parameter(s)            | returns                 |
| ------------ | ----------- |---------------------------|-------------------------|
| <code>/move</code> | Move from source to target location | <code>msg={"source": int, "target": int, "taskId": String}</code> | {status: accept/reject} |

## Multiple carriers HTTP API

| API endpoint          | description                                    | parameter(s)                                                                            | returns                                                        |
|-----------------------|------------------------------------------------|-----------------------------------------------------------------------------------------|----------------------------------------------------------------|
| <code>/request</code> | Request transportation of a package. Request goes to the queue as a task. | <code>msg={"source": int, "target": int, "packageId": String, "offerId": String}</code> | {"state": accept/reject, "queueIndex": int, "offerId": String} |
| <code>/report</code>  | Report sent by the car when it reaches the target location | <code>msg={"taskId": String, "state": error/done }</code>                               | {"state": success/error/reject}                                | 
|<code>/getTask</code> | Get state of the task  | <code>"taskId": String, "state": "done"</code>                                                         | {"state": state/error/reject}                                  |

## Master plant HTTP API

## Manufacturing plant HTTP API

## Multiple packages HTTP API

| API endpoint | description                                                                            | parameter(s)                   | returns |
|----------|----------------------------------------------------------------------------------------|--------------------------------|---------|
| <code>/transportFinished</code> | Multiple carriers app informs the package that the transport was successfully finished | <code>"offerId": String</code> |         |

# Control layer

## Carrier control app

* Arduino application for DFRobot micro:Maqueen Plus controlled by Mbits ESP32 microcontroller
* it manages the movement of the robot car
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

* Node.js application for Jetmax robotic arm that acts as the master plant = warehouse
* it works similarly to any manufacturing plant
* it receives dispatch requests from the multiple carriers control app and ads the tasks to the queue
* it processes tasks in sequential order and move packages from the carrier to the warehouse, from the warehouse to the carrier or internally inside the warehouse to optimize package storage and manipulation
* it maintains a state of the warehouse which include the exact location of all the packages in storage docks, receive and dispatch buffers
* 
## Manufacturing plant control app

---

# Applications

## JetMax Robotic Arm HTTP server:

Repository: https://github.com/fsprojekti/shr-mfg-robotic-arm-http-server

## Carrier management Node.js application:

Repository: https://github.com/fsprojekti/df-micro-maqueen-robot-cars-control-app

## Carrier Arduino app:

Repository: https://github.com/fsprojekti/df_micro_maqueen-mbits-esp32_arduino_app

## Package management app:

Repository: https://github.com/fsprojekti/shr-mfg-platform-packages

## Smart contracts:

Repository: https://github.com/fsprojekti/shr-mfg-platforms-smart-contracts


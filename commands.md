### Software:

| Command   | Description                     | Range(x)       | Default value                       | Test value                          | Return message(default)                                     |
|:----------|:--------------------------------|:--------------:|:-----------------------------------:|:-----------------------------------:|:---------------------------------------------------:|
| `<r0>`    | Read0; read common settings     |                |            |           | {"vM":1,"cA":100,"pN":0,"bP":150}                |
| `<vM:x>`  | vM; voltageMonitor              | 0-1            | 1                                   | 1                                   |                                                    |
| `<cA:x>`  | cA; calibrAtion                 | 80-120         | 100                                 | 100                                 |                                                    |
| `<pN:x>`  | pN; printNotes                  | 0-1            | 0                                   | 1                                   |                                                    |
| `<bP:x>`  | bP; battery protection          | 0/148-154      | 150                                 | 150                                 |                                                    |
| &nbsp;    | &nbsp;                          | &nbsp;         | &nbsp;                              | &nbsp;                              | &nbsp;                                             |
| `<r1>`    | Read1; read DC/DC-1 settings    |                |    |     | {"tB1":300,"tS1":300,"vH1":125,"vL1":110}        |
| `<tB1:x>` | tB1; timeBOOT1                  | 1-900          | 300                                 | 10                                  |                                                    |
| `<tS1:x>` | tS1; timeSHDWN1                 | 1-900          | 300                                 | 10                                  |                                                    |
| `<vH1:x>` | vH1; volt.High1                 | 100-130        | 125                                 | 110                                 |                                                    |
| `<vL1:x>` | vL1; volt.Low1                  | 100-130        | 110                                 | 105                                 |                                                    |
| &nbsp;    | &nbsp;                          | &nbsp;         | &nbsp;                              | &nbsp;                              | &nbsp;                                             |
| `<r2>`    | Read2; read DC/DC-2 settings    |                |       |      | {"tB2":1,"tS2":1,"vH2":125,"vL2":110}            |
| `<tB2:x>` | tB2; timeBOOT2                  | 1-900          | 1                                   | 10                                  |                                                    |
| `<tS2:x>` | tS2; timeSHDWN2                 | 1-900          | 1                                   | 10                                  |                                                    |
| `<vH2:x>` | vH2; volt.High2                 | 100-130        | 125                                 | 120                                 |                                                    |
| `<vL2:x>` | vL2; volt.Low2                  | 100-130        | 110                                 | 115                                 |                                                    |
| &nbsp;    | &nbsp;                          | &nbsp;         | &nbsp;                              | &nbsp;                              | &nbsp;                                             |
| `<r3>`    | Read3; read DC/DC-3 settings    |                |   |     | {"tB3":600,"tS3":600,"vH3":125,"vL3":110}        |
| `<tB3:x>` | tB3; timeBOOT3                  | 1-900          | 600                                 | 10                                  |                                                    |
| `<tS3:x>` | tS3; timeSHDWN3                 | 1-900          | 600                                 | 10                                  |                                                    |
| `<vH3:x>` | vH3; volt.High3                 | 100-130        | 125                                 | 130                                 |                                                    |
| `<vL3:x>` | vL3; volt.Low3                  | 100-130        | 110                                 | 125                                 |                                                    |
| &nbsp;    | &nbsp;                          | &nbsp;         | &nbsp;                              | &nbsp;                              | &nbsp;                                             |
| `<reset>` | Reset all to default            |                |                                     |                                     |                                                    |
| `<test1>` | Set all to test values          |                |                                     |                                     |                                                    |
| `<ver>`   | Software version                |                |                                     |                                     | {"ver:2.3"}                                       |

<br/><br/> 
<br/><br/> 
### `<r0>`    -    common settings description:

#### - pN = 1 (printNotes, 0=disable, 1=enable)

When pN (printNotes) is enabled, the following messages are sent via USB:

- When vH1 set voltage is reached:

```
“DC/DC 1 ON, stop monitoring, start countdown tB1 timer”
```

DC/DC-1 is turned ON. Voltage monitoring for this DC/DC is paused for the duration of tB1, ensuring the full boot procedure.
<br/><br/> 

- When the set time tB1 expires:

```
“Expired tB1 timer, start vL1 monitoring”
```

Voltage monitoring resumes, waiting for the set value vL1.
<br/><br/> 

- When the set voltage vL1 is reached:
```
“vL1 detected, stop monitoring”
“Sending command for shutdown via USB...”
“start countdown tS1 timer”
```

Additionally, a JSON message is sent (this is always sent, independent of pN):
```
{"DC1OFF": true}
```
This indicates that the tS1 countdown has started. When it expires, DC/DC-1 will be turned OFF.
The JSON message can be used in Node-RED, for example, to trigger a NAS shutdown command.
<br/><br/>

- When the set time tS1 expires:
```
“Expired tS1 timer, DC/DC 1 OFF, waiting…”
```
DC/DC-1 is now OFF and waits for the next cycle.
      
```
“Start vH1 monitoring…”
```
<br/><br/>
<br/><br/>


#### - bP = 0/148-154 (batteryProtection, 0=disable, 148-154=enable)

If bP is enabled, when battery voltage reach set value, JSON message {„BOVP”:true} will be sent. After 2sec, all DC/DC will be OFF.
To reset DOCzilla, it is necessary to disconnect the battery and DC power supply at the same time.
This is planned to be changed so that the reset is done via the reset button.

#### - cA = 80-120 (calibrAtion, 105 is 1.05)
Used to set the battery voltage level display, sent as a JSON message: {"bV":12.455}.

#### - vM = 0/1 (voltageMonitor, 0=disable, 1=enable (interval 500mS) )
Enable or disable sending JSON (  {"bV":12.455} ) battery voltage messages via USB.

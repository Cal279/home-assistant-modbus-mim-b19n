# home-assistant-modbus-mim-b19n
Integration of a Samsung heat pump with Home Assistant via a MIM-B19N modbus interface.

## Standard Registers (MIM-B19N Official)

| PDU Address | Description | R/W | Signal Values | Notes |
|---|---|---|---|---|
| 0 | Modbus interface module error status | R | b0=Address error, b1=R1/R2 comms error, b2=Tracking error | |
| 1 | Integrated outdoor unit error code | R | 0=No error, 100-999=Error code | |
| 2 | Outdoor defrost operation | R | 0 or 0xFF=Off, Others=On | |
| 3 | Buzzer on/off | W | 0=On, 1=Off | |
| 50 | Communication status | R | b0=Exist, b1=Type OK, b2=Ready, b3=Comms error | IU address 0 |
| 51 | Unit type | R | HE=110, HT=120, EHS=115-117 | IU address 0 |
| 52 | Air conditioner on/off | R/W | 0=Off, 1=On | |
| 53 | Operating mode | R/W | 0=Auto, 1=Cool, 2=Dry, 3=Fan, 4=Heat | |
| 54 | Indoor fan speed | R/W | 0=Auto, 1=Low, 2=Middle, 3=High | |
| 57 | Set temperature | R/W | Celsius ×10 | |
| 58 | Room temperature | R | Celsius ×10 | Indoor unit sensor |
| 63 | Indoor unit error code | R | 0=No error, 100-999=Error code | |
| 65 | Water-in / Return temperature | R | Celsius ×10 | |
| 66 | Water-out / Flow temperature | R | Celsius ×10 | |
| 68 | Water-out set temperature / CH flow setpoint | R/W | Celsius ×10, Heat: 25-55°C | |
| 72 | Hot water on/off | R/W | 0=Off, 1=On | |
| 73 | Hot water mode | R/W | 0=Eco, 1=Standard, 2=Power, 3=Force | |
| 74 | Hot water set temperature | R/W | Celsius ×10, EHS: 30-70°C | |
| 75 | Hot water temperature | R | Celsius ×10 | DHW tank temp |
| 78 | Quiet mode | R/W | 0=Off, 1=On | |
| 79 | Away mode | R/W | 0=Off, 1=On | |

---

## Extended Indoor Registers (NASA MessageSet — write IDs to address 7000, appear at PDU 82+)

| MessageSet ID | PDU Address | Description | Signed | Scale | Range | Unit |
|---|---|---|---|---|---|---|
| 0x4087 | 82 | Booster Heater | No | ×1 | 0=off, 1=on | - |
| 0x406C | 83 | Backup Heater | No | ×1 | 0=off, 1=on | - |
| 0x42E9 | 84 | Flow Sensor | No | ÷10 | 0-80 | L/min |
| 0x4067 | 85 | 3-Way Valve | No | ×1 | 0=room, 1=tank | - |
| 0x40C4 | 86 | Water Pump PWM | No | ÷10 | 0-100 | % |
| 0x406F | 87 | Temperature Reference | No | ×1 | 0=room, 1=water out | - |

---

## Extended Outdoor Registers (NASA MessageSet — write IDs to address 6000, appear at PDU 4+)

| MessageSet ID | PDU Address | Description | Signed | Scale | Range | Unit |
|---|---|---|---|---|---|---|
| 0x8001 | 4 | Operation Mode | No | ×1 | 0=stop, 1=safety, 2=normal, 3=balance, 4=recovery, 5=deice, 6=compdown, 7=prohibit, 8=linejig, 9=pcbjig, 10=test, 11=charge, 12=pumpdown, 13=pumpout, 14=vacuum, 15=caloryjig, 16=pumpdownstop, 17=substop | - |
| 0x8010 | 5 | Compressor 1 Status | No | ×1 | 0=off, 1=on | - |
| 0x8017 | 6 | Hot Gas 1 Status | No | ×1 | 0=off, 1=on | - |
| 0x8019 | 7 | Liquid Valve Status | No | ×1 | 0=off, 1=on | - |
| 0x8021 | 8 | EVI Bypass | No | ×1 | 0=off, 1=on | - |
| 0x801A | 9 | 4-way Valve Status | No | ×1 | 0=off, 1=on | - |
| 0x80AF | 10 | Base Heater | No | ×1 | 0=off, 1=on | - |
| 0x80D7 | 11 | PHE Heater | No | ×1 | 0=off, 1=on | - |
| 0x8204 | 12 | Outdoor Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x8206 | 13 | High Pressure | Yes | ÷10 | - | kgf/cm² |
| 0x8208 | 14 | Low Pressure | Yes | ÷10 | - | kgf/cm² |
| 0x820A | 15 | Discharge 1 Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x8217 | 16 | Compressor 1 Current | No | ÷10 | - | A (unreliable) |
| 0x8218 | 17 | Heat Exchanger Outlet Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x821A | 18 | Suction Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x821E | 19 | EVI In Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x8220 | 20 | EVI Out Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x8229 | 21 | Main EEV1 | No | ×1 | 0-10000 | ? |
| 0x8235 | 22 | Error Code | No | - | - | - |
| 0x8236 | 23 | Compressor 1 Order Frequency | No | ×1 | 0-1000 | Hz |
| 0x8237 | 24 | Compressor 1 Target Frequency | No | ×1 | 0-1000 | Hz |
| 0x8238 | 25 | Compressor 1 Current Frequency | No | ×1 | 0-1000 | Hz |
| 0x823B | 26 | DC Link Voltage | No | ×1 | ? | V |
| 0x823D | 27 | Outdoor Fan RPM | No | ×1 | - | Hz (unconfirmed) |
| 0x82DE | 28 | EVA In Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x8254 | 29 | IPM1 Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x8278 | 30 | Compressor OCT1 | No | ÷10 | ? | ? |
| 0x8280 | 31 | Compressor Top Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x829F | 32 | High Pressure Saturation Temperature | Yes | ÷10 | -41 to 150 | °C |
| 0x82A0 | 33 | Low Pressure Saturation Temperature | Yes | ÷10 | -41 to 150 | °C |

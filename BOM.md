# Bill of Materials (estimate)

Prices are estimates and must be replaced with actual purchase prices. Also available as [CSV](bill_of_materials.csv).

| Component | Purpose | Est. cost (₹) |
|---|---|---|
| ESP32 DevKit | Main controller, Wi-Fi and sensor acquisition | 500 |
| 4S cells + BMS board | Low-voltage demonstrator pack with cell balancing and protection | 750 |
| 4× DS18B20 sensors | Multi-point temperature monitoring | 400 |
| INA219 module | Pack current measurement over I²C | 120 |
| Cell-voltage sensing (MUX) | Individual cell voltage measurement | 150 |
| OLED / touch display | Local safety dashboard and Safety Score | 150 |
| Relay, 5 V fan, buzzer, LEDs | Protection / isolation output, thermal demo, alerts | 380 |
| microSD module + emergency stop | Safety Black Box logging and manual safe shutdown | 350 |
| Enclosure, wiring, connectors | Mechanical and electrical integration | 1,000 |
| **Itemised total** | | **3,800** |
| Miscellaneous allowance | Spares, connectors, shipping | 600 |
| **Estimated budget build** | | **≈ 4,400** |

Optional: MPU6050 motion sensor for vibration trend sensing.

## Interfaces

| Signal | Interface |
|---|---|
| Cell voltages (4) | Divider / MUX → ESP32 ADC |
| Pack current | INA219 over I²C |
| Temperature (4) | DS18B20 on shared 1-Wire bus |
| Cooling status, E-stop | Digital GPIO (E-stop is hard-wired) |
| Buzzer, LEDs, fan, relay, drive-permit | GPIO via relay / MOSFET stage |
| OLED, microSD | Serial interfaces |

## Indicative rule settings (to be calibrated on hardware)

| Parameter | Monitor | Warning | Critical |
|---|---|---|---|
| Cell-voltage imbalance ΔV | > 30 mV | > 60 mV | > 100 mV |
| Max cell temperature | > 37 °C | > 42 °C | > 50 °C |
| Temperature rise rate | Above normal trend | High | Very high with rising ΔT |
| Pack current | > 12 A | > 15 A | > 25 A |
| Sensor validity | — | Optional sensor invalid | Required sensor invalid → fail-safe |
| Cooling status | — | Cooling requested | Cooling failed while temp rising |

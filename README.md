# BMW-G31-coding
Coding for (pre-LCI) BMW G30/G31

Handsoff for Driving Assistant Pro, be very careful and only program if allowed in your country
SAS Module:
```
C_STA_ALC_HandsOffGeschwAusschaltGrenzeKmh from 0A (8) to FF
C_STA_ALC_HandsOffGeschwAusschaltGrenzeMph from 05 (5) to FF
C_STA_ALC_HandsOffGeschwEinschaltGrenzeKmh from 0A (8) to FF
C_STA_ALC_HandsOffGeschwEinschaltGrenzeMph from 06 (6) to FF
```

Automatic lane change (initiated with stalk) active in all countries:
```
C_SWA_Land_aktiv to FF FF FF FF
```

Speedometer (6WB) scale to 330 km/h (can be done with bimmercode):
```
dkombi2 ***8212; expert mode ***8212; 3000 > MPA_ENABLE  (this will change the scale to 330)
dkombi2 ***8212; expert mode ***8212; 3000 ***8212; MPA MSP VIEW ENABLE (optionally change color scheme to grey/white)
```

Speedometer move "needle" beyond 263 km/h (need to change scale to 330 km/h as well):
```
GESCHWINDIGKEIT_EFF_X -> to m_sport
GESCHWINDIGKEIT_ANZ_Y -> to m_sport
```

Launch control in DSC module (requires speedschalter AKTIV in EGS):
```
C_Funktion_LaunchControl_01_1 -> to AKTIV

```

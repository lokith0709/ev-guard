# Core Logic Pseudocode

Design outline only — not the final firmware.

## A. Main control cycle

```
loop every sample period:
    raw   = readSensors()              // V1-V4, I, T1-T4, fan, estop
    ok    = validate(raw)              // range, freshness, plausibility
    x     = filter(raw)                // moving average / smoothing
    f     = extractFeatures(x)         // dVcell, dT, dTdt, current
    rule  = evaluateRules(f)           // deterministic, with hysteresis
    anom  = anomalyScore(f)            // optional edge model
    loc   = localize(f, ok)            // suspected cell / condition
    risk  = sum(w[i] * R[i](f))        // normalized risk terms
    score = clamp(100 - risk, 0, 100)
    state = max(rule.severity, band(score))
    permit = interlock(state, ok, estop)
    act(state, loc, permit)            // buzzer, fan, relay, drive
    show(score, loc, reason); logBlackBox(...)
```

## B. Safe-Drive Interlock

```
function interlock(state, ok, estop):
    if not allRequiredValid(ok):  return NO_PERMIT   // fail-safe
    if estop == PRESSED:          return NO_PERMIT
    if state in (WARNING, CRITICAL): return NO_PERMIT
    if voltageBad or currentBad or tempTrendBad:
                                  return NO_PERMIT
    if inRecovery and not recoveryOK: return NO_PERMIT
    return PERMIT                                    // only when all checks pass
```

## C. Interlock states

`INIT (no permit) → NORMAL (permit) → WARNING (no permit) → CRITICAL (no permit) → RECOVERY (validated reset) → NORMAL`

![Interlock FSM](images/fig3-5-interlock-fsm.png)

## D. Features

```
ΔVcell = Vcell,max − Vcell,min
ΔT     = Tmax − Tmin
dT/dt  = (T[k] − T[k−n]) / (n · Δt)
Score  = clamp(100 − (wV·RV + wT·RT + wΔT·RΔT + wI·RI + wS·RS + …), 0, 100)
```

Score bands: green > 70, amber 40–70, red < 40.

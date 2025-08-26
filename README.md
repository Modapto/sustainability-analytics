# SustainabilityAnalyticsFMUs

FMU simulation models for sustainability analytics of 6-axis robotic modules (Kuka and ABB) with roller hemming or welding tools mounted.
Please note that the time input variable for FMI co-simulation is mandatory for all FMUs.

## Carbon emission FMU (modapto-cfp-*.fmu)

The FMU implements a general model for calculation of carbon emission based on energy mix factor and energy consumption (of 6-axis robotic modules (Kuka and ABB) with roller hemming or welding tools mounted)

## Fake Energy Consumption FMU (modapto-fecm*.fmu)

This is an intermediate step for the development of the Energy cosumption FMU, which implements a first model for calculation of energy consumption (of 6-axis robotic modules (Kuka and ABB) with roller hemming or welding tools mounted).

## Energy Consumption FMUs (modapto-ecm*.fmu)

The FMUs implement two models for calculation of energy consumption (of 6-axis robotic modules (Kuka and ABB) with roller hemming or welding tools mounted)
* a simple electrical model
* a kinetic/dynamic model

Both models use
* the independent `time`
* the inputs `start_measurement`, `stop_measurement`, and `reset_measurement`
* the outputs `measurement_state`, `timestamp_start`, `timestamp_stop`, `robot.used_energy`, and `robot.used_power`.

### Energy Consumption FMU - electrical model (modapto-ecm-electric*.fmu)

The electrical model calculates energy from the motor currents and a hard coded voltage of 230 V.

The model uses the inputs `max_current[…]` and `current[…]`

### Energy Consumption FMU - kinetic/mechanical/dynamic model (modapto-ecm-mechanic*.fmu)

The kinetic model uses the robot's kinematic chain, masses and inertias etc., and the robot joint values, velocities, and accelerations.

The kinematic model is built internally, depending on the input `robot.version`. Supported values are “KUKA KR2210 KRC2”, “KUKA KR360 R2830”, and “ABB6700 235 265”.

Used inputs: `robot.axis_val[…]`, `robot.axis_vel[…]`, `robot.axis_acc[…]`, `robot.toolMass`, `robot.version`.

Used parameters: `gravity` and `robot.version`

Unused inputs (so far): `robot.axis_jerk[…]`, `robot.press_hem_roller`, `robot.t_move`, `robot.t_weld`

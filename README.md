# SustainabilityAnalyticsFMUs

FMU simulation models for sustainability analytics of 6-axis robotic modules (Kuka and ABB) with roller hemming or welding tools mounted.
Please note that the time input variable for FMI co-simulation is mandatory for all FMUs.

When evaluating the FMUs, it is mandatory to fill in all parameters and input values during simulation to run the FMU correctly.

## Carbon emission FMU (modapto-cfp-*.fmu)

The FMU implements a general model for calculation of carbon emission based on energy mix factor and power consumption. This can for example be the power of  a 6-axis robotic module (Kuka and ABB) with roller hemming or welding tools mounted as described in the next section.

## Fake Energy Consumption FMU (modapto-fecm*.fmu, outdated)

This is an intermediate step for the development of the Energy cosumption FMU, which implements a first model for calculation of energy consumption (of 6-axis robotic modules (Kuka and ABB) with roller hemming or welding tools mounted).

The calculation is intentionally wrong. Its main purpose is to check connectivity of the FMU with the simulation environment.

## Energy Consumption FMUs (modapto-ecm*.fmu)

The FMUs implement two models for calculation of energy consumption (of 6-axis robotic modules (Kuka and ABB) with roller hemming or welding tools mounted). There are different variants of this model available depending on which model is used internally.

### Common considerations

Every model has an output for the currently used power of the robot. This output is called `robot.used_power`. It is always in W and represents the momentarily used power.

The models need to define the time interval(s) in which a measurement of the energy should take place. In order to define these intervals, the following signals are defined and need to be connected with any of the models below. These inputs will not be duplicated for sake of simpler reading.

| name | type | kind | unit | description |
|---|---|---|---|---|
| `start_measurement` | bool | input | | Flag t indicate the start of the measurement (positive egde) |
| `stop_measurement` | bool | input | | Flag to indicate the ending of the measurement (positive edge) |
| `reset_measurement` | bool | input | | Flag to indicate reset of the energy measurement (positive edge) |

By setting these values, the internal state machine of the model is controlled. The state machine has the following outputs that allow to inspect its state:

| name | type | unit | description |
|---|---|---|---|
| `measurement_state` | int | | Indicating the current state of the internal state machine. (0: inital, 1: started, 2: stopped, 3: resetted) |
| `timestamp_start` | float | | The point in time, at which a positive edge of `start_measurement` was detected |
| `timestamp_stop` | float | | The point in time, at which a positive edge of `stop_measurement` was detected |

Additionally, there is an output `robot.used_energy`, that represents the total (cummulated) energy (in Ws=J) the robot used in the time interval between started and stopped state (or the current time if the model was not stopped yet). This is only valid if the model was actually stared prior.

### Energy Consumption FMU - electrical model (modapto-ecm-electric*.fmu)

The electrical model calculates the required power from the motor power as reported from the controller. This can be done by means of the measured/calculated per-axis power values on the controller or the per-axis currents and a hard coded voltage of 230 V.

In the case of hard-coded voltages, the model has the following inputs/parameters required:

| name | type | kind | unit | description |
|---|---|---|---|---|
| `max_current` | float[6] | parameter | A | The maximal current of the axis motors as defined in the data sheet. |
| `current` | float[6] | input | % | Current of the motors, normalized according to max_current (0 to 100) |

Additionally, the inputs under [common considerations](#common-considerations) need to be taken into account.

### Energy Consumption FMU - kinetic/mechanical/dynamic model (modapto-ecm-mechanic*.fmu)

The kinetic model uses the robot's kinematic chain, masses and inertias etc., and the robot joint values, velocities, and accelerations to cacluclate the used power needed by the mechanical (sub-) system. The mechanical power is provided by motors and power electronics that have their individual power characteristics.

| name | type | kind | unit | description |
|---|---|---|---|---|
| `robot.version` | string | parameter | | The selected robot, supported values are “KUKA KR2210 KRC2”, “KUKA KR360 R2830”, and “ABB6700 235 265”. |
| `robot.tool_mass` | float | parameter | kg | Mass of the tool at TCP |
| `gravity` | float | parameter | m/s^2 | Gravity constant (typically 9.81) |
| `robot.axis_val` | float[6] | input | degree | Axis positions of the motors |
| `robot.axis_vel` | float[6] | input | degree/s | Axis velocities of the motors |
| `robot.axis_acc` | float[6] | input | degree/s^2 | Axis accelerations of the motors |

Additionally, the inputs under [common considerations](#common-considerations) need to be taken into account.

The kinematic model is built internally, depending on the input `robot.version`. The value must be present during initilization phase of the FMU and cannot change during model execution.

The inpute `robot.axis_jerk[…]`, `robot.press_hem_roller`, `robot.t_move`, and `robot.t_weld` are only historically present and not used so far.

### Energy consumption FMU - legacy combined model (modapto-ecm-[0-9-]*.fmu)

These models are legacy and no longer maintained. They combine the electrical and the mechanical model in a single FMU. As the two models shre little common signals on the input side, connection effort was quite hight. Thus, this kind of meta-model was rejected soon.

The fecm model is the more or less empty shell of this kind of models. Thus, the model I/O are the same as the fecm model. As this model is deprecated, the fecm is deprecated as well.

## Test input data

For each FMU, corresponding test input data in *.csv format is available. The correspondance is indicated by the name of the files.

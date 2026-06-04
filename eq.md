The proposed equation for the defined optimisation goal is as follows,
where costs are the price at timestamp $t$ multiplied by the sum of energy consumed or produced by each device in the given timestamp.

$$costs = \sum_{t \in T}{\left[ P_t \cdot \sum_{d \in D} E_{d,t} \right]}$$

where 
- $T$ – is a set of discrete time intervals in the optimisation horizon;
- $D$ – is a set of devices (loads);
- $P_t$ – is the electricity price at timestamp $t$;
- $E_{d,t}$ – is the electricity energy consumed or produced by device $d$ at time $t$.

For the model authors defined an overall constraint, where the maximum power
should not exceed the line capacity.

$$ \left|\sum_{d \in D} E_{d,t}\right| \le E_{max}: \forall t \in T$$

where
- $E_{max}$ - is a line capacity (5.2 kWh in our case).

For the devices with shiftable operational schedules additional constraints are defined. First of all, scheduled active period of the device should fit into time horizon or narrower operational window if such is defined.

$$ T_{min} \le t^s_d \ \wedge \ t^e_d \le T_{max}: t^e_d = t^s_d + l_d, \forall d \in D $$
where
- $t^s_d$, $t^e_d$ - are start and end timestamps of the period when the device $D$ is active;
- $l_d$ - is length of the active period for device $D$;
- $T_{min}$, $T_{max}$ - are operational window constraints, default to whole time horizon ($0$ and $T$, respectively).

Also the energy profile of the device for the whole time horizon should contain device specific energy profile of the active period *exactly once*.

$$ E_{d,t} = \left\{
\begin{array}{ll}
E^a_{d,i} & \text{if } i \in T^a\\
0 & \text{otherwise}
\end{array} \right. : \forall d \in D, t \in T$$

where
- $E^a_{d,i}$ - is the energy consumed or produced at the $i$-th interval (timestamp) as defined in device $d$ profile;
- $T^a$ - is active period of the device.

The battery (ESS) power profile is constrained by inflow (charging) and outflow (discharging) load constraints.
$$E_{out} \le E_t \le E_{in} : \forall t \in T, E_{out} < 0$$

where
- $E_t$ - is battery power at timestamp $t$;
- $E_{out}$ - is maximum outflow (discharging) power, specified as negative number;
- $E_{in}$ - is maximum inflow (charging) power.

Battery charge level is constrained by its maximum capacity and charging/discharging activities in previous timestamps.

$$C_t = C_s + \sum_{i=0}^{t}E_i : \forall t \in T$$

where
- $C_t$ - is battery charge level at timestamp $t$;
- $C_s$ - is battery charge level at start of modeling (i.e. current charge level);
- $E_i$ - is battery power at given timestamp $i$.

Optionally desired battery charge level can be specified for the last timestamp of time horizon (i.e. desired battery level for the next modelling period).

$$C_t = C_{e}: t = max(T)$$

where
- $C_{e}$ - is desired battery capacity at the end of time horizon $T$.


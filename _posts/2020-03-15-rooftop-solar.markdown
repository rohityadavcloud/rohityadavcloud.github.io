---
layout: post
category: projects
highlight: primary
title: Rooftop Solar PV Plant
---

Since last year I've been trying to [research, survey and
learn](https://twitter.com/rohityadavcloud/status/1126174634540392448) about solar energy
with the goal to setup a solar rooftop plant that can harness clean energy. In
this post I discuss and share my experience of deploying a microinverter based
6kW solar plant on my rooftop.

<div class="post-image">
  <img src="/images/solar/rooftop.jpg">
  <p>My 6kW Solar PV Rooftop Plant</p>
</div>

### Motivation and Concerns

- Sub-tropical [Indian climate](https://en.wikipedia.org/wiki/Climate_of_India)
ensures a lot of sunny days throughout the year, therefore there is potential of
rooftop solar to save the planet and electricity bills.
- [ISRO's solar calculator app that uses INSAT-3D
data](https://www.isro.gov.in/isro-develops-solar-calculator-android-app)
predicted that my location (Gurgaon) would get 8-10 hours+ of daily average
sunshine in a year and a rooftop area of 10mx5m or 50m^2 could generate about
40-50 kWh (units) a day.
- Pollution is a major problem and health hazard in most Indian cities including
my own. Most Indian power generation plants are coal-based, the growing trend
and adoption of renewable energy, EVs got me excited about rooftop-solar plant.
- A rooftop solar plant is expected to last 20-25 years and could pay itself in
4-10 years depending on cost, incentives, design and implementation, and other
factors.
- Overall it looked like a good investment and gave incentive to reduce carbon
emission.

### Scope and Requirements

- The solar plant should not have any single point of failure and it should be
easy to extend, service, maintain, monitor and certainly last for a very long
time (20-25 years).
- Based on electricity bills trends from last 3 years, I calculated my per day
consumption and used that to calculate the size of the plant I would require
ideally to have net zero electricity bill in a year.
- Per ISRO's app and other sources, I found that we can assume an average of 5
hours of sunshine per day in a year considering cloudy days, winter etc.
- I prefer to go with panels that can produce high (peak) power (400W+) so the
system would take less space on the rooftop.
- With the requirements, scope and limitations I could calculate the ideal solar
plant size and number of panels as follows:

```
    Avg Daily Units = ${Yearly Electricity Bill} / ( ${Rate of one kWh} * ${Days Per Year, 365} )
    PV Plant Size = ${Avg Daily Unit} / ${Avg Sunshine Hours} Total
    PV Panels = ${PV Plant Size} / ${Solar Panel Rating}

    # Illustration:
    Avg Daily Units = (INR 120000) / (INR 8/kWh * 365) = ~40kWh
    PV Plant Size = 40kWh / 5h = 8 kW
    Total PV Panels = 8000W / 400W = 20 panels
```

- Considering my available rooftop area and other factors I decided to start
with a 6.15kW solar PV system with 15x410Wp panels that may be expanded in
future depending on production and consumption.

### Economics

For cost calculations, I'm considering that the plant will generate [4.5kWh per
system kilo-watt](https://solarrooftop.gov.in/rooftop_calculator) (or 4.5x6.15 =
~27kWh) per day: (inflation or any incentive are not considered)

```
  Avg. Electricity Rate = INR 8/kWh
  PV Plant Size = 410Wp * 15panels = 6.15kW
  Annual Power Production = 6.15 x 4.5 x 365 = 10101 kWh
  Annual Electricity Bill Savings = INR 8/kWh * 10101 kWh = ~INR80k
```

The Solar PV Plant cost is subjective to market conditions, parts, installer
etc. Here are my ROI calculations:

```
  Time to complete ROI = ${ Total System Cost } / ${ Total Bill Savings }

  Total System Cost = 4.75L
  Time to complete ROI without subsidy/depreciation = ~6years

  Total System Cost after subsidy/depreciation = 70% x 4.75L = 3.33L
  Time to complete ROI with subsidy/depreciation = ~4years
```

For return on investment (ROI) comparison across assests, I compared the
electricity bill savings (considering no electricity kWh rate inflation) that
my solar PV plant would generate over a period of 25 years against different
assets:

- With an initial investment of 4.75L (same as cost of solar plant without
subsidy/depreciation) calculated against the same period but their post-tax
returns (i.e. investment+gain-tax, considering equity taxation @10%, fixed
deposit taxation @30%).

<div class="post-image">
  <img src="/images/solar/roi.png">
</div>

- With an initial investment of 3.33L (same as effective cost of solar plant
with depreciation, for FY21/22 of
[40% for renewable energy devices](https://www.incometaxindia.gov.in/charts%20%20tables/depreciation%20rates.htm))
calculated against the same period but their post-tax
returns (i.e. investment+gain-tax, considering equity taxation @10%, fixed
deposit taxation @30%).

<div class="post-image">
  <img src="/images/solar/roi-depreciation.png">
</div>

Based on the graph above and assuming no major maintenance costs throughout the
lifetime of the rooftop solar plant investment in solar rooftop PV is clearly
better than fixed deposits and even equity if subsidy/depreciation is available.

With the declining solar installation costs, this certainly becomes an
attractive investment. My total system cost was bit higher due to a custom-built
GI super-structure, therefore the ROI would greatly improve for deployments with
a basic rooftop structure, and with subsidy or
[depreciation](https://www.solarmaxx.co.in/accelerated-depreciation-tax-benefits-with-solar-explained/).

Some environmental [stats](https://solarrooftop.gov.in/rooftop_calculator) for
the 6.15kW solar plant (source: https://solarrooftop.gov.in/rooftop_calculator):

```
  Carbon dioxide emissions mitigated is: 170 tonnes
  Equivalent to planting: 272 Teak trees over the life time (data from IISc)
```

Future system expansion calculations for zero electricity bill: (not considering
inflation, panel degradation)

```
  Total Electricity Bill Outstanding = INR 120k-88k = INR 32k
  Est. System Expansion for zero bill = INR 32k / (INR 8kWh * 4.5kWh * 365) = ~2.4kW
  Est. Additional 410Wp Panels needed for zero bill = 2400 / 410 = ~6
```

### Design

- An off-grid setup was found to be expensive due to high cost of (Lithum)
batteries and would require more maintenance and could create concerns round
running high-load requiring appliances such as ACs, geysers or water pumps.
- An on-grid system with
[net-metering](https://en.wikipedia.org/wiki/Net_metering) was found to be most
feasible in terms of ROI especially for urban deployments where powercuts are
rare or infrequent.
- For on-grid systems, most discoms (power distributing companies) including
[DHBVN](https://esolarconn.dhbvn.org.in/) would install a net-meter that can
track power imported/exported from/to the grid and allow consumers to take
advantage of off-setting power consumed against power produced.
- Traditional on-grid systems have panels (that produce DC power)
connected in series (string-based where voltages add up per panel, current flow
is the same through panels) and typically has a one large DC-to-AC solar inverter
that connects to the property and the grid. This design has problems such as
many single points of failure, single inverter, shading issue (all panels
operate at the level of the lowest performing panel).
- Modern on-grid systems can use micro-inverters where a small DC-to-AC
micro-inverter is deployed for each solar panel that are connected in parallel
keep the same voltage (say 220-240VAC) across panels that allow each panel to
operate as an individual solar PV plant and therefore maximise generation.

### Parts and Components

A typical rooftop solar plant essentially consists of:
- solar panel(s): device that converts sunlight to DC power.
- invertor(s): device that converts DC power from solar panels to AC.
- balance of system: consists of wiring, ACDB (AC power distribution box that
can connect to the grid), circuit breakers (MCB, MCCBs), thimble/connectors, etc.
- batteries: (optional) to store excess generation, could be considered if
netmeters and on-grid connection is not possible.

<div class="post-image">
  <img src="/images/solar/design.png">
  <p>Typical Enphase based Rooftop Solar Plant layout (<a href="https://enphase.com/sites/default/files/downloads/support/IQ-7-7PLUS-micro-wiring.pdf">source</a>)</p>
</div>

#1 Solar Panels

Solar panels generally degrade over time, most manufactures guarantee 80% rated
wattage over 25years. That means, a 100W panel is rated to output 80W in 25years
time. IQ7+ peak output (AC) is documented as 295VA or 295W (at 1.0 power
factor). An Enphase whitepaper recommended DC-AC ratio of 1.1-1.2, therefore the
system requires a panel with at least 350W peak power rating.

After survey and consultation with Enphase sales team, [Hiku-410Wp](https://www.canadiansolar.com/hiku/)
solar PV panels were chosen.
These panels have 10-12years warranty and 80% power performance warranty for
25years, and unlike traditional solar panels they have [half-cut
cells](https://news.energysage.com/half-cut-solar-cells-overview/) that reduces
resistive power-losses and have higher shade tolerance. I found the industry trend
towards quarter-cut cells that may further reduce resistive losses.

<div class="post-image">
  <img src="/images/solar/panel.jpg"><br/>
  <p>Hiku 410Wp Panel</p>
</div>

#2 Micro-Inverters

Traditional solar inverters are string based where solar panels are connected in
series (they add voltages) and come with their [own bag of
issues](https://enphase.com/en-in/products/microinverters/vs-string-inverter).

After some survey and reading, I decided to go with [Enphase
IQ7+](https://enphase.com/sites/default/files/downloads/support/IQ7-IQ7plus-DS-EN-US.pdf)
[microinverters](https://en.wikipedia.org/wiki/Solar_micro-inverter).

- Microinverters are installed on every panel and can provide
[features](https://enphase.com/en-in/products/microinverters) such as per-panel
monitoring, and optimise power output on per-panel basis.
- They are more reliable (upto 25yrs warranty) and remove a
[SPOF](https://en.wikipedia.org/wiki/Single_point_of_failure) and provide
ability to maintain/service/replace on per-panel basis.
- More safe, connected in parallel and reduces DC losses.
- A microinverter-based solar plant system would also allow mixing panels of
different wattages as each panel-microinverter operates as an individual power
plant.

<div class="post-image">
  <img src="/images/solar/iq7plus.jpg">
  <p>Enphase IQ7+ Microinverter</p>
</div>

### Installer

Finding a good installer who can do EPC (engineering, procurement and
construction) is quintessential. I initially consulted many solar installers,
compared their solution and quotation to determine the best price-solution fit.

Finally, with Enphase sales team's consultation I went with one of their
official [channel partners](https://enphase.com/en-in/find-an-installer):
[SunSon Energy](https://www.instagram.com/sunsonenergy/).

### Implementation

Sunson installed a raised GI (galvanized-iron, so structure does not rust)
structure on my roof with concrete based footing. On this GI structure (as seen
in the photograph below) 15 Hiku 410Wp panels were mounted and 15
Enphase IQ7+ microinverters were installed. The 15 panel-microinverter units
were wired into three AC strings having 5 units of panel-microinverter each.
Each panel-microinverter unit works as an independent solar plant and on each
unit itself the microinverter converts DC power from solar panels to AC.

<div class="post-image"> <img src="/images/solar/structure.jpg"> </div>

Each of the three AC-strings from the units were connected to a [busbar junction
box](https://en.wikipedia.org/wiki/Busbar) and then the solar-AC cables were fed
to the three AC cables from the grid (with all the surge protection devices,
MCBs etc).

<div class="post-image">
  <img src="/images/solar/busbar.jpg">
  <p>Busbar Junction Box</p>
</div>

This central busbar junction box will allow extending the system in future
without requiring any changes to the balance-of-system.

### Production and Monitoring

The brain of the system is a gateway Linux-based IoT device called [Enphase
Envoy](https://enphase.com/en-in/products/envoy) that monitors and
[communicates](https://en.wikipedia.org/wiki/Power-line_communication) with the
microinverters and tracks the total power consumption and production in
real-time. Envoy periodically reports the stats to Enphase servers (typically
every 5mins).

<div class="post-image">
  <img src="/images/solar/envoy.jpg">
  <p>Enphase Envoy in AC Distribution Box (before deploying)</p>
</div>

Sunson helped configure and setup an account for me, and now I can monitor the
system power production, consumption using [Enphase
Enlighten](https://enlighten.enphaseenergy.com/):

<div class="post-image">
  <img src="/images/solar/production.jpg">
  <p>Generation on a clear sunny day</p>
</div>

Observing last 30 days (Feb-March), the average power production was at least
~4.5kWh per DC-kW (range of 3kWh to 5.6kWh per DC-kW depending on weather
conditions).

The Enphase system also provides a software based kill-switch to stop production
that can be done via the local Envoy connection or by the installer. It also
allows to configure custom power profiles to say (a) not export to the grid, (b)
export to the grid.

<div class="post-image">
  <img src="/images/solar/envoy-local.jpg">
  <p>Envoy Installer Page</p>
</div>

### Safety

- During a grid-powercut, the Enphase system will stop production and not export
any electricity to the grid.
- Microinverters connected in parallel ensure that voltages are limited
(240VAC).
- Circuit breakers, surge protectors and MCCB ensure all parts of the systems
can be isolated, shutdown or be tripped in case of a fault.
- The system has three separate ground wires (1) for the structure, (2) for the
components (panels and microinverters) and (3) for a lightning conductor.
- The heavy ground-mounts (footings) and wind-gaps ensure stability to the
structure.

### Maintenance

The system has no moving parts and under normal circumstances would only require
cleaning the panels every few weeks. In a rough experiment over three weeks, I
found that unclean panels generate about 4-8% less electricity over cleaner
ones and it depends on weather as well particulate matter in the air. Cleaning
the panels every two weeks have yielded satisfactory results so far.

<div class="post-image">
    <img src="/images/solar/cleaning.jpg">
</div>

### Timeline

```
Early 2019: Research and Survey
18 Dec 2019: IQ7+ availability confirmed
01 Jan 2020: EPC proposal received
06 Jan 2020: EPC contract signed
18 Jan 2020: GI structure mounted, civil work in progress
19 Jan 2020: Panels deployed
25 Jan 2020: Microinverters installed, civil work completed
28 Jan 2020: BoS installed, system (no-export) tested
20 Feb 2020: Net meter installed, system export on
08 Mar 2020: High record - 5.5kWh per system/DC kW tested
30 Jun 2020: Between March-June 2020, it produced 3.4MWh or ~4.58kWh per system kW per day
27 Sep 2021: Added 10 mono-perc panels with IQ7A (total plant capacity of 10kW)
```

[Public Dashboard](https://enlighten.enphaseenergy.com/pv/public_systems/9NBa1754143/overview)

### Conclusion

After deploying the system and observing it since January I'm satisfied with the
results so far. The daily production and ROI analysis show that the solar
rooftop investment would beat fixed deposits (post-taxes), save electricity
bills and promotes clean energy that would benefit the environment in the long
run.

If you're interested in deploying a rooftop solar plant for your home and
commercial property you can contact a local solar installer who would be able to
answer region specific questions and processes (as every power distribution
company have their own policies).

If you want to explore microinverter based rooftop solar plant similar to my own
deployment, you may contact:
- Enphase India by phone or email: [https://enphase.com/en-in/contact-us-india](https://enphase.com/en-in/contact-us-india)
- Find an Enphase Partner Installer: [https://enphase.com/en-in/find-an-installer](https://enphase.com/en-in/find-an-installer)
- SunSon Energy for Delhi/NCR region: [https://go.enphase.com/installer/sunson](https://go.enphase.com/installer/sunson), [gosolar@sunsonenergy.com](mailto:gosolar@sunsonenergy.com)

Hope this post will encourage others to consider solar rooftop for their needs
and promote clean renewable energy.

### Acknowledgements

- [Enphase](https://enphase.com/en-in/contact-us-india) (Sales: Rohit)
- [Sunson Energy](https://www.instagram.com/sunsonenergy/) ([Arshi](mailto:gosolar@sunsonenergy.com) and team - Feroz, Fateh, Mahendar)
- Thanks to [Pushkal](https://www.linkedin.com/in/pushkals/),
[Amar](https://www.linkedin.com/in/amar-parkash-71930329/),
[Abhishek](https://www.linkedin.com/in/shwstppr/),
[Himadri](https://www.linkedin.com/in/himadrisarkar/), and
[Paras](https://twitter.com/paraschopra) for reviewing this post.

<div class="post-image">
    <img src="/images/solar/gracie.jpg">
    <p>Gracie wonders - how these unclean panels generate clean energy!</p>
</div>

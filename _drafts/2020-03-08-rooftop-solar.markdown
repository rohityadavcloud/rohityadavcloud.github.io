---
layout: post
category: projects
highlight: primary
title: Rooftop Solar
---

Since last year I've been trying to [research, survey and
learn](https://twitter.com/rhtyd/status/1126174634540392448) about solar rooftop
with the goal to setup a solar plant on my house roof that can harness clean
solar energy. In this post I discuss and share my experience of my microinverter
based 6kW solar rooftop plant.

<div class="post-image">
    <img src="/images/solar/rooftop.jpg">
</div>

### Motivation

- Pollution is a major problem and health hazard in most Indian cities including
my own. Most power generation plants are coal-based, renewable energy such as
rooftop-solar needs adoption.
- Sub-tropical [Indian
climate](https://en.wikipedia.org/wiki/Climate_of_India) ensures a lot of sunny
days throughout the year.
- [ISRO INSAT-3D
data](https://www.isro.gov.in/isro-develops-solar-calculator-android-app) for my
location predicts 10 hours+ of sunshine in a year every day and a rooftop
area of 10mx5m or 50m^2 can generate about 40-50 kWh (units) a day.
- A rooftop solar plant is expected to last 20-25 years and could pay itself in
4-10 years depending on cost, incentives, design and implementation, and other
factors. This looked like a good investment.

### Requirements

- Per my past annual consumption trends and bills, my per day consumption was
found to be around 40kWh.
- The solar plant should be not have any single point of failure, easy
maintenance, observability/monitoring and easy to extend/service.
- Per ISRO app and predictions, on an average a 1kWh solar plant that can generate
4-4.5kWh in a day. Therefore, per my needs I would require a 10kW solar plant.
- To hedge risk, I decided to start with a ~5kW rooftop solar plant.

### Feasibility and Design

- An off-grid setup was found to be expensive due to high-cost Li-batteries.
- An on-grid system with
[net-metering](https://en.wikipedia.org/wiki/Net_metering) was found to be most
feasible, where during the day the solar plant can produce and export power in
excess that may be consumed during cloudy days and night.
- Most discoms (power distributing companies) including
[DHBVN](https://esolarconn.dhbvn.org.in/) would install a net-meter.
- On-grid systems with net-metering was found to significantly improve the solar
plant RoI.

### Parts and Components

#1 Inverters

Solar panels generate DC electricity that need to be converted to AC using an
inverter device. Traditional solar inverters are string based where solar panels
are connected in series (they add voltages) and come with their [own bag of
issues](https://enphase.com/en-in/products/microinverters/vs-string-inverter).

After some survey and reading, I decided to go with
[microinverters](https://en.wikipedia.org/wiki/Solar_micro-inverter) instead
of traditional string-based solar inverters:

- Microinverters are installed on every panel and can provide
[features](https://enphase.com/en-in/products/microinverters) such as per-panel
monitoring, and optimise power output on per-panel basis.
- They are more reliable (10-25yrs warranty) and remove a
[SPOF](https://en.wikipedia.org/wiki/Single_point_of_failure) and provide
ability to maintain/service/replace on per-panel basis.
- More safer, connected in parallel (than series therefore voltage is limited
and not some 100-1000s of volts) and reduces DC losses (DC to AC conversion
per panel basis).
- A microinverter-based solar plant system would also allow mixing panels of
different wattages as each panel-microinverter operates as an individual power
plant.

After surveying available options, I went with [Enphase IQ7+
microinverters](https://enphase.com/sites/default/files/downloads/support/IQ7-IQ7plus-DS-EN-US.pdf):

<div class="post-image">
    <img src="/images/solar/iq7plus.jpg">
    <p>Enphase IQ7+ Microinverter</p>
</div>

#2 Solar Panels

Solar panels generally degrade over time, most manufactures guarantee 80% rated
wattage over 25years. That means, a 100W panel is rated to output 80W in 25years
time. IQ7+ peak output (AC) is documented as 295VA or 295W (at 1.0 power
factor). An Enphase whitepaper recommended DC-AC ratio of 1.1-1.2, therefore the
system requires a panel with at least 350W peak power rating.

After survey and consultation with Enphase sales team, [Canadian Solar
Hiku-410Wp](https://www.canadiansolar.com/hiku/) solar PV panels were chosen.
These panels have 10-12years warranty and 80% power output in 25years, and
unlike traditional solar panels they have [half-cut
cells](https://news.energysage.com/half-cut-solar-cells-overview/) that reduces
resistive power loss and have higher shade tolerance. I found the industry trend
towards quarter-cut cells that may further reduce resistive losses.

<div class="post-image">
    <img src="/images/solar/panel.jpg"><br/>
    <p>Canadian Solar Hiku 410Wp Panel</p>
</div>

### Installer

Finding a good installer who can do EPC (engineering, procurement and
construction) is quintessential. I initially consulted many solar installers,
compared their solution and quotation to determine the best price-solution fit.

Finally, with Enphase sales team's consultation I went with one of their
official [channel partners](https://enphase.com/en-in/find-an-installer) [SunSon
Energy](https://www.instagram.com/sunsonenergy/).

### Implementation

Sunson installed a GI-based superstructure on my roof with concrete footing on
which 15 Canadian Solar Hiku 410Wp solar panels and 15 Enphase IQ7+
microinverters were installed grouped in three strings of 5 panel-microinverter
units each as my house has a 3-phase AC grid-connection.

<div class="post-image">
    <img src="/images/solar/structure.jpg">
</div>

Each of the three AC-string from the panels was connected to a [busbar junction
box](https://en.wikipedia.org/wiki/Busbar) and then the solar-AC cables were fed
to the three AC cables from the grid (with all the surge protection devices,
MCBs etc). The busbar enables the overall system to be extended in future if
more panel-strings are added without changing any other wirings and the
balance-of-system.

<div class="post-image">
    <img src="/images/solar/busbar.jpg">
    <p>Busbar Junction Box</p>
</div>

### Production and Monitoring

The brain of the system is a gateway Linux-based IoT device called [Enphase
Envoy](https://enphase.com/en-in/products/envoy) that monitors and
[communicates](https://en.wikipedia.org/wiki/Power-line_communication) with the
microinverters and tracks power consumption and production. Envoy periodically
reports the stats to Enphase servers.

<div class="post-image">
    <img src="/images/solar/envoy.jpg">
    <p>Enphase Envoy in AC Distribution Box (ACDB)</p>
</div>

Sunson helped configure and setup an account for me, and now I can monitor the
system power production, consumption using [Enphase
Enlighten](https://enlighten.enphaseenergy.com/):

<div class="post-image">
    <img src="/images/solar/production.jpg">
    <p>Enphase Enlighten App</p>
</div>

### Maintenance

The system has no moving parts and under normal circumstances would only require
cleaning of the panels every few weeks.

<div class="post-image">
    <img src="/images/solar/cleaning.jpg">
</div>

### Annual Returns Estimation

```
Total consumption = 40kWh * 365 = 14600 kWh
Total bill (Rs.8/kWh) = 14600 x 8 = Rs.116,800.0
Power production = 6.15 x 4.5kWh x 365 = 10101 kWh
DHBVN scheme (Re.1/kWh) = Rs. 10101
Amount saved = 10101 x (8+1) =  Rs.90,909.0
Amount outstanding = 116,800.0 - 90,909.0 = Rs. 25,891

Min. additional panels for zero bill = (25,891 * 1000) / (9 * 365 * 4.5 * 410 * 0.8) = ~6 panels
```

Assumptions:
- 40kWh per day avg. consumption
- 4.5kWh generation per solar DC kW
- DHBVN scheme of Re.1 per 1kWh solar produced unit
- Rs.8/kWh electricity cost
- ~5 years RoI
- No assumptions around inflation, minimum monthly cost

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

TODO: Add stats/updates here over next year
```

### Thanks

- Enphase Sales (Rohit)
- Sunson Energy (Arshi, Fateh, Feroz, Mahendar and team)
- DHBVN (Net-meter)

<div class="post-image">
    <img src="/images/solar/gracie.jpg">
    <p>Gracie wonders - how these unclean panels generate clean energy!</p>
</div>

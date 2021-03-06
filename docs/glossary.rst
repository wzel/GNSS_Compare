
********
Glossary
********


The aim of this section is to provide a small glossary on the subject of satellite navigation, Android and software engineering in general. You can find below descriptions of all used technical terms in this documentation.


Global Navigation Satellite Systems Glossary
============================================


.. _gnss:

Global Navigation Satellite Systems
-----------------------------------

A search on the internet about GNSS will give plenty of information about everything one needs to know. In this section we will
briefly present the current status of the existing global satellite navigation systems.

**Galileo**

Galileo is the European GNSS with the following status and characteristics:

- In orbit satellites: 22 ( *the launch of another 4 satellites being scheduled for 25th of July 2018*)
- Satellites orbit: Medium Earth Orbit (MEO) with an altitude of approximately 23000 kilometers
- Orbit inclination:  56 degrees to the equator
- Orbital planes: 3
- Time System : Galileo System Time (GST)
- Used spread-spectrum technique: Code Division Multiple Access (CDMA)



**GPS**

GPS is the American GNSS with the following status and characteristics:

- In orbit satellites: 31
- Satellites orbit: MEO with an altitude of approximately 20000 kilometers
- Orbit inclination:  55 degrees to the equator
- Orbital planes: 6
- Time System : GPS Time (GPST)
- Used spread-spectrum technique: CDMA


**GLONASS**

GLONASS is the Russian GNSS with the following status and characteristics:

- In orbit satellites: 25
- Satellites orbit: MEO with an altitude of approximately 19000 kilometers
- Orbit inclination:  65 degrees to the equator
- Orbital planes: 3
- Time System : GLONASS Time (GLONASST)
- Used spread-spectrum technique: Frequency Division Multiple Access (FDMA) and CDMA (recently)

**Beidou**

Beidou is the Chinese GNSS with the following status and characteristics:

- In orbit satellites: 28 ( *13 satellites are not included in operational orbital constellation*)
- Satellites orbit: Geostationary Orbit (GEO) and MEO
- Orbit inclination:  55.5 degrees to the equator for MEO
- Orbital planes: 3 (24 MEO)
- Time System : Beidou Time (BDT)
- Used spread-spectrum technique: CDMA


.. _pseudorange:

Pseudorange
-----------

For someone making his or hers first steps in the GNSS field, the term *pseduorange* might sound a little bit confusing. Afterall, the word *pseudo* is synonym with *false* and considering this, one might ask: why use this type of information? At the end of this section we hope to answer this question and also to make things more clear regarding this subject.

First let's start thinking (in general terms) how the receiver determines the distances towards the observed satellites. The range (R) is the difference between the time of signal reception and the time of signal transmission multiplied by the speed of light (c):

.. math::
  R = c \cdot (t_{R} - t^{S}).

Although the clocks (atomic clocks) of the satellites are highly accurate, they are still not perfect which lead them to be biased with respect to a certain GNSS time frame. Furthermore, considering that the quality of the clocks used in the typical GNSS receiver is inferior to the ones of the satellites, there is also a (significantly larger) bias in its time measurements. Therefore, let's take this into account in our equation expressed above:

.. math::
  R = c \cdot [t_{R}+\delta t_{R} - (t^{S} + \delta t^{S})].

If we arrange a bit the newly obtained expression, we get:

.. math::
  R = c \cdot (t_{R}-t^{S})+ c \cdot (\delta t_{R} - \delta t^{S}).

Assuming that the time of signal reception and the time of signal transmission are free of their biases and other error sources, then their difference multiplied by the speed of light can be viewed as the equivalent of the geometric distance (rho) in 3D between the receiver and the observed satellite!

.. math::
  R = \rho + c \cdot (\delta t_{R} - \delta t^{S}).

Now that we got this settled, we also need to account for the effects that disturb the signal's travel from the satellite to the receiver such as the ionosphere (I) and troposphere (T). The local effects like the receiver's noise and multipath which for the sake of simplicity we gather them in a single term (epsilon). The number of effects that introduce errors in the range measurements is larger and we don't cover them here.

.. math::
  R = \rho + c \cdot (\delta t_{R} - \delta t^{S}) + I + T + \epsilon.

In the equation of the range above we correct for the effect of the satellite clock bias, ionosphere, troposphere mainly by mathematical models. However, what we can't remove directly is the receiver clock bias which is required to be estimated. And that term will always be present in our measurements! Therefore, our *range* equation becomes the *pseudorange* (PR) equation because of that.

.. math::
  PR = \rho + c \cdot (\delta t_{R} - \delta t^{S}) + I + T + \epsilon.

We do hope that the aspects related to this subject are more clear now.




.. _ephemeris:

Ephemeris
---------

The process of obtaining the position in a certain coordinate system using GNSS technologies is based on a rather simple principle, which is *trilateration* (not triangulation, please be aware of that). Given an unknown point in a coordinate sytem from which we know the distances towards some known points in the same coordinate system, we can work out the coordinates of our unknown point. One can try this concept by defining a 2D coordinate system in which a triangle can be drew with two of its verticies having known coordinates. And the problem relies on finding the coordinates of the third vertex.

We have already seen in the *Pseudorange* section that we can obtain the range information towards the observed satellites. And what is missing is how to determine the coordinates of those satellites. To compute the coordinates of the satellite we need some parameters that describe their orbits. For this we have to be grateful to the work of Johannes Kepler on his law of planetary motion as he discovered the six parameters also known as the *Keplerian elements* that define an orbit:

- Eccentricity,

- Semimajor axis,

- Inclination,

- Longitude of the ascending node,

- Argument of periapsis,

- True anomaly.

The definition of all of the above elements can be easily found with a quick search on any internet search engine. The idea is that those parameters (and many others) are contained in the navigation message that is modulated on the transmissed GNSS signals. The receiver will extract this information from the signal itself or get them from external means (e.g., concept of Assisted GNSS) and feed them into specific algorithms that will determine the satellite coordinates.

A good source for the satellite coordinate computation algorithms for Galileo and GPS is the book called `ESA GNSS Data Processing Volume I`_ starting with page 57.

.. _pvt:

Position, Velocity and Time
---------------------------

In GNSS we are mostly interested in the parameters of the user that describe the position, velocity and also time. Position is quite obvious - that's the whole point of navigation, to know where the user is located. Velocity can be estimated from consecutive postion measurements, but can also be calculated directly from the satellite signals, due to the Doppler's effect. It can be later used for more precise estimations of the user's position, for highly dynamic systems. Time is also crucial to be estimated, as the user's receiver clock contains a bias with respect to a certain GNSS time frame.

For more clarifications on how this process is handled, the reader is welcomed to check the *Implemented PVT Algorithms* section.



.. _clockBias:

Clock bias
----------

In GNSS, when we talk about *clock bias* we usually refer to the satellite clock bias and/or to the receiver's clock bias with respect to a certain GNSS time frame (e.g., Galileo System Time, GPS Time).

Let's take a look on how we correct for the satellite clock bias as explained in `ESA GNSS Data Processing Volume I`_ (pages 104-105):

.. math::
  \delta t^{S} = \widetilde{\delta t}^{S} + \Delta t_{\text{rel}}.

In the above equation we can see that the satellite clock bias is also affected by a small relativistic effect caused by the orbit eccentricity. Is quite interesting to see that when dealing with time we do need to take into account these kind of phenomena! The correction for that relativistic effect is computed in the following way:

.. math::
  \Delta t_{\text{rel}} = -2~\frac{\mathbf{r}^{S} \cdot \mathbf{v}^{S}}{c^2},

where at the numerator we have the dot product between the satellite position vector and the velocity vector and at the denominator the speed of light squared.

And finally the satellite clock bias (without the relativistic effect) can be computed as:

.. math::
  \widetilde{\delta t}^{S} = a_0 + a_1(t-t_0)+ a_2(t-t_0)^2,

where the coefficients (a0, a1 and a2) are contained in the navigation message and t0 represents a given reference epoch.

Because we cannot account for the receiver clock bias beforehand we have to estimate it from the pseudorange equation:

.. math::
  PR = \rho + c \cdot (\delta t_{R} - \delta t^{S}) + I + T + \epsilon.

This one is more straight forward.




Android Glossary
================


Software Engineering Glossary
=============================

.. _polymorphism:

Polymorphism
------------

According to Wikipedia_, *Polymorphism is the provision of a single interface to entities of different types*. In Java this is achieved due to class inheritance.


.. _Wikipedia: https://en.wikipedia.org/wiki/Polymorphism_(computer_science)
.. _`ESA GNSS Data Processing Volume I`: https://gssc.esa.int/navipedia/GNSS_Book/ESA_GNSS-Book_TM-23_Vol_I.pdf

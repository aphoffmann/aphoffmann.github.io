---
permalink: /research/
title: "Improving Space Magnetometry"
---

My two research agendas are developing high fidelity spacecraft instrumentation and analysis of in situ spacecraft telemetry. My current research project investigates how to remove spacecraft noise from in magnetic field measurements.

## Noise Cancellation

Magnetometers are instruments that measure magnetic fields. Spacecraft electrical systems such as solar panels, magnetorquers, battery currents, and reaction Wheels create noise in the form of magnetic fields. These noise signals interfere with magnetic field data germane to scientific investigation. Figure 1 shows an example of noisy magnetometer data recorded by a japanese satellite.

![](/images/UBSS/michibiki.jpg){: .align-center}
**Figure 1:** Michibiki-1 Magnetometer Data polluted with Spacecraft Noise
{: .text-center}

The traditional approach to reduce spacecraft noise is to simply place the magnetometer on the end of a mechanical boom. The magnititude of stray magnetic fields decreases with distance, however, this approach significantly increases the design complexity and cost of a spacecraft. 

![](/images/UBSS/goes.jpg){: .align-center .width-half}
**Figure 2:** two magnetometers mounted on a boom on the GOES-XX satellite.
{: .text-center}

I developed an algorithm based on Underdetermined Blind Source Separation (UBSS). UBSS is a problem well researched in the field of acoustics. In UBSS, an array of M microphones is placed inside a room filled with N people having simultaneous conversations, such that N > M.  UBSS seeks to use the noisy microphone signals to isolate each voice. In a similar way, we use multiple magnetometers to isolate magnetic noise signals and separate the ambient magnetic field. 

Humans can do this automatically. If you focus on a specific person in a noisy room, you can often isolate their voice perfectly. The japanese emperor, 聖徳太子 (Shotoku Taishi), was rumored to be able to listen to ten advisors simultaneously during his reign in the 574 C.E.

![](/images/UBSS/UBSS.png){: .align-center}
**Figure 3:** Underdetermined Blind Source Separation.
{: .text-center}


The complication of using UBSS to identify magnetic noise signals is that magnetic fields have unique spatial structures. Magnetic signals have different phase depending on the **magnetic latitude** of the magnetometer with respect to the noise source. The gains and phases of magnetic noise signals and magnetometers is defined by a system called a **mixing matrix**.

### How We Solve It
Removing spacecraft noise from magnetic field data is a two step process. In the first step we identify noise signals using clustering. If you plot magnetometer data against each other, each source signal will draw straight lines. This data can be projected into a clusterable form to reconstruct the **mixing matrix**. Figure 4 shows this process.

![](/images/UBSS/clustering.jpg){: .align-center}
**Figure 4:** The plot on the left shows the magnetometer data in the time-frequency domain forming straight lines that correspond to each source signal. The plot on the right shows these signals projected into a clusterable form.
{: .text-center}

In order to discover the clusters in figure 4, we use an algorithm called [DBSCAN](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.DBSCAN.html). DBSCAN is a clustering algorithm that does not require the number of clusters to be known beforehand, and will also ignore noise points. The clusters that DBSCAN return define the mixing matrix of the system.

In the second step, the mixing matrix is used in a process called **Compressive Sensing**. Compressive sensing is a method originally for audio and video compression. Compressive sensing is used to reconstruct the source signals from the mixing matrix.

To test our algorithm, we used a mock cubesat shown in figure 5. In this experiment, electrical current is run through four copper coils in order to create magnetic noise signals. The noise signals are recorded by three PNI RM3100 magnetometers. 

![](/images/UBSS/cubesat.jpg){: .align-center .width-half}
**Figure 5:** The plot on the left shows the magnetometer data in the time-frequency domain forming straight lines that correspond to each source signal. The plot on the right shows these signals projected into a clusterable form.
{: .text-center}

### Results

We artificially inserted data from the SWARM mission in order to simulate the geomagnetic field. This produces the following signals as shown in figure 6.

![](/images/UBSS/MixedSignalsPNI.png){: .align-center .max-width: 100px}

**Figure 6:** Data from the three PNI RM3100 magnetometers shown in figure 5. These mixed signals are composed of two sine waves, two square waves, and the SWARM residual data.
{: .text-center}

The algorithm was tested on 100 seconds of this data. It correctly identified each of the noise signals. The reconstructed geomagnetic field signal is shown in figure 7.

![](/images/UBSS/recoveredGMF.png){: .align-center .width-half}
**Figure 7:** The recovered geomagnetic field signal in blue versus the true signal in orange.
{: .text-center}
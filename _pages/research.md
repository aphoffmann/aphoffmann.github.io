---
permalink: /research/
title: "Improving Space Magnetometry"

gallery:
  - url: UBSS/MixedSignalsPNI.jpg
    image_path: UBSS/MixedSignalsPNI.jpg
    alt: "placeholder image 1"
    title: "Data from the three PNI RM3100 magnetometers shown in figure 5. These mixed signals are composed of two sine waves, two square waves, and the SWARM residual data."
  - url: UBSS/recoveredGMF.jpg
    image_path: UBSS/recoveredGMF.jpg
    alt: "placeholder image 2"
    title: "The top plot (a) shows the cleaned magnetometer signal in blue with the ambient magnetic field signal overlayed in orange. Plot (b) shows a spectrogram of the uncleaned signal from magnetometer (a) in Figure 9. Plot (c) shows a spectrogram of the reconstructed ambient magnetic field signal. Plot (d) shows a spectrogram of the true ambient magnetic field signal. The spectrograms were created using wavelet analysis. The shaded areas indicate where the wavelet does not produce valid results. The bottom plot (d) shows a histogram of the signal reconstruction error."


---

My two research agendas are developing high fidelity spacecraft instrumentation and analysis of in situ spacecraft telemetry. My current research project investigates how to remove spacecraft noise from magnetic field measurements.

## Noise Cancellation

Magnetometers are instruments that measure magnetic fields. Spacecraft electrical systems such as solar panels, magnetorquers, battery currents, and reaction wheels create noise in the form of magnetic fields. These noise signals interfere with magnetic field data germane to scientific investigation. Figure 1 shows an example of noisy magnetometer data recorded by a japanese satellite.


![image-center](/images/UBSS/michibiki.jpg){: .align-center}
**Figure 1:** Michibiki-1 Magnetometer Data polluted with Spacecraft Noise
{: .text-center}

The traditional approach to reduce spacecraft noise is to simply place the magnetometer on the end of a mechanical boom. The magnititude of stray magnetic fields decreases with distance, however, this approach significantly increases the design complexity and cost of a spacecraft. 

![image-right](/images/UBSS/goes.jpg){: .align-right}

I developed an algorithm based on Underdetermined Blind Source Separation (UBSS). UBSS is a problem well researched in the field of acoustics. In UBSS, an array of M microphones is placed inside a room filled with N people having simultaneous conversations, such that N > M.  UBSS seeks to use the noisy microphone signals to isolate each voice. In a similar way, we use multiple magnetometers to isolate magnetic noise signals and separate the ambient magnetic field. 

Humans can do this automatically. If you focus on a specific person in a noisy room, you can often isolate their voice perfectly. The japanese emperor, 聖徳太子 (Shotoku Taishi), was rumored to be able to listen to ten advisors simultaneously during his reign in the 574 C.E.

![image-center](/images/UBSS/UBSS.png){: .align-center}
**Figure 2:** Underdetermined Blind Source Separation.
{: .text-center}


The complication of using UBSS to identify magnetic noise signals is that magnetic fields have unique spatial structures. Magnetic signals have different phase depending on the **magnetic latitude** of the magnetometer with respect to the noise source. The gains and phases of magnetic noise signals and magnetometers is defined by a system called a **mixing matrix**.

### How We Solve It
Removing spacecraft noise from magnetic field data is a two step process. In the first step we identify noise signals using clustering. If you plot magnetometer data against each other, each source signal will draw straight lines. This data can be projected into a clusterable form to reconstruct the **mixing matrix**. Figure 4 shows this process.

![image-center](/images/UBSS/clustering.jpg){: .align-center}
**Figure 3:** The plot on the left shows the magnetometer data in the time-frequency domain forming straight lines that correspond to each source signal. The plot on the right shows these signals projected into a clusterable form.
{: .text-center}

In order to discover the clusters in figure 3, we use an algorithm called [DBSCAN](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.DBSCAN.html). DBSCAN is a clustering algorithm that does not require the number of clusters to be known beforehand, and will also ignore noise points. The clusters that DBSCAN return define the mixing matrix of the system.

![image-right](/images/UBSS/cubesat.jpg){: .align-right}
In the second step, the mixing matrix is used in a process called **Compressive Sensing**. Compressive sensing is a method originally for audio and video compression. Compressive sensing is used to reconstruct the source signals from the mixing matrix.

To test our algorithm, we used a mock cubesat shown in the figure on the right. In this experiment, electrical current is run through four copper coils in order to create magnetic noise signals. The noise signals are recorded by three PNI RM3100 magnetometers. 

   
### Results

We artificially inserted data from the SWARM mission in order to simulate the geomagnetic field. This produces the following signals as shown in the figures below. The algorithm was tested on 100 seconds of this data. It correctly identified each of the noise signals. The reconstructed geomagnetic field signal is shown in figure 7.

{% include gallery caption="Mixed and Separated Signals" %}
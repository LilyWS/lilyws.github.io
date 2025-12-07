+++
date = '2025-12-07T14:43:00-05:00'
draft = true
title = 'Designing a Gilbert Cell Based Automatic Gain Controller'
+++
## Why Make This?

A couple of months back I decided to try making an analog function generator. My requirements were pretty loose, I just wanted to see if I could generate some square, triangle, and sine waves. Additionally, I wanted to be able to adjust the frequency and amplitude of the waves generated. This actually turned out to be pretty easy, only taking three op-amps.

![Three Op Amp Function Generator](/images/FunctionGenerator.png)

The problem with this circuit is that it's extremely limited in its bandwidth. This is because of the capacitors used in the integrators for the sine and square waves. As the frequency of the waveform rises, the capacitor presents less and less impedance so the waveform travels ‘around’ the op-amp (operational-amplifier) via the capacitor. This causes the amplitude to drop as the op-amp is what’s responsible for amplifying the waveform. Thus, our output waveform drops as our frequency rises. Pretty undesirable for a waveform generator.

## What is an AGC? Why a Gilbert cell?

So our problem is that as the frequency rises, the op-amps become increasingly shorted. One way to stop this would be to swap the capacitors so that they present a higher impedance at higher frequencies. Or we could even swap out the amplifier topology entirely to circumvent this issue. 

Another way would be to simply generate the signal, and then amplify the signal back to a desirable level afterwards. This method doesn’t involve modifying our current waveform generation circuit but instead adding another stage entirely. This stage would be responsible for amplifying the generated waveform so that it maintains a constant amplitude, in our case 5 volts peak-to-peak.

So for this secondary stage we have an input signal (the output of the function generator), which will have a varying amplitude. To maintain a constant amplitude we must apply a dynamic gain in order to respond to the variations in input amplitude. This process is known as automatic gain control (AGC). In this post we’ll begin by implementing a voltage controlled amplifier (VCA), which is an amplifier whose gain varies with a control voltage. In order to go from a VCA to AGC, we’ll need to implement a feedback loop to dynamically adjust this control voltage such that a constant output amplitude is maintained, although I won’t be discussing that in this post. 

This is not a typical application for an AGC. To be honest, simply changing the function generator topology would be a better way to achieve a function generator with a desired output range and bandwidth. While the function generator is what got me started designing this AGC, at this point I am much more interested in designing a stand alone AGC whose capabilities would also make it adequate for improving the bandwidth of the three op-amp function generator as well.

The rest of this post will discuss the design of the VCA; for which I’ve decided to use a gilbert cell. It’s capable of multiplying voltages which means it’s capable of dynamic voltage controlled gain, and it’s also frequently used in RF applications which means it should be able to handle our desired frequency range of 1hz-1Mhz. Let’s first get an overview of how a gilbert cell works.

## How does a gilbert cell work?
A gilbert cell is composed of 3 differential pairs. To keep everyone on the same page, I'll quickly cover how a differential pair works. For those who already know, feel free to skip the next three paragraphs.


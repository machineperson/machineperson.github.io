---
layout: post
title:  "RL Wireshark project"
date:   2018-09-19 15:45:00
categories: hardware
---

I built a project for [Dublin Maker][dublinmaker] this year. It's a real-life Wireshark (in reference to the [network traffic analysis software][wireshark]) with LEDs that light up based on network traffic.

This is Sharky:
![sharky]({{ site.url }}/assets/sharky.png) 

I gave a demo of the project at [Pyladies Dublin][pyladies] last night and promised a write-up on how I built it to some people, so here it is.

# The wire sculpture

Building the wire model was the part that easily took the longest. I used garden wire that I bought on Amazon - I didn't know what thickness I would need so I ordered a few different ones, but I ended up using 1.2mm, 0.7mm and 0.5mm wire. I got a good deal on it so I bought about 200m, which is way more than I ended up using. 
I had never built a wire sculpture before, but I decided to just go for it and figure it out as I went. I started with a couple of 1.2mm "anchor" wires defining the rough shape:

![sharky-progress1]({{ site.url }}/assets/sharky-progress1.png) 

and then wove more of the thinner (0.7mm and later 0.5mm) wires between them to create a mesh to fill it in.
![sharky-progress2]({{ site.url }}/assets/sharky-progress2.png) 
This is my progress after working on it for the most part of a day, at a "finish your project" hackathon at [TOG][tog].

I continued to come in for a few hours here and there to work on the sculpture a bit more, so all in all I spent about a week building it. When I finally realised that I still had all the electronics to do and had to declare the sculpture finished, I sprayed it with clear acrylic spray paint in order to insulate it.


# Electronics

This is more or less my first electronics project beyond "hey I can light up an LED", but I figured it out.

The current incarnation of Sharky has 2 colours of LEDs that are each driven by a Raspberry Pi's GPIO port. I'm not powering them through the Pi, though, instead I'm using a 12V laptop charger as a power source (I just cut off the connector and stripped the wires), and using transistors as switches to drive the LEDs.

The white LEDs are connected in 6 parallel strings of 4 LEDs, because there wasn't enough voltage to go around to serialise them all. I only had 2 red LEDs for the eyes, so I could just connect them in series. Below is the circuit diagram I drew up - in the end I used slightly different resistor values than what's on the diagram because I had to work with what was in my resistor kit, but this circuit is pretty forgiving so a few ohms give or take don't really make a difference. 

![sharky-diagram]({{ site.url }}/assets/sharky-diagram.png) 

After I was fairly convinced that my resistor math was correct enough that nothing would explode, I set out to solder everything together. 
![sharky-soldering]({{ site.url }}/assets/sharky-diagram.png) 

I got a lot of practice soldering wires together that day... which I also needed. But eventually it all came together.


# Code

The code to drive the LEDs was actually quickest to do. I used a prerecorded packet capture from my own network, because 1. I did not know if we would have WiFi at Dublin maker, and 2. I couldn't think of a good way to do a live capture with people also consenting to have their traffic inspected. As it was I did get a fair amount of questions about GDPR, but if I do this with a live capture in the future I do have to give people a way to opt in - which isn't really feasible to do at Dublin Maker where most people only look at your project for thirty seconds. 

The code runs on the Raspberry Pi that is connected to the LEDs. It reads a packet capture file, divides it into segments and does statistics on the transport-layer protocols that are used in each segments. It lights up the LEDs for a certain percentage of each protocol occurring - white for TCP, red for UDP. There's a handy Python library to control the GPIO pins on a Pi, which is helpful.

The Github repo is [here][github] - note: I had to do a couple of last-minute fixes after putting everything together, and at that point the Pi was no longer connected to a network, so what's on Github is not *exactly* what runs on the Pi (I need to get around to merging those changes) and will not work out of the box straight away. Sorry - I'm working on fixing that.


# Ideas for Sharky 2.0

Only looking at 2 transport-layer protocols is good for a first version, but I want to do a bit more with it in future years:
- Add more LEDs to the body to visualise different things: more protocols, incoming/outgoing traffic...
- Find some way to do a live capture without violating GDPR :) 
- Visualise traffic throughput levels somehow - brighter/more LEDs for more packets within a certain time period. (Pi GPIO ports are digital, unfortunately, so this may be a nontrivial change)
- Some attendee gave me the idea of doing something with lights moving across the body to illustrate traffic flow. I like this idea and will think about what exactly to do with it.


[wireshark]: https://www.wireshark.org
[dublinmaker]: http://www.dublinmaker.ie/
[pyladies]: https://www.meetup.com/PyLadiesDublin/
[tog]: https://www.tog.ie
[code]: https://github.com/machineperson/shark-in-the-wires

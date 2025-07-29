---
title: "Kin3ct"
author: "KavEn06"
description: "Handheld DIY 3D scanner that repurposes an Xbox Kinect and a Raspberry Pi"
created_at: "2025-07-20"
---

# Kin3ct Creation Journal 

---
# July 20th – Can I even make this? Is this even possible?

Hi hi, this is my devlog/journal for my new idea: a **handheld 3D scanner** using a Kinect and Raspberry Pi!  

The idea is to build something like a portable(ish) 3D scanner that can capture point clouds **live while scanning**, so you can actually see what you’re scanning in real‑time, instead of just dumping a bunch of photos and waiting hours to process them later. That was the whole point from the start — not just “photogrammetry from a camera,” but an actual live‑processing scanner.


## **Early research / what I found:**
At first I thought about just using **photogrammetry**: take a video, import it later, and run it through Meshroom or COLMAP. But that doesn’t really fit the goal: I *want* the data to be processed live, so I can immediately see what I captured and clean it up later. So photogrammetry became more of a **backup option**, but not the main plan.

Then I found **Skanect**, which sounded promising: it can take data from a Kinect and build a mesh in real‑time. My idea was to run **Raspbian** on the Pi and install Skanect, using a Kinect driver like **libfreenect** to get the depth data. But after digging deeper, I realized Skanect (and pretty much every similar program) only runs on **x86** processors — not on **ARM**, which is what Raspberry Pis use. Same story for RecFusion, ReconstructMe, etc.


## **RTAB‑Maps! (MY SAVIOUR)**
After some more digging, I found **RTAB‑Map**, an open‑source SLAM and mapping library that *does* support ARM, and can work with the Kinect through **libfreenect2**. Even better, RTAB‑Map can do **live SLAM**: it builds a 3D map *while* you scan. Then I can import the point cloud later on my PC and clean it up properly for CAD or Blender.

So now the idea is:
- Use Kinect + Raspberry Pi 5 + RTAB‑Map + libfreenect2  
- Save maps to SSD or USB drive  
- Clean them up later on a real PC


## **Choosing the Kinect: v1 vs v2**
There are two main versions:  
- **Xbox 360 Kinect (v1)**  
- **Xbox One Kinect (v2)**
<img width="632" height="176" alt="image" src="https://github.com/user-attachments/assets/3df80085-9550-4e4f-98ca-258800be2629" />

The Kinect v2 is better because:
- Higher resolution depth sensor
- Wider field of view
- Better depth accuracy and less noise

Downside: both require a special 12V power adapter to run over USB. It’s not a huge deal since adapters are available online, but it is an extra cost :/


## **Which RPi?**
I decided that the Raspberry Pi 5 would be my best option as, 
- It has way more processing power than Pi 4 → better for running RTAB‑Map
- USB 3.0 ports → needed to handle Kinect v2’s high bandwidth
- Can easily add an SSD for storage, which makes it practical to do lots of scans without swapping SD cards every time


## **Power challenge:**
The Kinect v2 is **very power hungry**. Making this completely battery‑powered (with step‑up converters) is possible, but battery life would probably be terrible. Having a cable for power isn’t the end of the world, since no one’s realistically going to use this to scan a whole house (and it obviously won’t be commercial grade anyway).

<img width="425" height="210" alt="image" src="https://github.com/user-attachments/assets/40d42d4a-1d1d-49ff-89c2-b233ed972a07" />

(This is how the adapter looks) 


## **Basicallyyy**
After digging through lots of dead ends, I finally landed on:
- Kinect v2 + Raspberry Pi 5
- RTAB‑Map to do live SLAM
- libfreenect2 as the driver
- Save to SSD, clean up on PC

**Total Time Spent Today: ~5h**
(Quite tragic that this took this long)

---

# July 21 – BOM and Starting CAD

Hi hi, devlog time again!

Today I spent a bit of time putting together an actual **BOM** so I could see roughly what this thing will cost and what parts I really need.  
The list isn’t final (obviously) but it helps me get an idea if it’s even reasonable before I get too carried away designing stuff.

### BOM (Unfinished)
| Item                        | Purpose                       | Source | Price |
|----------------------------|------------------------------|-------|------:|
| Raspberry Pi 5             | Main compute unit            | [PiShop](https://www.pishop.ca/product/raspberry-pi-5-4gb/?searchid=0&search_query=pi+5+) | $85 |
| 7 inch LCD Display + Touch | Display & user interface     | [AliExpress](https://www.aliexpress.com/item/1005006420023450.html?invitationCode=MWVGaWdsZDc0cUZBdVBDM3l3bnY2ZGI4ckF6NFl1RjcwaTVFclZqZytLU2VQemFTZUJrNWVWT0s1MU1hdTAyWg&srcSns=sns_Copy&spreadType=socialShare&social_params=21892192294&bizType=ProductDetail&spreadCode=MWVGaWdsZDc0cUZBdVBDM3l3bnY2ZGI4ckF6NFl1RjcwaTVFclZqZytLU2VQemFTZUJrNWVWT0s1MU1hdTAyWg&aff_fcid=bc58928340ae4fef85f7e64e18bd7d7a-1753674064912-08252-_mPIB3Jt&tt=MG&aff_fsk=_mPIB3Jt&aff_platform=default&sk=_mPIB3Jt&aff_trace_key=bc58928340ae4fef85f7e64e18bd7d7a-1753674064912-08252-_mPIB3Jt&shareId=21892192294&businessType=ProductDetail&platform=AE&terminal_id=f0c0659a4ca447babbee09709f77824e&afSmartRedirect=y) | $40 |
| Kinect v2 + Adapter        | Depth camera for scanning    | Self-Sourced | $60 |
| Push Button                | Physical controls (start/stop etc.) | [AliExpress](https://www.aliexpress.com/item/32815969627.html?invitationCode=MWVGaWdsZDc0cUZ3UlZKTHg5WXhOZGI4ckF6NFl1RjcwaTVFclZqZytLU2VQemFTZUJrNWVWT0s1MU1hdTAyWg&srcSns=sns_Copy&spreadType=socialShare&social_params=21887243335&bizType=ProductDetail&spreadCode=MWVGaWdsZDc0cUZ3UlZKTHg5WXhOZGI4ckF6NFl1RjcwaTVFclZqZytLU2VQemFTZUJrNWVWT0s1MU1hdTAyWg&aff_fcid=4c7a59c12cce419489546f7f6a67c5eb-1753674154916-06773-_mMydiq7&tt=MG&aff_fsk=_mMydiq7&aff_platform=default&sk=_mMydiq7&aff_trace_key=4c7a59c12cce419489546f7f6a67c5eb-1753674154916-06773-_mMydiq7&shareId=21887243335&businessType=ProductDetail&platform=AE&terminal_id=f0c0659a4ca447babbee09709f77824e&afSmartRedirect=y) | $2 |
| Custom PCB                 | Connects buttons, SSD, etc.  | JLCPCB | TBD |
| 3D Printed Case            | Holds everything together    | Self-Sourced | $0 |
| NVMe SSD                   | Storage for scan data        | Owned | $0 |
||                           |                                Total CAD | $187 |
||                           |                                Total USD | $138 |

## CAD

After I got the BOM together, I started working in **Onshape** to see how this thing could actually look and come together.

I decided to take some inspiration from the Revopoint Miraco scanner, mainly because I really like that it’s a proper two‑handed scanner. It just seems way easier to hold steady and move around whatever you’re scanning, instead of trying to grip a box in one hand.

<img width="415" height="259" alt="image" src="https://github.com/user-attachments/assets/1be9cccc-cc3d-4293-94ee-3a030ac62a69" />

Here's the CAD model, it’s still *midway through*, but you can already see the vision I’m going for, kinda inspired by the Miraco scanner, with the two‑handed grip and the screen right in front. I feel like I have enough room to shove both the adapter and the Pi into the shell without any problems. 

<img width="625" height="427" alt="image" src="https://github.com/user-attachments/assets/c1326252-8e92-4cf3-9c9c-8aaaa0339f25" />

I’ve just went with this model and kept refining it, and a couple hours later it’s starting to look quite polished now!!!! :D

<img width="625" height="427" alt="image" src="https://github.com/user-attachments/assets/e9eb42b3-1a3c-495a-8596-245614cb2f81" />
<br>
<img width="625" height="427" alt="image" src="https://github.com/user-attachments/assets/520777e8-49cb-4234-aea6-5ef9a38ce009" />

**Total Time Spent Today: ~6h**

---

# July 22nd – more cad

Spent today doing more CAD stuff!  

First pic is just me turning the model into a proper shell with a wall thickness of 2.6mm, and I added standoffs for the Raspberry Pi and the screen. Also made sure all the parts actually fit inside (which they very clearly do so far, yay).  

<img width="725" height="444" alt="image" src="https://github.com/user-attachments/assets/78f6ece6-303c-432d-9150-8f05318952cd" />

Side note: the random yellow block in the model is just the Kinect adapter — I couldn’t find a proper CAD model for it, so that’s what its gonna be lol.


You might be wondering *“kavin where’s the Kinect gonna go?!?!?!”*  

It’s actually gonna go right at the bottom of the scanner, which is why i didn't do any fillets or anything down there so then it mates up nicely with the kinect.  

Here’s the model of the mount I made for it :D

<img width="752" height="454" alt="image" src="https://github.com/user-attachments/assets/c8419c89-237f-431a-8aed-b3eebab081db" />

<img width="752" height="500" alt="image" src="https://github.com/user-attachments/assets/da4793b8-6a27-4216-b7c0-82a62a7de9e1" />

**Total Time Spent Today: ~3h**

---

# July 23rd – even more cad

Today wasn’t much, I just decided to finish up this CAD model.  

I added ribs inside the shell where the grips are so it feels sturdier in the hand, and also added some buttons to the right side of the screen for easy control of shortcuts.  

It’s nothing huge, but it does make it feel more like a proper handheld scanner and less like just an empty box lol.

<img width="790" height="554" alt="image" src="https://github.com/user-attachments/assets/5b7b1a51-2aa4-4841-b44a-9af0e3963238" />

<img width="790" height="554" alt="image" src="https://github.com/user-attachments/assets/1cdf2ce6-2901-4bd8-b2db-a6cb20a15f8e" />

<img width="790" height="554" alt="image" src="https://github.com/user-attachments/assets/ee7bf564-aa47-4399-b7ba-9ff8f29b211a" />

(IT'S ACTUALLY LOOKING LIKE A PROPER SCANNER WOOOOOOO) 

I also imported the sketch that I used to make the holes in the shell into KiCAD, so that I know where to place the push buttons on the PCB. (I just exported the sketch as a DXF and imported it into KiCAD's PCB editor)

<img width="163" height="389" alt="image" src="https://github.com/user-attachments/assets/bb5c470d-37e4-4e22-aff6-7c4523d8b694" />

**Total Time Spent Today: ~2h**

---

# July 28th – pcb and finishing steps

Made a simple 4‑button PCB today that connects straight to the Pi.  

<img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/5d03aaed-351b-4a4d-9901-3aec7211d542" />
<img width="163" height="370" alt="image" src="https://github.com/user-attachments/assets/7813354a-72ba-4c0f-9175-35f27030a302" />

I also imported it into Onshape so I could figure out the final position for the Pi inside the case. As you can see in the screenshot, everything lines up perfectly, but the USB headders on the Pi doesn't allow the Pi to get close enough to the PCB. 

<img width="550" height="550" alt="image" src="https://github.com/user-attachments/assets/7b83dcea-47ef-42bd-a4c1-ca9cb216c5f3" />
<img width="227" height="450" alt="image" src="https://github.com/user-attachments/assets/91b300dd-dd0d-4366-b883-92693230380d" />

I just put a small cutout in the corner of the pcb, which allowed me to move the Pi closer to the PCB by 6.7mm. (Which SHOULDDDD be close enough) 

<img width="600" height="375" alt="image" src="https://github.com/user-attachments/assets/8a476a9b-665b-4f05-a16b-7ccb32d4b95b" />
<img width="225" height="468" alt="image" src="https://github.com/user-attachments/assets/b47b4b65-dc9c-4f3c-a9a2-5fea2843c5ff" />

**Total Time Spent Today: ~1h**

---


# Kin3ct

<img width="905" height="571" alt="image" src="https://github.com/user-attachments/assets/ed81127b-27cb-4aae-b51f-a58f44d43555" />

## What is it?
Kin3ct is a handheld 3D scanner built around a Kinect v2 sensor and a Raspberry Pi 5. It’s designed to let you scan objects and spaces while seeing the point cloud build live on the screen, instead of waiting to process photos later with photogrammetry. The scanner will have a built‑in 7‑inch touchscreen so you can watch the scan as you move, along with a few shortcut buttons on the side to start/stop scanning and switch modes.  
Right now, the plan is to capture the raw data on the Pi and clean it up later on a PC for use in CAD, Blender, or other 3D software.

## Backstory / Why did I build it?
I’ve always thought 3D scanners were super cool, but most of the good ones are either huge, expensive, or both. At first I looked into doing photogrammetry, but it felt too slow — I really wanted something that could show what I was scanning *live*, so you can see if you missed a spot or need to rescan right away. That’s when I thought about using a Kinect v2 with a Raspberry Pi to make a handheld scanner that’s (sort of) portable and way cheaper than commercial scanners. It probably won’t be as accurate or polished, but the idea of being able to walk around an object and watch the point cloud build up on a little screen sounded too fun not to try.

## Hardware
- **Raspberry Pi 5**             – handles running RTAB‑Map and collecting the data
- **Kinect v2 + USB adapter**    – the depth sensor that actually does the scanning
- **7-inch LCD touchscreen**     – to see the point cloud build live while scanning
- **NVMe SSD**                   – extra storage for saving larger scans without swapping SD cards
- **Custom 4-button PCB**        – for shortcut controls (start/stop scan, switch modes, etc.)
- **3D printed case**            – inspired by the Revopoint Miraco so it’s easier to hold with two hands

## 3D Model

<img width="1069" height="668" alt="image" src="https://github.com/user-attachments/assets/bc396b04-ee0c-409d-85bb-0435234b8b5a" />

<img width="1372" height="709" alt="image" src="https://github.com/user-attachments/assets/fa612133-d185-4e7a-986e-ddae5e8d8995" />

## Schematic and PCB 

<p align="center">
  <img width="600" height="400" alt="image" src="https://github.com/user-attachments/assets/5d03aaed-351b-4a4d-9901-3aec7211d542" />
  <img width="163" height="370" alt="image" src="https://github.com/user-attachments/assets/7813354a-72ba-4c0f-9175-35f27030a302" />
</p>

Interms of the wiring for the Kinect, it goes such as: 
Kinect -> Adapter -> Raspberry Pi 5 
Power -> Adapter -> Raspberry Pi 5 and Kinect 

## Firmware 
For controlling the scanner, I made a small 4‑button PCB that connects directly to the GPIO pins on the Raspberry Pi.  
There will be a Python script running on the Pi can just watch for GPIO state changes and trigger the right RTAB‑Map commands or shortcut functions like:
- Start/stop scanning
- Switch scanning modes
- Reset/clear the current point cloud
- Maybe something else later on


For the actual scanning, I’m planning to run RTAB‑Map on the Raspberry Pi 5. RTAB‑Map is an open‑source SLAM (Simultaneous Localization and Mapping) library that can build a 3D point cloud *live while scanning*, which is exactly what I wanted from the start.

To get the Kinect v2 data into RTAB‑Map, I’ll be using libfreenect2 — an open‑source driver that lets Linux talk to the Kinect v2 over USB 3.  
There isn’t an official ARM build, but it *does* work on the Pi if you build it from source (thanks to the Pi 5’s faster CPU and USB 3.0 ports).

I’m mostly following this guide on Instructables:  
👉 [RGB‑D SLAM With Kinect on Raspberry Pi 4 (Buster / ROS)](https://www.instructables.com/RGB-D-SLAM-With-Kinect-on-Raspberry-Pi-4-Buster-RO/)  

The general plan:
- Build and install libfreenect2 on the Pi so the Kinect v2 can stream depth + RGB data  
- Set up RTAB‑Map (through ROS) to receive that data and do live SLAM
- Save the generated point clouds to the SSD for cleanup later on the PC

This way, the Pi handles the live scanning part, and I can do heavier mesh cleanup or conversions in Blender or MeshLab afterward.  

### BOM 
| Item                        | Purpose                       | Source | Price |
|----------------------------|------------------------------|-------|------:|
| Raspberry Pi 5             | Main compute unit            | [PiShop](https://www.pishop.ca/product/raspberry-pi-5-4gb/?searchid=0&search_query=pi+5+) | $85 |
| 7 inch LCD Display + Touch | Display & user interface     | [AliExpress](https://www.aliexpress.com/item/1005006420023450.html?invitationCode=MWVGaWdsZDc0cUZBdVBDM3l3bnY2ZGI4ckF6NFl1RjcwaTVFclZqZytLU2VQemFTZUJrNWVWT0s1MU1hdTAyWg&srcSns=sns_Copy&spreadType=socialShare&social_params=21892192294&bizType=ProductDetail&spreadCode=MWVGaWdsZDc0cUZBdVBDM3l3bnY2ZGI4ckF6NFl1RjcwaTVFclZqZytLU2VQemFTZUJrNWVWT0s1MU1hdTAyWg&aff_fcid=bc58928340ae4fef85f7e64e18bd7d7a-1753674064912-08252-_mPIB3Jt&tt=MG&aff_fsk=_mPIB3Jt&aff_platform=default&sk=_mPIB3Jt&aff_trace_key=bc58928340ae4fef85f7e64e18bd7d7a-1753674064912-08252-_mPIB3Jt&shareId=21892192294&businessType=ProductDetail&platform=AE&terminal_id=f0c0659a4ca447babbee09709f77824e&afSmartRedirect=y) | $40 |
| Kinect v2 + Adapter        | Depth camera for scanning    | Owned | $0 |
| Push Button                | Physical controls (start/stop etc.) | [AliExpress](https://www.aliexpress.com/item/32815969627.html?invitationCode=MWVGaWdsZDc0cUZ3UlZKTHg5WXhOZGI4ckF6NFl1RjcwaTVFclZqZytLU2VQemFTZUJrNWVWT0s1MU1hdTAyWg&srcSns=sns_Copy&spreadType=socialShare&social_params=21887243335&bizType=ProductDetail&spreadCode=MWVGaWdsZDc0cUZ3UlZKTHg5WXhOZGI4ckF6NFl1RjcwaTVFclZqZytLU2VQemFTZUJrNWVWT0s1MU1hdTAyWg&aff_fcid=4c7a59c12cce419489546f7f6a67c5eb-1753674154916-06773-_mMydiq7&tt=MG&aff_fsk=_mMydiq7&aff_platform=default&sk=_mMydiq7&aff_trace_key=4c7a59c12cce419489546f7f6a67c5eb-1753674154916-06773-_mMydiq7&shareId=21887243335&businessType=ProductDetail&platform=AE&terminal_id=f0c0659a4ca447babbee09709f77824e&afSmartRedirect=y) | $2 |
| Custom PCB                 | Connects buttons, SSD, etc.  | JLCPCB | $5 |
| 3D Printed Case            | Holds everything together    | Self-Sourced | $0 |
| NVMe SSD                   | Storage for scan data        | Owned | $0 |
|                            |                              |Total CAD | $132 |
|                            |                              |Total USD | $96 |

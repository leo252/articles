# Arduino Meets Linux: Arduino Introduces Its 3rd Generation Platform

[Original URL](http://electronics360.globalspec.com/article/5995/arduino-meets-linux-arduino-introduces-its-3rd-generation-platform)

> The Arduino platform's simplicity, open architecture and ease of use helped make it the most popular embedded development tool within the Maker community. In this article, we will look at the new...

The Arduino platform's simplicity, open architecture and ease of use helped make it the most popular embedded development tool within the Maker community. In this article, we will look at the new Linino framework, which makes it possible to seamlessly integrate Linux into the Arduino platform as well as some of the boards, protocols and development tools that can support advanced functionality, such as real-time control of multiple functions, IoT applications and integration of cloud-based services.

**In the Beginning**

The first Arduino boards were powered by Atmel's AVR family of 8-bit microcontrollers that were great for simple tasks such as controlling lights, power supplies or motors. However, as Arduino's popularity grew, new boards sporting more powerful 16-and 32-bit MCUs were introduced to support increasingly-demanding applications. Likewise, many new plug-in "[shields](http://www.arduino.org/products/shields)" added powerful wired and wireless networking capabilities, as well as I/O options ranging from motion sensing to motor control.

**Arduino + Linux = Open-Source Awesomeness**

**<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-YunPhoto.jpg" width="263">
</span>

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-YuniBridgeDiag.jpg" width="321">
</span>**

**Fig.1: The Arduino Yun is a 3

<sup>rd</sup>

-generation dual-processor Arduino board (left) which uses the Linino Bridge Protocol to share resources and act as a command/data pipeline (right). Images credit: Arduino.org.**

Now, a third generation of Arduino boards is emerging that contains two processors. 3

<sup>rd</sup>

-gen boards such as the Arduino Yun. (Fig.1) have an embedded microcontroller (MCU) and a more powerful microprocessor Unit (MPU), similar to those found in PCs and mobile devices. The MCU (typically an Atmel [ATmega32x](http://www.atmel.com/dyn/resources/prod_documents/7766S.pdf) device) supports the real-world I/O functionality traditionally handled by the Arduino platform. The MPU is typically based on the MIPS architecture (such as the Atheros AR9331) and powerful enough to support application languages such as C/C++, Python and Java. The microprocessor can also support multiple communication protocols (Wi-Fi, TCP/IP, HTTP etc) and web-based services (Chat, e-mail, Twitter, AllJoyn etc).

This division of labor frees the MCU to handle all the traditional close-to-hardware functions that the original Arduino was designed to, leaving the CPU free to support higher-level applications and compute-intensive communications tasks. Splitting functionality also allows the microprocessor to run LininoOS, a Linux distribution based on OpenWRT (Fig.2) while the MCU to still runs conventional Arduino code. The Arduino MCU communicates with its higher-powered companion using a protocol stack called LininoIO.

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-Linino-IO-Protocol.jpg" width="340">
</span>

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-LininoOS.jpg" width="313">
</span>

**Fig.2: Linino's IO/OS subsystem enables the MCU to transparently share its I/O peripherals with the platform's MPU. Image credit: Arduino.org.**

The MCUIO/LININOIO subsystem allows a microprocessor, connected to the Arduino's MCU, to see the MCU's peripherals as standard Linux peripherals (i.e. GPIOs, I

<sup>2</sup>

C adapters, PWM channels, etc). The basic communication protocol is completely generic, it just defines ways to read and write a (virtualized) memory space on the MCU. Standard vendor independent memory maps are then defined for each kind of MCU peripheral, so that hardware-specific implementation details can be ignored by the MPU. The protocol is transport independent, requiring only a wired or wireless bidirectional channel (for instance rs232 or even a network connection). Linino IO also enables a single microprocessor to communicate with up to 128 MCUs. As a result, an MPU running Linino OS can run applications written in higher-level languages (Python, Bash, Lua etc), which controls the entire board and all the devices attached to it.

Communication between the MPU and MCU is normally handled by the Bridge library (Fig1b), a collection of utility classes that support different kinds of functionality. The Bridge library facilitates communication between the two processors, giving Arduino sketches the ability to run shell scripts, communicate with network interfaces and receive information from the AR9331 processor.

The third pillar of the new software architecture is Arduino Ciao_,_ a library of routines that enables Arduino sketches to communicate with the "outside World" (Fig.3). By simplifying interactions between the MCU and Linino OS, Ciao makes it easy to establish a variety of connections with most common protocols, third-party services and social networks.

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-Ciao.jpg" width="318">
</span>

**Fig.3: The Arduino Ciao library enables the MCU to access web-based services and resources. Image credit: Arduino.org.**

The Ciao library's modular routines support several logical connections capable of interacting with system resources (file system, console and memory). Others can be used to communicate with the most common and useful protocols (XMPP, HTTP, WebSocket, COAP, etc) and applications (Jabber, WeChat, Twitter, Facebook, etc.)

Together, the Linino OS, Linino IO, Bridge and Ciao form a complete Linux system for Internet of Things (IoT) applications.

**A New Development Environment**

In order to support concurrent operation of Arduino and Linino code, the folks at Arduino had to create [Arduino Studio](http://labs.arduino.org/Arduino+Studio), a new development environment that breaks from the monolithic architecture and a centralized development model of the original [Arduino IDE](http://labs.arduino.org/Arduino+IDE). Arduino Studio is intended to serve as a single editor for all environments, enabling developers to use it as a stand-alone, web/cloud-based or onboard embedded editor. By leveraging the versatility of Adobe [Brackets](http://brackets.io/) Studio supports all the functionality of the original Arduino IDE, but also offers a full-featured Linux development environment. Arduino Studio's features include [Code Completion](http://labs.arduino.org/Code+Completion+and+in-line+Quick+Language+Reference#Code_Completion), an in-line [Quick Language Reference](http://labs.arduino.org/Code+Completion+and+in-line+Quick+Language+Reference#In-line_Quick_Guide) and a [Debugger](http://labs.arduino.org/Arduino+Studio+M0Pro+Debugger) that currently supports the Arduino [M0 Pro](http://www.arduino.org/products/arduino-m0-pro) platform.

**Hardware Resources**

There are already several boards that support the dual-processor Linino architecture. Many of these boards contain both an MPU and an MCU device, but as we will see shortly, the Linino platform also makes it possible to create extremely low-cost systems based on a single MCU.

The [**Arduino Yún**](http://world.arduino.org/en/arduino-yun.html) is based on the [ATmega32u4](http://www.atmel.com/dyn/resources/prod_documents/7766S.pdf) and the Atheros AR9331 (Fig.1a). It includes built-in Ethernet, a USB-A port, micro-SD card slot, 20 digital input/output pins (of which seven can be used as PWM outputs and 12 as analog inputs), a micro USB connection, an ICSP header and three reset buttons. Yún also has a Wi-Fi module built on board, allowing it to connect to a wireless router, or act as an access point. The Yún's ATmega32u4 has built-in USB communication, eliminating the need for a secondary processor. This allows it to appear to a connected computer as a mouse and keyboard, in addition to a virtual (CDC) serial / COM port.

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-YunMiniPhoto.jpg" width="262">
</span>

**Fig.4: The Arduino Yún Mini. Image credit: Arduino.org.**

[**Arduino Yún MINI**](http://www.arduino.org/products/boards/4-arduino-boards/arduino-yun-mini) is a compact breadboard PCB powered by an ATmega 32u4 MCU and a Qualcomm Atheros AR9331 CPU. It has built-in WiFi and nearly all of the full-size Yún's I/O compliment, including a USB-based COM port. This open-source board design is also available as the [Linino One](http://www.linino.org/product/linino-one/) from [Linino.org](http://www.linino.org/linino-modules/).

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-Tian.jpg" width="273">
</span>

**Fig.5: Arduino Tian, scheduled for initial release in November 2015\. Image credit: Arduino.org.**

[**Arduino TIAN**](http://www.arduino.org/products/boards/4-arduino-boards/arduino-tian) is similar to the Yun, but designed for projects that require more processing power and I/O capability. TIAN is equipped with Atmel's 32-bit ARM-based SAMD21 MCU and the Qualcomm Atheros AR9342 MPU. Its integrated Wi-Fi module supports IEEE802.11n 2x2 2.4/5 GHz dual-band operation. It is also equipped with 4GB of eMMC memory that can store application code or additional Linux libraries.

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-ChiWaWaModule.jpg" width="253">
</span>

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-ChiWaWaEVB.jpg" width="244">
</span>

**Fig.6: Linino Chiwawa LGA module and evaluation board (left) and its companion Evaluation Board(right). Images credits: Arduino.org and Linino.org**

The [**Linino Chiwawa**](http://www.linino.org/portfolio/chiwawa/) is an LGA module based on Arduino's open-source **101 Industrial** design. It is intended to be a low-cost drop-in LininoOS device for commercial applications and is expected to cost $10 or less in production quantities. It contains an Atheros MIPS AR9331 MPU, but does not include an MCU. The board has built-in Ethernet and Wi-Fi support, a USB-A port, micro-SD card slot, a micro-USB connection, PoE module connection and reset button. Chiwawa can connect to one or more Arduino MCU devices via a daughter board (aka "collar"), such as the **Chiwawa EVB** that serves as the module's evaluation/development board.

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-Industrial101EVBPhoto.jpg" width="267">
</span>

<span class="img_inline">
  <img src="http://electronics360.globalspec.com/images/assets/995/5995/IHS-151021-Arduino-Industrial101EVB.jpg" width="367">
</span>

**Fig.7: The Arduino Industrial 101 evaluation board, scheduled for a November 2015 release, provides an Atmel MCU, a USB-based console interface and bridging of other real-world signals for the Arduino Industrial LGA module. Images credits: Arduino.org and Linino.org**

The [**Arduino Industrial 101**](http://labs.arduino.org/Arduino+Industrial+101) is another evaluation/development board for the 101 Industrial LGA module described above. The board's integrated ATmega 32u4 MCU supports the LininoIO and Bridge libraries exports all the signals required to support communication, reset, 1 PWM output and two analog inputs. It also brings its USB and an Ethernet signal out to pin headers for easy diagnostic and debug.

To contact the author of this article, email [engineering360editors@ihs.com](mailto:engineering360editors@ihs.com)

**References:**

"_[Linino Technology - Virtualizing MCU peripherals](http://www.linino.org/wordpress/wp-content/uploads/2015/02/Fosdem_LininoIO.pdf)_"

[Linino Wiki](http://wiki.linino.org/doku.php)

[Linino Chiwawa](http://www.doghunter.org/wp-content/uploads/2014/03/data-sheet_Chiwaw-final_A4-size.pdf)
# GPS hacking (PART 1)

[Original URL](http://translate.wooyun.io/2016/02/04/41.html)

> Author: Kevin2600 From: <http://drops.wooyun.org/tips/11155> 0x00 Preface GPS hacking has alway been a hot topic on security conferences over the past few years. But the contents are over academic and...

Author: [Kevin2600](http://drops.wooyun.org/author/Kevin2600)<br>
From: <http://drops.wooyun.org/tips/11155>

## 0x00 Preface

GPS hacking has alway been a hot topic on security conferences over the past few years. But the contents are over academic and the cost for necessary equipment is too high, which stops many fans from getting started. The appearance of some open source projects, such as GPS-SDR-SIM, and the Keynote speech given by @Wang Kang on Blackhat Europe 2015 have pierced the veil of GPS. This means any user who is interested in this topic will truly be able to have a try on GPS hacking.

I believe many of you have heard of a powerful tool called Software Defined Radio (SDR) in GPS research. But a new USRP is very expensive, until we found an amazing TV Dongle named RTL-SDR. Some time ago, everyone enjoys using it to watch "adult channels". In view of its hardware constraints, it can only be used to received data. However, HackRF and BladeRF support both receiving and sending data, besides they are cheaper than USRP. Of course, HackRF and BladeRF support different frequency and sample rate. Therefore, this two become the first choice of radio fans. The most important part about BladeRF is that it is full duplexed. Here is a comparison between several SDR equipment. You may purchase as you need.

![](http://static.wooyun.org//drops/20151208/2015120812432876209126.png)

Brief introduction of GPS system

GPS system is very complex and involve many fields from satellite communications. Here is just a brief introduction. The GPS we are talking about is built by U.S. Department of Defense. Currently there are 31 satellites working simultaneously in space. Normally we need at least 4 satellites to complete triangulation positioning. All satellites broadcast both L1 signals for civil use and L2 signals for military use at the same time. What we often use is a 1575.42MHz ultra high frequency which is an unencrypted L1 signals for civil use.

GPS signals include 3 kinds of information.

- Pseudorandom code: a simple ID code used to identify each satellite.
- Ephemeris data: include information about the time and status of satellites, which plays an important role in calculating the position of each satellite.
- Almanac data: include information about satellite orbits and the specific position that a satellite will be at in a given time.

![](http://static.wooyun.org//drops/20151208/201512081243354092821.jpg)

## 0x01 Processes to forge BadeRF GPS signals

## 1.1 Install a BladeRF tool on Ubuntu 14.04.3

![](http://static.wooyun.org//drops/20151208/201512081243385861939.png)

install the header file

![](http://static.wooyun.org//drops/20151208/201512081243415891349.png)

install BladeRF firmware & FPGA image

![](http://static.wooyun.org//drops/20151208/201512081243427563859.png)

After installation, you may notice a `hostedX40.rbf` and a `bladerf_fw.img` in /`usr/share/nuand/BladeRF/`. Here you can insert the BladeRF to a USB interface. Normally system will automatically load the FPGA image. Or it can be manually loaded by inputing `bladerf_cli -l /path/hostedX40.rbf` in command line. When the image is loaded successfully, 3 LED lights on BladeRF board will illuminate, meanwhile, we can add a -p parameter to further verify if system installation is successful.

![](http://static.wooyun.org//drops/20151208/201512081243449340369.png)

## 1.2 Install GPS-SDR-SIM

```
git clone https://github.com/osqzss/gps-sdr-sim.git 
cd gps-sdr-sim 
gcc gpssim.c -lm -O3 -o gps-sdr-sim
```

Set up latitude and longitude, then generate a data sample. Note that the I/O baseband signal here is 16.

![](http://static.wooyun.org//drops/20151208/201512081243454976879.png)

Afterwards, gps-sdr-sim will generate data files with latitude and longitude data in a automatic way. Then we will be able to send the forged GPS data through bladerf_cli.

![](http://static.wooyun.org//drops/20151208/201512081243465709689.png)

## 1.3 Running time issue of GPS-SDR-SIM

In practical test, @Wangwang find that GPS simulator can only work continuously for five minutes by default. By viewing its source code, we realized this problem is caused by default settings of the program. The program is designed to use less hard drive space and will only generate 300-second data by default. We could modify its parameter to extend working time. But data of 15 minutes will be up to 5GB.

![](http://static.wooyun.org//drops/20151208/201512081243485895197.png)

## 0x02 Forge GPS signals in practice

@Wangwang has shared several practical test cases here. Whoever is interested may have a try.

## 2.1 Search for girls through Wechat People Nearby

I heard that a lot of programmers have little time to hang out with their queen in heart due to working pressure and introvert personality. And Wechat People Nearby perfectly solves this problem for people like that. All you need is to turn on the GPS of your phone, then you'll have a change to say hello to the girls near by. But the drawback is the maximum range is limited within dozen miles away. For those kings of emotion, the stage is too small. Here @Wangwang presents the first case on forging GPS signals-search girls through Wechat People Nearby.

Rumor says a campaign held several days ago in Sanya, Hainan gathered a town of beauties. @Wangwang can't help but wonder how they look like. Let's try Wechat people nearby! Before sending the forged GPS coordinate, @Wangwang only got the girls in the same city.

![](http://static.wooyun.org//drops/20151208/2015120812435533129107.png)

![](http://static.wooyun.org//drops/20151208/2015120812440524586111.jpg)

Then @Wangwang started to send the forge GPS coordinate. 5 minutes later, the girls in Sanya turned up.

LOL...@Wangwang signed a tech geek can change his life.

![](http://static.wooyun.org//drops/20151208/2015120812441331668121.jpg)

## 2.2 Forge Nike+ step counts

Many friends who are found of mobile security must have read a post called "using AnDroid Hook to cheat Wechat sports" (<http://drops.wooyun.org/tips/8416>) written by @Zheng Mi. In this post, he mentioned that he used Android Hook to cheat on step counts so that to beat his friends' record. But this method needs you to root your phone and install some relevant cheating plugins. For other step counting software, these plugins require to be modified accordingly. Here the test target is Nike+ Running. Let's see a video first. This video is expedited to save time.

![](http://static.wooyun.org//drops/20151208/2015120812442291438131.jpg)

<http://player.youku.com/player.php/sid/XMTQwMzAxMTk4OA==/v.swf>

By browsing the home page of GPS-SDR-SIM, we learn that the forged GPS latitude and longitude data can be static or dynamic. To succeed in simulating motion trail, we have to forge dynamic GPS latitude and longitude data, which can be completed by the following parameters.

```
gps-sdr-sim -e brdc3540.14n -u circle.csv -b 16
```

As you can see, step counting App-Nike+ is fooled through directly forging GPS signals. You'll be the top scorer even you are in bed. But, of course, @Wangwang wish you could really join in running and enjoy the joy of sports.

## 2.3 Range test on forge signals

From the aforementioned experiments, we know that GPS receiver can accept software simulated signals within a short distance. Then what about the performance of the GPS receiver in a larger scale? How far does effective distance can reach? Of course, it connects with output power, antenna gain and signal interference from nearby signals. So @Wangwang only presents a simple indoor test here. Actual condition prevails. Please watch this video first.

<http://player.youku.com/player.php/sid/XMTQwMzAwNzMxNg==/v.swf>

As can be seen from the video, the latitude and longitude of the GPS receiver is successfully changed in a 25-meter long linear corridor without any obstacles. Normally, real GPS signals coming from 20,000 meters high are already weak, and almost no signal is detected indoors. Therefore, indoor GPS signals forging attack can be pretty effective.

![](http://static.wooyun.org//drops/20151208/2015120812442518374141.jpg)

## 0x03 Summaries

Based on the above cases, I believe you have more or less learned something about forging GPS signals. But as far as GPS itself, this is a very funny and esoteric area. More GPS related products emerge in market and each will respond different towards GPS deceiving attacks. Everybody can use your imagination and try different tricks.

Finally, I'd like to extend my thanks to @osqzss, @Wang Kang and countless GNURadio enthusiasts for their unselfish sharing. It's because of them that we'll have chance to experience the charm of software defined radio. I recommend you the home page of GPS-SDR-SIM Project and the presentation given by @Wang Kang on Blackhat. Those who have HackRF equipment can read the post "Hijack GPS positioning & hijack WIFI positioning" by @lxj616.

## 0x04 References
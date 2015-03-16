---
layout: post
title: 'Experiences with Buying Cheap Arduinos and shields on eBay'
redirect_from: /blog/post/experiences-with-buying-cheap-arduinos-and-shields-on-ebay
tags:
    - Guide

---
Recently I've started getting interested in electronics and in particular [Arduino][1]. Ardunios are a microcontroller boards that you can programme to perform actions. The board I am interested in is the entry level [Ardunio Uno][2]. This board has 14 digital inputs/outputs and 6 analogue inputs. The Arduino can be expanded with Shields, these are addon boards that stack on top of the Arduino to offer additional functionality (like ethernet, storage, motors etc.)

Buying genuine Arduinos in Ireland can be costly because they have to be ordered from suppliers in the [US][3] or [UK][4]/[Europe][5]. Often the cost of shipping and customs exceeds the cost of the Arduino itself!

## I'm Broke. There has to be a cheaper way?

Because Arduino is a open source project anyone can make knock off Ardunios (or dunios as their called). A quick search on eBay reveals [lots][6] of different options. The main disadvantages with buying on eBay are:

1. **Quality**. You have no idea of the quality of the board manufacture or components used will be.
2. **Slow Deliver**. Most of these parts are shipped from China. Expect to wait three weeks for delivery to Ireland.

If you can put up with these disadvantages you can look to pickup an Arduino for around 16.

## EKits Zone UNO

![EKits Zone Uno (Top view))](/media/buying-arduino/dscf1775.jpg)
![EKits Zone Uno (Bottom view)](/media/buying-arduino/dscf1778.jpg)

Cost: 16

Link: [eBay][7] and [EKitsZone.com][8] (this might be a different model)

Pros:

* Good build Quality (apart from the USB cable).
* Uses the more common Mini-USB port rather that the USB-B port that is on the official Arduino.
* Reset button is on the side rather than the top. This makes it easier to access when you have another shield stacked on top or you just a have lots of wires plugged in.
* Funky red PCB!
* One of the only rev. 3 Arduino borads I could find.

Cons:

* Supplied with a dodgy USB cable.
* The power indicator LED is *really* bright. I had to cover mine with tape so it wouldn't dazzle me (you can see this in the image above, at the top right)!

Bottom Line:

For this price you can't go wrong!

## ENC28J60 Ethernet Shield

![ENC28J60 Ethernet Shield (Top view)](/media/buying-arduino/dscf1780.jpg)
![ENC28J60 (bottom)](/media/buying-arduino/dscf1782.jpg)

Cost: 13

Link: [eBay][9] and [EKitsZone.com][10]

Pros:

* Build quality looks good.
* Cheapest ethernet shield available.
* Few components (less likely to malfunction).

Cons:

* This shield is based on the [ENC28J60][11] ethernet controller. This controller is different to the controller used by the official Arduino ethernet shield and as such it is NOT compatible with the the built in Arduino library and examples. There are a number of third party Libraries, but they are more complicated to use and offer less functionality.

Bottom Line:

Appealing price but avoid if you are a beginner. Complicated Library and lack of examples means I gave up on this ethernet shield and bought:

## Ethernet Shield 05 W5100

![Ethernet Shield 05 W5100 (top View)](/media/buying-arduino/dscf1774.jpg)
![W5100 (bottom)](/media/buying-arduino/dscf1779.jpg)

Cost: 15

Link: [eBay][12]

Pros:

* Compatible with the Arduino ethernet and SD libraries.
* Lots of flashing status lights (is this a pro?!).

Cons:

* Average build quality. Not poor but not as good as the other shield above.
* Advertised as having POE (Power Over Ethernet) but in reality it has the *ability* to have POE, you need to add another module on to get this.

Bottom Line:

Quality not as good as the other boards, but works seamlessly with the existing Arduino libraries.

## Conclusion

Buying knock off Arduinos on eBay is a bit of a hit-and-miss exercise. Quality and accuracy of specs can vary but price is uniformly low. If you need additional Arduinos or shields for projects that you do not want to tie up your primary development Arduino with then these might be the perfect compromise, however if reliability is your main concern then stick with the originals.

 [1]: http://arduino.cc/
 [2]: http://arduino.cc/en/Main/ArduinoBoardUno
 [3]: http://www.sparkfun.com/products/11021
 [4]: http://uk.farnell.com/arduino
 [5]: http://store.arduino.cc/eu/index.php?main_page=product_info&cPath=11_12&products_id=195
 [6]: http://www.ebay.ie/sch/i.html?_from=R40&_trksid=m570&_nkw=arduino+uno
 [7]: http://cgi.ebay.ie/ws/eBayISAPI.dll?ViewItem&item=290646109175&ssPageName=ADME:L:OU:IE:1123#ht_555wt_1398
 [8]: http://www.ekitszone.com/p/10/arduino-uno
 [9]: http://cgi.ebay.ie/ws/eBayISAPI.dll?ViewItem&item=260852073384&ssPageName=ADME:L:OU:IE:1123#ht_500wt_1413
 [10]: http://www.ekitszone.com/p/11/enc28j60-ethernet-shield-for-arduino-uno-duemilanove
 [11]: http://www.microchip.com/wwwproducts/devices.aspx?ddocname=en022889
 [12]: http://www.ebay.com/itm/ws/eBayISAPI.dll?ViewItem&item=130646984479&ssPageName=ADME:L:OC:IE:3160

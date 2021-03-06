pi-gpio
=======

pi-gpio is a simple node.js based library to help access the GPIO of the Raspberry Pi (Debian Wheezy). It's modelled loosely around the built-in ``fs`` module.

```javascript
var gpio = require("pi-gpio");

gpio.open(16, "output", function(err) {		// Open pin 16 for output
	gpio.write(16, 1, function() {			// Set pin 16 high (1)
		gpio.close(16);						// Close pin 16
	});
});
```

## About the pin configuration

This couldn't have been more confusing. Raspberry Pi's physical pins are not laid out in any particular logical order. Most of them are given the names of the pins of the Broadcom chip it uses (BCM2835). There isn't even a logical relationship between the physical layout of the Raspberry Pi pin header and the Broadcom chip's pinout. The OS recognizes the names of the Broadcom chip and has nothing to do with the physical pin layout on the Pi. To add to the fun, the specs for the Broadcom chip are nearly impossible to get!

This library simplifies all of this (hopefully), by abstracting away the Broadcom chip details. You only need to refer to the pins as they are on the physical pin layout on the Raspberry PI. For your reference, the pin layout follows. The cells with the grey background represent the pins, with P1 at the top left.

<table>
	<tr>
		<td>
			P1 - 3.3v
		</td>
		<td style="background: #ddd">
			1
		</td>
		<td style="background: #ddd">
			2
		</td>
		<td>
			5v
		</td>
	</tr>
	<tr>
		<td>
			I2C SDA
		</td>
		<td style="background: #ddd">
			3
		</td>
		<td  style="background: #ddd">
			4
		</td>
		<td>
			--
		</td>
	</tr>
	<tr>
		<td>
			I2C SCL
		</td>
		<td style="background: #ddd">
			5
		</td>
		<td style="background: #ddd">
			6
		</td>
		<td>
			Ground
		</td>
	</tr>
	<tr>
		<td style="background: #90cf4d">
			GPIO
		</td>
		<td style="background: #ddd">
			7
		</td>
		<td style="background: #ddd">
			8
		</td>
		<td>
			TX
		</td>
	</tr>
	<tr>
		<td>
			--
		</td>
		<td style="background: #ddd">
			9
		</td>
		<td style="background: #ddd">
			10
		</td>
		<td>
			RX
		</td>
	</tr>
	<tr>
		<td style="background: #90cf4d">
			GPIO
		</td>
		<td style="background: #ddd">
			11
		</td>
		<td style="background: #ddd">
			12
		</td>
		<td style="background: #90cf4d">
			GPIO
		</td>
	</tr>
	<tr>
		<td style="background: #90cf4d">
			GPIO
		</td>
		<td style="background: #ddd">
			13
		</td>
		<td style="background: #ddd">
			14
		</td>
		<td>
			--
		</td>
	</tr>
	<tr>
		<td style="background: #90cf4d">
			GPIO
		</td>
		<td style="background: #ddd">
			15
		</td>
		<td style="background: #ddd">
			16
		</td>
		<td style="background: #90cf4d">
			GPIO
		</td>
	</tr>
	<tr>
		<td>
			--
		</td>
		<td style="background: #ddd">
			17
		</td>
		<td style="background: #ddd">
			18
		</td>
		<td style="background: #90cf4d">
			GPIO
		</td>
	</tr>
	<tr>
		<td>
			SPI MOSI
		</td>
		<td style="background: #ddd">
			19
		</td>
		<td style="background: #ddd">
			20
		</td>
		<td>
			--
		</td>
	</tr>
	<tr>
		<td>
			SPI MISO
		</td>
		<td style="background: #ddd">
			21
		</td>
		<td style="background: #ddd">
			22
		</td>
		<td style="background: #90cf4d">
			GPIO
		</td>
	</tr>
	<tr>
		<td>
			SPI SCLK
		</td>
		<td style="background: #ddd">
			23
		</td>
		<td style="background: #ddd">
			24
		</td>
		<td>
			SPI CE0
		</td>
	</tr>
	<tr>
		<td>
			--
		</td>
		<td style="background: #ddd">
			25
		</td>
		<td style="background: #ddd">
			26
		</td>
		<td>
			SPI CE1
		</td>
	</tr>
</table>

That gives you several GPIO pins to play with: pins 7, 11, 12, 13, 15, 16, 18 and 22. You should provide these physical pin numbers to this library, and not bother with what they are called internally. Easy-peasy.

## Installation

If you haven't already, get node and npm on the Pi. The simplest way is:

	sudo apt-get install nodejs npm

The Raspberry Pi's GPIO pins require you to be root to access them. That's totally unsafe for several reasons. To get around this problem, you should use the excellent [gpio-admin](https://github.com/quick2wire/quick2wire-gpio-admin).

Do the following on your raspberry pi:

	git clone git://github.com/quick2wire/quick2wire-gpio-admin.git
	cd quick2wire-gpio-admin
	make
	sudo make install
	sudo adduser $USER gpio

After this, you will need to logout and log back in. [Details](http://quick2wire.com/2012/05/safe-controlled-access-to-gpio-on-the-raspberry-pi/), if you are interested.

Next, ``cd`` to your project directory and use npm to install pi-gpio in your project.

	npm install pi-gpio

That's it!

## Usage

### .open(pinNumber, [direction = "output"], [callback])

Aliased to ``.export``

Makes ``pinNumber`` available for use. 

* ``pinNumber``: The pin number to make available. Remember, ``pinNumber`` is the physical pin number on the Pi. 
* ``direction``: (Optional) Direction can either be ``input`` or ``output``, depending on what you want to do with the pin. You could alternatively use ``in`` or ``out``. Default: ``output``.
* ``callback``: (Optional) Will be called when the pin is available for use. May receive an error as the first argument if something went wrong.

### .close(pinNumber, [callback])

Aliased to ``.unexport``

Closes ``pinNumber``.

* ``pinNumber``: The pin number to close. Again, ``pinNumber`` is the physical pin number on the Pi.
* ``callback``: (Optional) Will be called when the pin is closed. Again, may receive an error as the first argument.

### .setDirection(pinNumber, direction, [callback])

Changes the direction from ``input`` to ``output`` or vice-versa.

* ``pinNumber``: As usual.
* ``direction``: Either ``input`` or ``in`` or ``output`` or ``out``.
* ``callback``: Will be called when direction change is complete. May receive an error as usual.

### .getDirection(pinNumber, [callback])

Gets the direction of the pin. Acts like a getter for the method above.

* ``pinNumber``: As usual
* ``callback``: Will be called when the direction is received. The first argument could be an error. The second argument will either be ``in`` or ``out``. 

### .read(pinNumber, [callback])

Reads the current value of the pin. Most useful if the pin is in the ``output`` direction.

* ``pinNumber``: As usual.
* ``callback``: Will receive a possible error object as the first argument, and the value of the pin as the second argument. The value will be either ``0`` or ``1`` (numeric).

Example:
```javascript
gpio.read(16, function(err, value) {
	if(err) throw err;
	console.log(value);	// The current state of the pin
});
```

### .write(pinNumber, value, [callback])

Writes ``value`` to ``pinNumber``. Will obviously fail if the pin is not in the ``output`` direction.

* ``pinNumber``: As usual.
* ``value``: Should be either a numeric ``0`` or ``1``. Any value that isn't ``0`` or ``1`` will be coerced to be boolean, and then converted to 0 (false) or 1 (true). Just stick to sending a numeric 0 or 1, will you? ;)
* ``callback``: Will be called when the value is set. Again, might receive an error.

## Misc

* To run tests: ``npm install && npm test`` where you've got the checkout.
* This module was created, ``git push``'ed and ``npm publish``'ed all from the Raspberry Pi! The Pi rocks!

## Coming soon

* Support for I2C and SPI (though it should already be possible to bit-bang the SPI protocol).
* Any other suggestions?

## License

(The MIT License)

Copyright (c) 2012 Rakesh Pai <rakeshpai@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

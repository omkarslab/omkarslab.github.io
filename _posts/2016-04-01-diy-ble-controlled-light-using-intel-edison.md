---
id: 582
title: 'DIY &#8211; BLE Controlled Light using Intel Edison'
date: 2016-04-01T20:51:11+00:00
author: Omkar
layout: post
guid: http://omkarslab.com/?p=582
permalink: /2016/04/01/diy-ble-controlled-light-using-intel-edison/
sfw_pwd:
  - wd4d31hifliR
image: /wp-content/uploads/2016/04/FJS1IDTILJWGGQD.MEDIUM.jpg
categories:
  - Internet Of Things
tags:
  - ble
  - bluetooth
  - diy
  - edison
  - Intel
  - lights
---
A while ago I built a <a href="https://www.youtube.com/watch?v=PrQ96u2z29A" target="_blank" rel="nofollow">DIY for the Raspberry Pi that could control lights over Wifi</a>. While Intel’s Edison can pull off the same feat, it does one more things. It talks BLE. This is hands-on tutorial on turning your Intel Edison board into a BLE peripheral that you could control from your smartphone. As always, Node.js will be my weapon of choice. You can find the github link and a demo video at the end of this blog.

**The Setup**

Before getting started, make sure you have your Intel Edison running the Yocto build. If you haven’t setup your Edison follow this guide. Once installed the Yocto build, SSH to your board. You will have preinstalled Node.js.

For the development setup, I prefer to use Putty for SSH/Serial and WinSCP to move code from my machine to Edison.

**Enable Bluetooth**

This is a on boot ritual that you might have to repeat to enable BLE on Edison. You can automate with a shell script. Use the following 4 commands to enable BLE.

<pre>bluetooth_rfkill_event &
rfkill unblock bluetooth
hciconfig dev
hciconfig hci0 up</pre>

**Hardware setup**

We will be using the Arduino breakout board with Intel Edison development board to interface with a LED (my setup has a relay). This will be our BLE peripheral.
  
![BLE light hardware setup](http://cdn.instructables.com/FZP/67ZT/IM2DNILO/FZP67ZTIM2DNILO.MEDIUM.jpg)

<div class="step-container">
  <h2 id="step1" class="step-title">
    Step 1: Node.js package config
  </h2>
  
  <div class="step-body">
    <p>
      Create a directory for your Node.js app and configure the package.json to install necessary modules. Your package.json should have the following dependencies &#8211; async, noble, bleno, util.
    </p>
    
    <pre>{  "name": "ble-light",
  "description": "",
  "version": "0.0.1",
  "main": "app.js",
  "engines": {
    "node": "&gt;=0.10.0"
  },
  "dependencies": {
      "async": "latest",
      "noble": "latest",
      "bleno": "latest",
      "util": "latest",
      "mraa": "latest"
  }
}</pre>
  </div>
  
  <div id="double-inline-ads" class="clearfix">
  </div>
</div>

<div class="step-container">
  <div class="step-container">
    <h2 id="step2" class="step-title">
      Step 2: BLE app and your services
    </h2>
    
    <div class="step-body">
      <p>
        For a BLE peripheral, you need to register services that you will be exposing for other hosts. Using the bleno module, you can register your service and start advertising capabilities.
      </p>
      
      <pre>var bleno = require('bleno');&lt;br&gt;var BlenoPrimaryService = bleno.PrimaryService;
var FirstCharacteristic = require('./characteristic');
bleno.on('stateChange', function(state) {
  console.log('BLE State: ' + state);
  if (state === 'poweredOn') {
    bleno.startAdvertising('BLE Light', ['fc00']);
  }
  else {
    if(state === 'unsupported'){
      error.log("BLE error. Check board configuration.");
    }
    bleno.stopAdvertising();
  }
});

bleno.on('advertisingStart', function(error) {console.log('Advertising:' + (error ? 'error ' + error : 'success'));
  if (!error) {
    bleno.setServices([
      new BlenoPrimaryService({
        uuid: 'fc00', // Custom BLE Service
        characteristics: [] // TODO: Add characteristic
      })
    ]);
  }
});

console.log("BLE app initiated...");</pre>
      
      <p>
        The is the basic skeleton which will form your app.js. We are yet to add Characteristics to our service, which we will be doing in the next section.
      </p>
    </div>
  </div>
  
  <div class="step-container">
    <h2 id="step3" class="step-title">
      Step 3: Create BLE Characteristic
    </h2>
    
    <div class="step-body">
      <p>
        Every BLE peripheral exposes services that can be consumed by host devices. You find a comprehensive list of standard services <a href="https://developer.bluetooth.org/gatt/services/Pages/ServicesHome.aspx" rel="nofollow">here</a>. Each service has a range of characteristics that used to interact with the service. We will be creating one such characteristic to operate our BLE light through a custom service.
      </p>
      
      <p>
        Use the following code for your <em>characteristic.js</em>. The Characteristic below is plain vanilla definition carrying out read, write and notify operations on a variable in memory. We will be modifying this Characteristic in next steps.
      </p>
      
      <pre>var util = require('util');
var bleno = require('bleno');

var BlenoCharacteristic = bleno.Characteristic;

// Initialize BLE Characteristic
var FirstCharacteristic = function() {
  FirstCharacteristic.super_.call(this, {
    uuid: 'fc0f',
    properties: ['read', 'write', 'notify'],
    value: null
  });
  this._value = new Buffer("OFF", "utf-8");
  console.log("Characterisitic's value: "+this._value);
  this._updateValueCallback = null;
};

// Inherit the BlenoCharacteristic
util.inherits(FirstCharacteristic, BlenoCharacteristic);

// BLE Read request
FirstCharacteristic.prototype.onReadRequest = function(offset, callback) {
  console.log('FirstCharacteristic - onReadRequest: value = ' + this._value.toString("utf-8"), offset);
  callback(this.RESULT_SUCCESS, this._value);
};

// BLE write request
FirstCharacteristic.prototype.onWriteRequest = function(data, offset, withoutResponse, callback) {
  this._value = data;
  console.log('FirstCharacteristic - onWriteRequest: value = ' + this._value.toString("utf-8"));
  if (this._updateValueCallback) {
    console.log('FirstCharacteristic - onWriteRequest: notifying');
    this._updateValueCallback(this._value);
  }
  callback(this.RESULT_SUCCESS);
};

// BLE subscribe
FirstCharacteristic.prototype.onSubscribe = function(maxValueSize, updateValueCallback) {
  console.log('FirstCharacteristic - onSubscribe');
  this._updateValueCallback = updateValueCallback;
};

// BLE unsubscribe
FirstCharacteristic.prototype.onUnsubscribe = function() {
  console.log('FirstCharacteristic - onUnsubscribe');
  this._updateValueCallback = null;
};

module.exports = FirstCharacteristic;</pre>
    </div>
  </div>
  
  <div class="step-container">
    <h2 id="step4" class="step-title">
      Step 4: Modify Characteristic for Light control
    </h2>
    
    <div class="step-body">
      <p>
        Modify <em>characteristic.js</em> to perform GPIO operation using the mraa module.
      </p>
      
      <p>
        The characteristic initialization needs to define and initialize the GPIO pins. I have a relay connected to my setup on the Arduino digital pin 3.
      </p>
      
      <pre>// Initialize BLE Characteristic
var FirstCharacteristic = function() {
  FirstCharacteristic.super_.call(this, {
    uuid: 'fc0f',
    properties: ['read', 'write', 'notify'],
    value: null
  });
  this._value = new Buffer("0", "utf-8");
  console.log("Characterisitic's value: "+this._value);
  this._light = new mraa.Gpio(3);
  this._light.dir(mraa.DIR_OUT);
  this._light.write(0);
  this._updateValueCallback = null;
}

util.inherits(FirstCharacteristic, BlenoCharacteristic);</pre>
      
      <p>
        The BLE write request will read the BLE data for a string. If the string equates to &#8220;1&#8221; we turn the light on. Else we switch it off. Quick and <em>dirty</em>.
      </p>
      
      <pre>// BLE write request
FirstCharacteristic.prototype.onWriteRequest = function(data, offset, withoutResponse, callback) {
  this._value = data;
  if (data == "1") {
    this._light.write(1);
  }
  else {
    this._light.write(0);
  }
  console.log('FirstCharacteristic - onWriteRequest: value = ' + this._value.toString("utf-8"));
  if (this._updateValueCallback) {
    console.log('FirstCharacteristic - onWriteRequest: notifying');
    this._updateValueCallback(this._value);
  }
  callback(this.RESULT_SUCCESS);
};</pre>
      
      <p>
        Do not forget to require the mraa module in the JS if you missed reading between the lines. 😉
      </p>
      
      <h2 id="step5" class="step-title">
        Step 5: Try it out
      </h2>
      
      <p>
        Run the node app.
      </p>
      
      <pre>node app.js
</pre>
      
      <p>
        Download the <a href="https://play.google.com/store/apps/details?id=com.macdom.ble.blescanner&hl=en" rel="nofollow">BLE Scanner</a> or another equivalent app for your smartphone. Connect to &#8220;BLE Light&#8221; and use the &#8220;Custom Service&#8221;. You can now read, write and subscribe to notifications from your peripheral. To turn the light on, send string &#8220;1&#8221; and &#8220;0&#8221; to turn off.
      </p>
      
      <p>
      </p>
      
      <p>
        The source code of this DIY is available to fork on <a href="https://github.com/omkarkhair/ble-light" rel="nofollow">github here</a>.
      </p>
      
      <p>
        For any questions, suggestions feel free to get back to me on <a href="https://twitter.com/omtalk" rel="nofollow">twitter</a>.
      </p>
    </div>
  </div>
</div>
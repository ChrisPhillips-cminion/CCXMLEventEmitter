#CC128EventEmitter
=================

Connects the CurrentCost (EnvIR) basestation (CC128) XML output with node.js EventEmitter.

I wanted to be able to deal with my EnvIR basestation XML output within node, but 
using node's EventEmitter rather than having to deal with the XML directly. As the EnvIR mostly conforms 
to the CC128 specification, this should work with other devices, so if you use this on another device successfully
please drop be a line with the device name/type and if possible the 'src' property from the 'base' message.

There are a few node/currentcost implementations out there, but they did not seem as simple
to interface with as I had envisaged. I'll list some I looked at below as they may be a better fit for others.

This module is initialised with a 'device' parameter representing the serial port 
your currentcost basestation is connected to.

Once you have instantiated an instance of '' it will emit various messages with relevant data that your 
implementation can listen to and act upon. I will add history events shortly (as I have no current use for them).

I use coffee-script, so examples are coffee-script

    ccSvc = require 'cc128EventSvc'

    options = {baseEventRepeatEvery: 60} #default

    envir = new ccSvc.basestation '/dev/ttyUSB3', options  #or whatever your usb device is.

    #you can this listen to those events you want
    envir.on 'data', (einfo) ->
      einfo.sensor == 0 then console.log "whole house using:#{einfo.watts} watts" 



The following events are emitted (descriptions below):
* 'base' (eventinfo)
* 'sensor' (eventinfo)
* 'impulse' (eventinfo)
* 'impulse-reading' (eventinfo)
* 'impulse-avg' (eventinfo)

The following events are planned:
* 'history' (eventinfo)


###base###
'base' events are generated every {baseEventRepeatEvery} seconds (default = 60). They contain information relating
to the basestation itself. Internally these messages are generated very frequently but they contain very little useful 
information except perhaps temperature of the base station itself, and are hence reported every 60 seconds by 
default. This can be changed by the {baseEventRepeatEvery} parameter.


###sensor###
This is emitted every time a normal sensor reading is generated, sensors are 0-9 with 0 being while house 
and 9 normally being a 'data' channel. If a 'data' channel is detected, it is actually generated as a set of
'impulse' events as they carry additional information. 

parameters: eventinfo
              

###impulse### 
This event is emitted everytime a 'data' channel is encountered. So if you have configured one of your EnvIR 
channels as a 'data' channel it will be reported as 'impulse*' rather than 'sensor', as impulse events have 
additional information.

###impulse-reading###
This event represents the 'reading' that would be present on your meter dials if you initialised the class with your 
current meter reading. It should track your meter within a reasonable margin of error. These events seem to be 
generated less often that 'sensor' events, approx every 27.5 seconds on my unit.

###impulse-avg###
This event represents the average consumption of (elec/gas/water) during the reporting periods. As the period is 
less often than 'sensor' events it may not seem to 'follow' your use pattern, but as it is based on 'pulses' it should 
be more accurate than those reported by 'sensor' events for overall consumption reporting.





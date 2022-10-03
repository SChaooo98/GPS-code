# Deprecate. The latest gps python module has been changed significantly.

import gps, os, time

session = gps.gps()

while 1:
    os.system('clear')
    session.query('admosy') 
    # a = altitude, d = date/time, m=mode,  
    # o=postion/fix, s=status, y=satellites

    print
    print ' GPS reading'
    print '----------------------------------------'
    print 'latitude    ' , session.fix.latitude
    print 'longitude   ' , session.fix.longitude
    print 'time utc    ' , session.utc, session.fix.time
    print 'altitude    ' , session.fix.altitude
    print 'eph         ' , session.fix.eph
    print 'epv         ' , session.fix.epv
    print 'ept         ' , session.fix.ept
    print 'speed       ' , session.fix.speed
    print 'climb       ' , session.fix.climb
    
    print
    print ' Satellites (total of', len(session.satellites) , ' in view)'
    for i in session.satellites:
        print '\t', i

    time.sleep(3)
gpsdData.py

main()

NEW code
import serial
import time
import string import pynmea2
while True:    port="/dev/ttyAMAO"
ser=serial.Serial(port, baudrate=9600, time=0.5)
dataout = pynmea2.NMEAStreamReader()
newdata=ser.readline()
if newdata[0:6] == "$GPRMC":
newmsg=pynmea2.parse(newdata)
lat=newmsg.latitude
lng=newmsg.longitude
gps="Latitude=" + str(lat)+ "and Longitude=" + str(lng)
print(gps)

----------------------------------------------------------------------------------------------
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
@author: Leonardo La Rocca
"""

import melopero_samm8q as mp
import melopero_ubx as ubx
import time
import sys

gps = mp.SAM_M8Q()

#Set the communication protocol to ubx for input and output
gps.ubx_only()
gps.wait_for_acknowledge(ubx.CFG_CLASS, ubx.CFG_PRT)

#Set the navigation message to be sent every time there is a navigation solution
gps.set_message_frequency(ubx.NAV_CLASS, ubx.NAV_PVT, 1)
gps.wait_for_acknowledge(ubx.CFG_CLASS ,ubx.CFG_MSG)

#Set the measurement frequency to 500 ms
gps.set_measurement_frequency(500, 1)
gps.wait_for_acknowledge(ubx.CFG_CLASS, ubx.CFG_RATE)

log_file_name = "gps_log.txt"

#take a measurement every 10 seconds for an hour 
for i in range(60 * 6):
    try:
        info = gps.get_pvt()
        #if there is a valid measurement
        if info:
            with open(log_file_name, 'a') as file:
                file.write("Valid time : {}, Valid date : {}".format(info[gps.VALID_TIME_TAG], info[gps.VALID_DATE_TAG]))
                file.write("\n")
                file.write("[{}/{}/{}] {}h:{}m:{}s".format(info[gps.DAY_TAG], info[gps.MONTH_TAG], info[gps.YEAR_TAG],
                      info[gps.HOUR_TAG], info[gps.MINUTE_TAG], info[gps.SECOND_TAG]))
                file.write("\n")
                file.write("GNSS Fix: {}".format(info[gps.GNSS_FIX_TAG]))
                file.write("\n")
                file.write("Coordinates: {} N {} E".format(info[gps.LATITUDE_TAG], info[gps.LONGITUDE_TAG]))
                file.write("\n")    
                file.flush()
            
        else :
            print("Something went wrong and no measurement was taken")
    except: 
        print("Unexpected error:", sys.exc_info()[0])
    
    time.sleep(10)

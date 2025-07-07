---
layout: post
title: CISCO router/switch config resetter
date: '2025-07-07 21:33:08 +0100'
---



During the summer holidays I was challenged with doing a project for the university, the aim was to be able to run a python script to reset the configuration files for CISCO routers and switches which are used in the networking section of the building. Students use the routers and switches for learning purposes, so they need to be reset before each class starts.
When I talked to my tutor about it, I found out that the process would normally take 30 minutes to an hour to reset all the switches and routers manually which I thought sounded very annoying and I knew there had to be a way to make it easier for everyone. 
I went into the university building and asked for a demonstration so I could understand the reset stages for both the router and the switch, another student had attempted creating the program before which had a few issues which needed to be fixed therefore I had a nice head start to see how they attempted it. 

The person who had previously attempted it used the [serial package]( https://pyserial.readthedocs.io/en/latest/pyserial.html) and I liked how simple it was to work with so I used that for my version, there was a few things I noticed with the original which I wanted to change so I decided to just start from scratch which would help me figure out a good structure to the program.

By reading the CISCO documentation I realised there were a few repeat messages and responses which are the same so I thought it would be best to create a respond function where it would take the messages and respond to the message received in the right way, for connecting to the router/switch it needed to figure out when the console cable was removed to then start a new connection when the cable was plugged into a new port, I did this by creating a check connection function which is ran on each loop of the script, the function uses the built in serial.cts function to check the Clear to Send Signal in the switch/router, if it returns False then it assumes the cable has been removed and will start a new connection. 

The switch/router resetter can be found below:



````python
# this program was made by Ryan - https://github.com/RynTurtle
import serial 
import serial.tools.list_ports
import time 

class resetter():
    def __init__(self):
        self.router_commands = ["enable","erase startup-config","reload"] 
        #enter privileged EXEC mode 
        #erase the startup config from the NVRAM
        #reload the router to start with an empty config
        self.switch_commands = ["enable","show flash","erase startup-config","reload"] 
        #["enable","show flash"]
    
        # enter privileged EXEC mode 
        # show flash memory to see if a VLAN has been created on the switch 
        # if there is then respond with deleting it 
        # remove the startup config 
        # reload 
        self.i = 0 
        self.commands = []
        # when the commands have been sent to the router/switch, a variable is set to False blocking future commands until a new connection has been made. 
        self.commands_available = True 


    def connect(self):
        # find the connections in a loop, this is for when you unplug cable and want to continue finding the connection 
        while True:
            com_ports = serial.tools.list_ports.comports() 
            for coms in com_ports: 
                #print(f"{coms.device} - {coms.description} - {coms.vid}:{coms.pid}")
                print(f"\x1b[33m [CONNECT] => attempting to connect to: {coms.device} - \x1b[0m")

                srl = serial.Serial(coms.device,baudrate=9600, timeout=8)
                # get rid of previous or pending input 
                srl.reset_input_buffer()  
                srl.reset_output_buffer()


                srl.write("\r\n".encode()) # send newline to envoke response 

                time.sleep(1)
                response = srl.in_waiting # should be info now in the queue 
                if response > 0: # if there is a response 
                    #print(f"response received")  
                    self.srl = srl # the active serial connection has been found 
                    print(f"\x1b[32m [CONNECT] => connected to: {self.srl.name} \x1b[0m")
                    self.commands_available = True 
                    self.i = 0
                    return 
                else:
                    print(f"\x1b[31m [CONNECT] => no response, closing: {coms.device} - \x1b[0m")
                    srl.close() # close the connection used  
        


    def respond(self,message):
        message = message.lower() 
        response = False 
        # "" means enter (yes)
        # changes the commands whether or not switch or router is in communication
        if "router" in message:
            self.commands = self.router_commands
        if "switch" in message:
            self.commands = self.switch_commands


        if "erasing the nvm filesystem will remove all configuration files!" in message:
            response = "" 
        elif "proceed with reload?" in message:
            response = ""
        elif "system configuration has been modified. Save?" in message:
            response = "no"
        elif "would you like to enter the initial configuration dialog?" in message:
            # set to reply only mode due to to it being rebooted
            print(f"\x1b[34m [INFO] => The switch/router has already rebooted, set to reply only \x1b[0m")
            self.commands_available = False 
            response = "no"
            
        elif "would you like to terminate autoinstall?" in message:
            response = "yes"

        elif "press RETURN" in message:
            response = ""
        
        elif "directory of flash" in message:
            if "vlan.dat" in message:
                response = "delete vlan.dat"
            else:
                print(f"\x1b[34m [INFO] => The switch doesnt have a vlan.dat to remove, you can unplug \x1b[0m")
                self.commands_available = False
                 

        elif "delete filename" in message:
            # confirm the filename 
            response = "" 
        
        elif "delete flash" in message and "[confirm]" in message:
            # confirm deletion 
            response = ""

        elif "password" in message:
            print(f"\x1b[34m [INFO] => requires password, unplug \x1b[0m")
            # stops trying to send commands if it requires a password, if tutor wants to type in password then you can change it easy
            self.commands_available = False

        if response is not False:
            self.srl.write(f"{response}\r\n".encode())
            print(f"\x1b[32m [WRITE] => {response} \x1b[0m")
            return True 
        else:
            return False
        
    def send_commands(self):
        # if all the commands have been sent
        if self.i > 0 and self.i == len(self.commands):
            self.i = 0 # reset counter to 0 
            #print(f"\x1b[34m [INFO] => completed commands for connected device \x1b[0m")
            self.commands_available = False # sent all the commands to that device, block any other commands and only respond 

        if self.commands_available and len(self.commands) > 0: 
            # write the next command in the list 

            print(f"\x1b[32m [WRITE] => {self.commands[self.i]} \x1b[0m")
            self.srl.write(f"{self.commands[self.i]} \r\n".encode())
            self.i+= 1
    
    def check_connection(self):
        # here i check if the cable has been removed, if it has then try making a connection 
        # Clear To Send signal, False if its not clear to send messages therefore assume cable is removed
        #print(self.srl.cts)
        if not self.srl.cts:
            print(f"\x1b[34m [INFO] => Cable removed, starting new connection\x1b[0m")
            self.srl.close()
            self.connect() 

    def loop(self):
        while True:
            self.check_connection()
            if self.srl.in_waiting > 0:  # handle messages when there is a message to be received
                cisco_message = self.srl.read(4096).decode('utf-8')
                print(f"\x1b[37m [RECEIVED] => {cisco_message} \x1b[0m")

                response = self.respond(cisco_message) 
                # if theres no response needed then send the command instead, this avoids a response and a command trying to be sent at the same time.
                if response == False:
                    self.send_commands()     
        self.srl.close() 


"""

-- INFO --
!! wait until the program has noticed the unplug before plugging in again otherwise it wont start new connection !! 

# for the router 
# send all commands, then it has to reboot 
# reply to the two messages after reboot 

# you can either wait for the full reboot whilst plugged in to do everything for the specific router or 
# you could plug in, send commands, unplug then go over each one again to reply to the final messages 
# this might reduce time waiting 

# for the switch 
# show flash, if there is vlan then delete it and reboot 
# if no vlan then allow user to move on  


step 1: plug into router/switch
step 2: allow it to send commands and replies 
step 3: unplug, allow to detect and replug into other switch/router 

for now if it sees a message that you only get on reboot it will prevent further commands 
that way it can add the final yes/no 


"""
r = resetter()
r.connect() 
r.loop()

````
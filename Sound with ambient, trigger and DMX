# Pre-use install
# sudo apt-get install python-pygame

# sudo nano /etc/apt/sources.list
# insert > deb http://apt.openlighting.org/debian squeeze main

# sudo apt-get update
# sudo apt-get install ola
# sudo apt-get install ola-python ola-rdm-tests

# Lamp: Cameo CLP64RGBA10BS
# Mode: 3CH
# DMX start ch: 1

soundTrigger = "/home/pi/jail_cell_door.wav"
soundAmbient = "/home/pi/rumble.wav"
soundDebounce = 1 # Debounce set in seconds

import pygame
import time, os
import RPi.GPIO as GPIO
import os.path
import thread

os.system("amixer cset numid=3 1") # Set output to 3.5mm jack
os.system("amixer set PCM -- 400") # Set alsa volume to 100%

# Set up and initiate mixer
pygame.mixer.pre_init(44100, -16, 2, 4096)
pygame.mixer.init()
pygame.mixer.set_num_channels(8)

triggerChannel = pygame.mixer.find_channel()


# Set up thread for sending data to OLA
def lampBlink():
    os.system("ola_streaming_client -u 1 -d 255,255,255")
    time.sleep(0.1)
    os.system("ola_streaming_client -u 1 -d 0,0,0")


# Define function which will get triggered by the GPIO input
def pirTriggered(channel):
    global triggerChannel
    triggerChannel = pygame.mixer.find_channel()
    triggerChannel.play(triggerEffect)
    thread.start_new_thread(lampBlink,()) # Start thread for blinking the lamp with DMX
    print("Triggered")


# Set up looping ambient sound
if os.path.exists(soundAmbient):
    print("Starting ambient sound")
    pygame.mixer.music.load(soundAmbient)
    pygame.mixer.music.set_volume(0.2)
    pygame.mixer.music.play(-1) # start music looping
else:
    print("Ambient file not found")


# Set up trigger sound
if os.path.exists(soundTrigger):
    print("Setting up trigger sound")
    triggerChannel.set_volume(1.0)
    triggerEffect = pygame.mixer.Sound(soundTrigger)
    # Set up GPIO trigger
    GPIO.setmode(GPIO.BOARD)
    GPIO.setup(23, GPIO.IN, pull_up_down = GPIO.PUD_DOWN)
    # Add event for detecting the signal from PIR
    GPIO.add_event_detect(23, GPIO.RISING, callback=pirTriggered, bouncetime = (soundDebounce * 1000))
else:
    print("No trigger file found")


while 1:
    time.sleep(1)

GPIO.cleanup()

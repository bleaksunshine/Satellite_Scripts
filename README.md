# Satellite_Scripts
These are the scripts essensial for the operation of the project 

Now that you've got a working RTL dongle on your Raspberry Pi, it's time to actually make it receive some weather maps. Make sure your antenna is connected and located as best as possible. I've got mine mounted in my attic and it works fairly well from there. If you can mount it outside, that would be even better. Get it as high as possible.

We are going to need to create a few scripts to automate everything. This is where that Linux familiarity is going to come in.

First we'll create a couple directories to hold our files

Next we'll make the two scripts that kick off the scheduling. The first is 'schedule_all.sh'. This script will be called nightly at midnight. It downloads satellite pass information from celestrak and creates a TLE file for predict to use. Then it removes all AT jobs from the system, so no passes get double scheduled. Finally, it calls the second script 'schedule_satellite.sh' for each satellite we are interested in.

The second script, 'schedule_satellite.sh', recurses through each pass of the given satellite for the current day. It determines if the maximum elevation is 20 degrees or greater. If it is, then it calculates the length of the pass, and schedules the recording and processing of the pass. If the maximum elevation is less than 20 degrees, the pass is ignored, as it generally won't produce a decent image.

When the time comes for a give pass to be recorded and processed, at kicks off the final script 'receive_and_process_satellite.sh'. When this script is called, it uses rtl_fm to receive the audio from the satellite pass, and sends that audio to sox for processing. Sox saves the audio to a file. Once the pass is complete, wxmap is called to generate an overlay map for the image. Finally, wxtoimg is called to generate the actual image and place the overlay map on it.

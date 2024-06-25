## Why use docker for 3d printing?

Several years ago, I was told about docker while commiserating with a freind about the lack of raspberry pi devices or at least their massivly blown up price during the pandemic. My friend pointed out docker and it opened a world up. after lots of hours watching youtube videos, digging into guides and setup documentation and poking at some basic containers using the single rasperry pi I had in my posession I started my project. At the time I had a few things already running on that pi and I was going to use it to also host octoprint to help me in managing my ender 3 pro. While this would have been a good enough use case and I will get into some of the things I had to figure out to make this single install work, it became even more important when I gained an additional 3 3d printers from a friend. these machines, to be easily operated and maintaind also needed octoprint running. this was the true benifit of running octoprint in a container. Because this application is really light, while it is easier to install directly to an sd card and run it bare metal on a raspberry pi both because it requries connecting to usb and because it greatly simplifies connection to a camera for streaming (more on that later). From what I understand you can hook more than one usb up to a pi running octoprint bare metal but I havent seen many people use this method. I assume it is because octoprint really seems to be designed to run one printer. One rasperry pi for one printer at a time when a medium spec pi 4 was hanging out in the 200$ range if you could find them at all was a really steap ask. In retrospect a Pi 3 would have been cheaper and have run just fine but again, more on that later. When I set to adding the additional 3 printers to my fleet of machines, I started with first converting my original octoprint container from running off of a docker "one liner" and building a docker compose file for it. This process was really straight forward because the docker hub page for the octoprint app has a compose file right there. The things I had to add and modify were easy enough for the first machine, simply probe for and pass though the usb device and also give the web portal its own defined port. This process would become more dificult as I duplicated the docker compose file for the usb pass though which I will get to in a bit. the ports being duplicated was easy enough and I made things simple by using 4 adjacent ports with numbers 1-4 in order of the printers. As far as the usb connections go, the printers, running all at the same time are not capable of saturating the usb bus, and the main problem was that as you plug things in and un-plug things, the linux operating system will change the port numbers for each individual device. This is an issue because the decleration for which usb device bets passed into the docker container is static. it cannot tell the diffrence between two diffrent usb inputs as far as I can tell. I have a feeling that I could possibly figure out how to script a way of polling the 3d printer for some kind of finger print at the time of spinning up the docker container to grab the right usb device automaticly but I havent figured that out yet. for now what that means is each /dev/ttyusb gets passed into its own container correctly by simply shutting down all containers, unpluging all usb devices from the host system, and then adding the devices and starting the containers that corespond with that printer 1 at a time. this way the usb inputs are enumerated correctly to give each instance of octoprint the correct machine. this is a pretty hacky way of doing things but unless multiple 3d printers get unpluged at the same time it is pretty functional once setup.
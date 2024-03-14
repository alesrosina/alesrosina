In my child's daycare the have a toy called [Toniebox](https://tonies.com). Well, it's quite cool device for kids, that has no screens, but very intuitive UI - you put a small toy in a shape of eg. Mickey Mouse or Elsa and it will start playing a song or audio book. But, it's all connected to cloud - so nothing works without internet and you also need an online account. From privacy point of view I think this is not acceptable, since Toniebox always knows what my child has listened to. At least not from my point of view. 

But beside privacy concerns I also wanted to do some tinkering and soldering with actual hardware. Quite quickly I've discovered a project called [PhonieBox](https://github.com/MiczFlor/RPi-Jukebox-RFID), which is an open source project and can be run on Raspberry Pi. Not to mention, that it can run totally offline and you can actually upload MP3 files with playlists.

## Phase 1 - prototype

With this in mind, I went shopping - since I already had an old Raspberry Pi 1 (from 2013) and Elatec TWN3 MultiISO RFC reader/writer laying around, I've quickly managed to make a prototype, that has proven to work and approved by my kid.

![First prototype](/assets/images/2024-03-13-personal-projects-(part-1)/1.jpg)

Saddly using a USB RFC reader, which basically simulates a keyboard has a drawback - you cannot detect, if a NFC tag was removed.

## Phase 2 - final "product"

Since the test was a success, I've decided to upgrade the whole project to a bit nicer box with few hardware buttons (volume and power). 

To get a bit technical, here's the list of components:

- Cheap (~ $20) USB speakers
- Rotary knob KY-040 
- RFID module RC522
- LED green light
- Basic button
- Raspberry PI board
- Battery pack
- NFC sticker tags

After ordering all parts, I also needed a box. I've decided for a clean box with as little buttons as possible. And since I don't have a lot of tools, not to mention skills, I've decided for wooden box. It's the easiest to work with basic tools that I have.

### Box

For side panels I went with a bit thicker wood plates and for front I went with a very thin wood plate. I would not go technical here, since I went to local hardware store and asked something in line "I like this and this wood and can you please cut it with this measurements". No idea which wood is that actually, it just looked nice to me and they had those small pieces in stock.

![Wooden box frame](/assets/images/2024-03-13-personal-projects-(part-1)/2.jpg)
 
### Assembly

When all the parts finally arrived, I've finally managed to assemble everything together. My skills for soldering are quite bad, but I've managed to shorten cables from speaker, solder LED light and power button to cables. Everything else was just attaching cables to correct pins on Raspberry PI.

![Wiring cables](/assets/images/2024-03-13-personal-projects-(part-1)/3.jpg)

### Final "product"

I must say, it ended up looking quite nice and for the skills (and tools) I have, it turned out quite nice. The most important thing was, that also my kid found it nice and started using it right away.

For final touches I've also order some miniature figures and stick NFC tags on bottom. With this my kid could just place a toy on top of speaker and it will start playing music connected with this tag.

![Final product](/assets/images/2024-03-13-personal-projects-(part-1)/final.jpg)

## Phase 3 - Aftermath

After few months I did a few modifications. The biggest issue was battery - since Raspberry PI does not completely turn off, it drains battery quite fast. This becomes a problem, when your kid wants to listen to music only once in a while. So, I've removed the battery and just bought a long USB charging cable, since speaker is anyway quite heavy due to thick wood used and it's sitting on one place all the time.

The second thing I've added was to install also [Shareport Sync](https://github.com/mikebrady/shairport-sync), since I have a few AirPlay speakers already at home. This turned out to be the most used feature, since our kid is rarely only in her room, and playing stories on all speakers in our home is a must.

And since I've removed the battery, now the power button is kinda obsolete, so I need to figure out for what it should be used.

There's also some ideas, that rotary knob should also control volume, when music is played over AirPlay, but that's not yet configured. 

Another task for future added to my endless "personal tasks list".
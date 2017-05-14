# gmusicaiy
## Google Music action for Google AIY Voice Kit

Command your Google AIY Voice Kit to play playlists from your Google Music account using `play <playlist>`.

## Installation

Requires VLC for Raspbian: `sudo apt-get install vlc`

Requires [gmusicapi](https://github.com/simon-weber/gmusicapi) and a Google Music account.

The code is setup to output text to an I2C display called [Scroll pHAT](https://shop.pimoroni.com/products/scroll-phat), make sure to comment/replace those lines as needed.

Make sure that `playscroll.py` and `vlc.py` are located in `/home/pi/voice-recognizer-raspi/src` or that the system path is updated to point at their folder location.

Add an action to `action.py` as follows:

```python
sys.path.append('/home/pi/Python/gmusicaiy')
from playscroll import Player
import time
import RPi.GPIO

class PlayAction(object):

    """Plays the provided playlist."""

    def __init__(self, say):
        self.player = Player('email', 'password', 'device_id')
        self.say = say
        
    def run(self, voice_command):
        playlist = voice_command.replace("play","").strip()
        if self.player.load_playlist(playlist):
            self.say("Playing " + playlist)
            self.player.start_playlist()

            RPi.GPIO.setmode(RPi.GPIO.BCM)
            RPi.GPIO.setup(23, RPi.GPIO.IN)

            # Wait for button press to stop
            while self.player.playing and RPi.GPIO.input(23):
                time.sleep(1)

            self.player.stop()

def make_actor(say):
    actor.add_keyword(_('play'), PlayAction(say)) 
```

Make sure to provide your own email address and password. If you don't know your device ID, an error will be raised and a list of your valid IDs displayed in the terminal window. Make sure to copy one of these into the script. 

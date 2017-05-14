# gmusicaiy
## Google Music action for Google AIY Voice Kit

Requires VLC for Raspbian: `sudo apt-get install vlc`

Requires gmusicapi, available [here](https://github.com/simon-weber/gmusicapi)

The code is setup to output text to an I2C display called [Scroll pHAT](https://shop.pimoroni.com/products/scroll-phat), make sure to comment/replace those lines as needed.

Add an action as follows:

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

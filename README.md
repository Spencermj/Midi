# Problem
We want to iterate through a MIDI file and extract the key attributes.

# Question
1. How do you open a MIDI file in python?
2. How do you get loudness and pitch?
3. How do you determine the length of a note?

# Resources
1. [python-midi]
2. [Reading MIDI]
3. [Issues Forum]
4. [Note Values]
5. [Extracting Pitch]
6. [NoteOn Velocity]
7. [Ticks]

### 1. Opening a MIDI file in python with [python-midi]
After searching through multiple MIDI modules, it became apparent that most of them were more suited to taking MIDI files and converting them into data to be streamed to a device. This module, however, allows the user to directly access a MIDI file and extract key attributes like pitch and velocity. Events such as NoteOn and NoteOff events are stored as objects in a list, allowing for easy iteration through all the events that occur in a MIDI file. This module lacks documentation, but an [Issues Forum] is available that gives some insight into how to accomplish basic tasks with python-midi. In addition, the creator provided a couple examples showing the very basics. For example [Reading Midi] gives the following code which opens a MIDI file and prints the events to the console:

```python
import midi
pattern = midi.read_midifile("example.mid")
print pattern
```

These resources answer the question 1: how do you open a midi file in python?

### 2. Extracting loudness and pitch
After searching the forums, I discovered a post discussing how to find the pitch of every note in a MIDI file. A simple getter method, get_pitches() returns a value indicating which pitch is being played during any NoteOn event. [Pitch Values] provides the value for every possible note, making it easy to play the appropriate note for any event. The following code from [Extracting Pitch] shows how to get the pitch for any event by iterating through the MIDI file and printing the pitch if the current element is a NoteOn or NoteOff event:

```python
import midi
pattern = midi.read_midifile('mary.mid')

for track in pattern:
    for event in track:
        if isinstance(event, midi.NoteEvent): # check that the current event is a NoteEvent, otherwise it won't have the method get_pitch() and we'll get an error
            print event.get_pitch()
```

Python-midi makes the loudness of an event just as easy to find as pitch. Rather than calling a method, though, loudness can simply be found in the velocity attribute of an event object. While looking through the forums, I discovered [NoteOn Velocity] which informed me of a very important fact involving the velocity of a track in regards to the duration of a note. Some MIDI files, rather than calling a NoteOffEvent to stop a certain pitch from being played, call a NoteOnEvent with a velocity of 0. When synthesized, MIDI files do not play two occurrences of the same pitch at the same time, so by setting the velocity to 0 the creator of the MIDI essentially signaled for the note to end. This means that, when iterating through a MIDI file to synthesize it, both NoteOnEvents with a velocity of 0 and NoteOffEvents must be considered when determining when to end a note. 

These resources answer question 2: how do you get loudness and pitch?


### 3. Determining Duration From Ticks
The creator of python-midi did the users a favor by including a section in his .readme about ticks. According to [Ticks], a tick is the lowest level resolution of a MIDI track and can be used with both the resolution and the tempo of the track to determine the duration of any note. The length of a tick can be found by dividing the tempo of the track in microseconds by the resolution of the track. This will give the length of a single tick in a track, which can be used to determine the duration of any given note. One issue that comes up when using ticks to determine duration is that ticks are measured relative to when the last note occurred. This makes it very difficult to keep track of the change in ticks between events when multiple notes are being played at once. As [] though, the creator of python-midi included the make_ticks_abs() funtion to make finding the duration of notes much easier. When the ticks are set to absolute in a track, you can simply subtract the tick value where a note started from the tick value where it ended and convert ticks to second.

These resources answer question 3: how do you determine the length of a note?

[python-midi]: https://github.com/vishnubob/python-midi
[Reading Midi]: https://github.com/vishnubob/python-midi/blob/master/examples/example_2.py
[Issues Forum]: https://github.com/vishnubob/python-midi/issues/
[Note Values]: http://newt.phys.unsw.edu.au/jw/notes.html
[Extracting Pitch]: https://github.com/vishnubob/python-midi/issues/38
[NoteOn Velocity]: https://github.com/vishnubob/python-midi/issues/13
[Ticks]: https://github.com/vishnubob/python-midi#Side_Note_What_is_a_MIDI_Tick

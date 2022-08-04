# How to copy, read and write Paxton fobs and cards with an RFIDler

Paxton fobs and readers are popular in the UK especially the Net2 system where the fobs look like this with a blue ring.

Readers often look like this

This how to covers how to read an exisiting tag and write data to another tag. If that tag has been authorised for entry the new tag will be able to gain entry and will be seen by the reader be the same tag effetively a clone. You can copy cards to fobs and fobs to cards.

## Equipment
* An RFIDLer avialable here from one of the tools authors http://rfidiot.org/
* Enamelled copper wire I used 33swg or ~0.25 mm
* Some Paxton fobs or hitag2 cards
* (optional) a soldering iron but you my be able to get away without one

These Paxton fobs use hitag2 technology and can be copied to hitag2 cards and fobs etc.

## Antennas
The RFIDler comes with a coil antenna that is very good for reading cards and sniffing readers. It does not however do well with other tag form factors. In order to read and write to a Paxton fob I had to wind my own antenna. This is covered further down.

## Reading a card

First we need to set the RFIDLer to hitag2 cards config
```
set tag hitag2
OK 
*HITAG2> save
OK
HITAG2> 
```
Next we need to try reading the tag serial number. This is readonly and cannot be changed but it is not used in identifying access. It can be read without knowing the password and logining into the tag and is also known as page 0.

```
HITAG2> READER
12345678
12345678
12345678
```
You should get the tag serial number being repeated continously which means there is a good strong signal. If you don't then adjust the tag coil position until you do.

The next step is to login to the tag that is get passed the tags auth.

```
HITAG2> login BDF5E846
06F907C2
```
The response is the config bit and tag password. This is held on page 2 of the tag and is the same for all Paxton fobs I tested.

Once you have logged in you can now read the 8 pages of data.

```
HITAG2> read 0
0: 12345678
HITAG2> read 1
1: BDF5E846
…
```

repeat up to page 7
Once you have this data you have all the info you need to clone the tag. The important pages are 4-7 these contain the data on which the system identifes for access.

## Can I Emulate a Paxton fob?
Not currently with an RFIDler. The data flow is more complicated than some other tags involving a back and forth of commands the reader could send. The chips in hitag2 cards handle these commands really well. Why not just use one of those i.e. clone to a hitag2 card.



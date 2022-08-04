# How to copy, read and write Paxton fobs and cards with an RFIDler

Paxton fobs and readers are popular in the UK especially the Net2 system where the fobs look like this with a blue ring.

Readers often look like this

This how to covers how to read an exisiting tag and write data to another tag. If that tag has been authorised for entry the new tag will be able to gain entry and will be seen by the reader be the same tag. You can copy cards to fobs and fobs to cards.

## Equipment
* An RFIDLer avialable here from one of the tools authors http://rfidiot.org/
* Enamelled copper wire I used 33swg or ~0.25 mm
* Some Paxton fobs or hitag2 cards
* (optional) a soldering iron but you my be able to get away without one

These Paxton fobs use hitag2 technology and 

## Atennas
The RFIDler comes with a coil atenna that is very good for reading cards and sniffing readers. It does not however struggle with other tag form factors. In order to read and write to a Paxton fob I had to wind my own atenna. This is covered further down.

## Can I Emulate a Paxton fob?
Not currently with an RFIDler. The data flow is more complicated than some other tags involving a back and forth of commands the reader could send. The chips in hitag2 cards handle these commands really well. Why not just use one of those i.e. clone to a hitag2 card.



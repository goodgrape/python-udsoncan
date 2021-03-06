Underlying protocol (Connections)
=================================

Basics
------

Since UDS is an application layer protocol, it must be used over a data transport protocol. The actual industry mostly uses ISO-TP protocol (ISO-15765-2) over CAN bus (ISO-11898).

Controller Area Network (CAN) protocol is a link layer protocol that sends data over small chunks of 8 bytes. ISO-TP is a transport protocol that allow the transmission of larger frames, usually 4095 bytes maximum altough the 2016 version of the standard uses sizes defined over 32bits, which would theoretically allow frames of 4GB.

ISO-TP has been designed to be used for UDS. The actual ISO-15765 protocol comes in 4 parts. ISO-15765-2 tells how to transmit large frame, ISO-15765-4 defines how to map the ISO-TP fields to a UDS message.

This project does not implements any communication protocol below the UDS layer, but provide a standard interface to interract with them.

How to
------

Access to the underlying protocol is done through a **Connection** object. A user can defines its own Connection object by inheriting the BaseConnection object and implementing the abstract methods.

The main interface to use with the Connection object are:

.. automethod:: udsoncan.connections.BaseConnection.send
.. automethod:: udsoncan.connections.BaseConnection.wait_frame

Available Connections
---------------------

SocketConnection
################

.. autoclass:: udsoncan.connections.SocketConnection

IsoTPConnection
################

.. autoclass:: udsoncan.connections.IsoTPConnection

QueueConnection
################

.. autoclass:: udsoncan.connections.QueueConnection

Defining a new Connection
-------------------------

In order to define a new connection, 4 methods must be implemented as they will be called by the Client object.

 .. automethod:: udsoncan.connections.BaseConnection.open
 .. automethod:: udsoncan.connections.BaseConnection.close
 .. automethod:: udsoncan.connections.BaseConnection.specific_send
 .. automethod:: udsoncan.connections.BaseConnection.specific_wait_frame
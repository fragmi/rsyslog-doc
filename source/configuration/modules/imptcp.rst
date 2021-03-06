imptcp: Plain TCP Syslog
========================

Provides the ability to receive syslog messages via plain TCP syslog.
This is a specialised input plugin tailored for high performance on
Linux. It will probably not run on any other platform. Also, it does not
provide TLS services. Encryption can be provided by using
`stunnel <rsyslog_stunnel.html>`_.

This module has no limit on the number of listeners and sessions that
can be used.

**Author:**\ Rainer Gerhards <rgerhards@adiscon.com>

Configuration Directives
------------------------

This plugin has config directives similar named as imtcp, but they all
have **P**\ TCP in their name instead of just TCP. Note that only a
subset of the parameters are supported.

Module Parameters
^^^^^^^^^^^^^^^^^

These paramters can be used with the "module()" statement. They apply
globaly to all inputs defined by the module.

-  Threads <number>
   Number of helper worker threads to process incoming messages. These
   threads are utilized to pull data off the network. On a busy system,
   additional helper threads (but not more than there are CPUs/Cores)
   can help improving performance. The default value is two, which means
   there is a default thread count of three (the main input thread plus
   two helpers). No more than 16 threads can be set (if tried to,
   rsyslog always resorts to 16).

Input Parameters
^^^^^^^^^^^^^^^^

These parameters can be used with the "input()" statement. They apply to
the input they are specified with.

.. function:: AddtlFrameDelimiter <Delimiter>
   This directive permits to specify an additional frame delimiter for
   plain tcp syslog. The industry-standard specifies using the LF
   character as frame delimiter. Some vendors, notable Juniper in their
   NetScreen products, use an invalid frame delimiter, in Juniper's case
   the NUL character. This directive permits to specify the ASCII value
   of the delimiter in question. Please note that this does not
   guarantee that all wrong implementations can be cured with this
   directive. It is not even a sure fix with all versions of NetScreen,
   as I suggest the NUL character is the effect of a (common) coding
   error and thus will probably go away at some time in the future. But
   for the time being, the value 0 can probably be used to make rsyslog
   handle NetScreen's invalid syslog/tcp framing. For additional
   information, see this `forum
   thread <http://kb.monitorware.com/problem-with-netscreen-log-t1652.html>`_.
   **If this doesn't work for you, please do not blame the rsyslog team.
   Instead file a bug report with Juniper!**

   Note that a similar, but worse, issue exists with Cisco's IOS
   implementation. They do not use any framing at all. This is confirmed
   from Cisco's side, but there seems to be very limited interest in
   fixing this issue. This directive **can not** fix the Cisco bug. That
   would require much more code changes, which I was unable to do so
   far. Full details can be found at the `Cisco tcp syslog
   anomaly <http://www.rsyslog.com/Article321.phtml>`_ page.

.. function:: SupportOctetCountedFraming on/off

   *Defaults to "on"*

   The legacy octed-counted framing (similar to RFC5425
   framing) is activated. This is the default and should be left
   unchanged until you know very well what you do. It may be useful to
   turn it off, if you know this framing is not used and some senders
   emit multi-line messages into the message stream.

.. function:: ServerNotifyOnConnectionClose on/off

   *Defaults to off*

   instructs imptcp to emit a message if the remote peer closes a
   connection.

.. function:: KeepAlive on/off

   *Defaults to off*

   enable of disable keep-alive packets at the tcp socket layer. The
   default is to disable them.

.. function:: KeepAlive.Probes <number>

   The number of unacknowledged probes to send before considering the
   connection dead and notifying the application layer. The default, 0,
   means that the operating system defaults are used. This has only
   effect if keep-alive is enabled. The functionality may not be
   available on all platforms.

.. function:: KeepAlive.Interval <number>

   The interval between subsequential keepalive probes, regardless of
   what the connection has exchanged in the meantime. The default, 0,
   means that the operating system defaults are used. This has only
   effect if keep-alive is enabled. The functionality may not be
   available on all platforms.

.. function:: KeepAlive.Time <number>

   The interval between the last data packet sent (simple ACKs are not
   considered data) and the first keepalive probe; after the connection
   is marked to need keepalive, this counter is not used any further.
   The default, 0, means that the operating system defaults are used.
   This has only effect if keep-alive is enabled. The functionality may
   not be available on all platforms.

.. function:: Port <number>

   Select a port to listen on

.. function:: Name <name>

   Sets a name for the inputname property. If no name is set "imptcp"
   is used by default. Setting a name is not strictly necessary, but can
   be useful to apply filtering based on which input the message was
   received from.

.. function:: Ruleset <name>

   Binds specified ruleset to next server defined.

.. function:: Address <name>

   On multi-homed machines, specifies to which local address the
   listerner should be bound.

.. function:: RateLimit.Interval [number]

   *Default is 0, which turns off rate limiting*

   Specifies the rate-limiting interval in seconds. Set it to a number 
   of seconds (5 recommended) to activate rate-limiting.
   
.. function:: RateLimit.Burst [number]

   *Default is 10,000*

   Specifies the rate-limiting burst in number of messages.

Caveats/Known Bugs
------------------

-  module always binds to all interfaces

Example
-------

This sets up a TCP server on port 514:

::

  module(load="imptcp") # needs to be done just once 
  input(type="imptcp" port="514")

Legacy Configuration Directives
-------------------------------

.. function:: $InputPTCPServerAddtlFrameDelimiter <Delimiter>

   Equivalent to: AddTLFrameDelimiter

.. function:: $InputPTCPSupportOctetCountedFraming on/off

   Equivalent to: SupportOctetCountedFraming

.. function:: $InputPTCPServerNotifyOnConnectionClose on/off

   Equivalent to: ServerNotifyOnConnectionClose.

.. function:: $InputPTCPServerKeepAlive <on/**off**>

   Equivalent to: KeepAlive

.. function:: $InputPTCPServerKeepAlive\_probes <number>

   Equivalent to: KeepAlive.Probes

.. function:: $InputPTCPServerKeepAlive\_intvl <number>

   Equivalent to: KeepAlive.Interval

.. function:: $InputPTCPServerKeepAlive\_time <number>

   Equivalent to: KeepAlive.Time

.. function:: $InputPTCPServerRun <port>

   Equivalent to: Port

.. function:: $InputPTCPServerInputName <name>

   Equivalent to: Name

.. function:: $InputPTCPServerBindRuleset <name>

   Equivalent to: Ruleset

.. function:: $InputPTCPServerHelperThreads <number>

   Equivalent to: threads

.. function:: $InputPTCPServerListenIP <name>

   Equivalent to: Address

Caveats/Known Bugs
------------------

-  module always binds to all interfaces

Example
--------

This sets up a TCP server on port 514:

::

  $ModLoad imptcp # needs to be done just once 
  $InputPTCPServerRun 514


haskell-openflow
================

[![Build Status](https://travis-ci.org/brooksbp/haskell-openflow.png?branch=master)](https://travis-ci.org/brooksbp/haskell-openflow)

OpenFlow v1.0.0 protocol [1] implementation in Haskell.

* "OpenFlow enables networks to evolve, by giving a remote controller the power to modify the behavior of network devices, through a well-defined 'forwarding instruction set.'" [2]
* "The OpenFlow protocol, which ONF is standardizing, increases network functionality while lowering operating costs through simplified hardware, software, and management." [3]

Example OpenFlow server:

```haskell
handleSwitch :: Socket -> SockAddr -> IO ()
handleSwitch sock caddr = 
  forever $ do
    frame <- readOfpFrame sock
    case frame of
      (OfpFrame (OfpHeader _ _ _ xid) (OfptEchoRequest dat)) -> do
        let resp = (OfpFrame (OfpHeader 1 0 0 xid) (OfptEchoReply dat))
        sendAll sock $ encode resp
      (OfpFrame (OfpHeader _ _ _ xid) (OfptPacketIn (OfpPacketIn bid len inp reason dat))) -> do
        let resp = (OfpFrame (OfpHeader 1 0 0 xid) (OfptPacketOut (OfpPacketOut bid inp [(OfpOutput ofppAll 0)] dat)))
        sendAll sock $ encode resp
      _ -> putStrLn "unhandled packet"
```

[1] http://www.openflow.org/documents/openflow-spec-v1.0.0.pdf

[2] http://www.openflow.org/

[3] https://www.opennetworking.org/


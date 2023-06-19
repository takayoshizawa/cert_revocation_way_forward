# cert_revocation_way_forward
TLS server certificate revocation improvement

The code files in this repository are provided as a supplemental information for the paper "Certicate Revocation  Is there A Way Forward?"
These code files represent incremental changes on top of the "tlslist-ng" (https://github.com/tlsfuzzer/tlslite-ng) to include additional information in clientHello and serverHello messages and corresponding logic that are discussed in the above-mentioned paper. 

For the rest of this document, we refer to the codes in "tlslite-ng" as the "baseline."

Two files in "tlslist-ng" are modified to implement the idea discussed in the paper. All the rest of the codes use the "baseline" as-is with no modification. The modified code files are:
1. tlsconnection.py
2. messages.py

The most straightforward way to see exactly what the changs are from the "baseline" codes is to do "diff" these two files against the one under "tlslite-ng" repository.

The high-level description of the modifications in these two files:
1. tlsconnection.py
   
   1.a) client-side processing:
     Sending clientHello message
      - generate a 4-byte client ID (a fixed value) and a 16-byte random number (the random number is sent in the 2nd time onward only).
      - insert these two values in the clientHello message and send it to the TLS server.
    Receiving serverHello message
      - extract the client key and store it (when only the cleint key is present in serverHello message).
      - calculate the expected response value.
      - extract the response and check against the expected response value. If they match, declare success and continue with the TLS session establishment. Otherwise, declare failure.
        
   1.b) server-side processing
     Receiving cleintHello message
      - extract the client ID and random number received in the clientHello message.
      - if the random number is not included in the cleintHello message (i.e. 1st access from this client), generate only a 16-byte client-specific client key.
      - otherwise, generate a 16-byte client-specific client key and a 16-byte response values.
      - insert the client-specific client key and a response (if the random number is included in the clientHello message) to the serverHello message.
      - send the serverHello message.
        
3. messages.py
   Message handling routines for clientHello and serverHello message mentioned above.

--- end of the note ---

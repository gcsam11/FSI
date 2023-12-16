# Find-my-TLS

We started by openning the file dump.pcapng that was provided to us by the CTF platform.

In the moodle, we were told that the flag could be rebuilt using varoious details of that connection:

```
flag{<frame_start>-<frame_end>-<selected_cipher_suite>-<total_encrypted_appdata_exchanged>-<size_of_encrypted_message>}

- <frame_start> and <frame_end> are the first and last (respectively) frame numbers corresponding to the TLS handshake procedure.
- <selected_cipher_suite> is the ciphersuite chosen for the TLS connection.
- <total_encrypted_appdata_exchanged> is the total sum of the size of encrypted data exchanged on this channel, until its termination.
- <size_of_encrypted_message> is the size of the message encrypted in the handshake that completed the handshake procedure.
```

We were also told that we needed to foccus on a specific TLS connection, more precisely in a handshake in which we could find the random number '52362c11ff0ea3a000e1b48dc2d99e04c6d06ea1a061d5b8ddbf87b001745a27' used in the Client Hello message.

And so the first step was to search for the specific connection that we were supposed to foccus on, to do that we could click on the bar with the placeholder text 'Apply a filter' and type the follwoing: 'tls.record.content_type == 22 && tls.handshake.type == 1'. After doing that, we clicked on the blue button with a right arrow on the right side of the bar, to proceed to apply the filter.

After filtering the enormous ammount of connections we clicked on 'Find a packet' (magnifying glass icon on the top bar of Wireshark) and chose the option 'Hex Value' and typed in '52:36:2c:11:ff:0e:a3:a0:00:e1:b4:8d:c2:d9:9e:04:c6:d0:6e:a1:a0:61:d5:b8:dd:bf:87:b0:01:74:5a:27'. Then we clicked on the `Find` button.

By doing so we found out the frame number of Client Hello message, that being the <frame_start> since the Client Hello is the first frame in the TLS handshake procedure in question.




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

We were also told that we needed to foccus on a specific TLS connection, more precisely in a handshake in which we could find the random number "52362c11ff0ea3a000e1b48dc2d99e04c6d06ea1a061d5b8ddbf87b001745a27" used in the Client Hello message.

And so the first step was to search for the specific connection that we were supposed to foccus on, to do that we could click on the bar with the placeholder text "Apply a display filter..." and type the follwoing: "tls.record.content_type == 22 && tls.handshake.type == 1". After doing that, we clicked on the blue button with a right arrow on the right side of the bar, to proceed to apply the filter.

![Image 1](/images/ctf13-1st_filter.png)

After filtering the enormous ammount of connections we clicked on 'Find a packet' (magnifying glass icon on the top bar of Wireshark) and chose the option "Hex Value" and typed in "52:36:2c:11:ff:0e:a3:a0:00:e1:b4:8d:c2:d9:9e:04:c6:d0:6e:a1:a0:61:d5:b8:dd:bf:87:b0:01:74:5a:27". Then we clicked on the `Find` button.

By doing so we found out the frame number of Client Hello message, that being the <frame_start>, since the Client Hello is, in fact, the first frame in the TLS handshake procedure in question.

After this, purely for convenience, we decided to mark (`command + M`), and we also decided to ignore (`command + D`) all of the other connections that wouldn't be needed to find the rest of the flag components.

Since the frame number 819 is the last frame to have a "Encrypted Handshake Message", we could concluded that it's frame number would be the <frame_end>. 

When trying to look for which cipher suite was selected, we depared ourselves with two names in the "Cipher Suites" section of the Client Hello message.

So to confirm which one was the selected one we clicked on the frame number 816 (Server Hello message) and scrolled down once again to the 'Cipher Suites' section, verifying that the one selected was "TLS_RSA_WITH_AES_128_CBC_SHA256", that being the <selected_cipher_suite>.

We decided then to click once again on the "Apply a display fiter..." bar and type in: "tls.record.content_type == 23 && frame.number > 814 && frame.number < 824" (814 because it's the frame_start and 824 because it's the last frame of that connection that uses the TLS protocol). The results of applying that filter were the frames number 820 and 821, and scrolling down to see their lengths, which were 80 and 1184, respectively, the total of their sum would be 80 + 1184 = 1264, that being the <total_encrypted_appdata_exchanged>.

Since we already found out the <frame_end>, for the <size_of_encrypted_message> we just needed to scroll down to the "Length" section of that frame to find what it was.

After doing that we had all the components we needed to rebuild the flag.

flag{814-819-TLS_RSA_WITH_AES_128_CBC_SHA256-1264-80}

# Network-Security (hw2)
# Homework 2 
> Scenario: 
Alice and Bob were talking about an important case of their company. They use websocket technic to communicate with each other. Since the websocket is not encrypted, Alice (172.17.0.4) and Bob (172.17.0.3) decided to use another https website (https://172.12.0.5/) to upload their secret document.

> Goal: You have to observe the traffic and get the secret document.

> Hint:
  * Wireshark
    * Wireshark can capture the traffic on an interface and perform some analysis with its powerful packet filter.
  * Websocket
    * Apply websocket filter using Wireshark to observe websocket related packets only.
  * HTTPs traffic
    * HTTPs handshake
  * RSA Common Factor Attack
    * RSA do have some security problems if the two primes used in RSA are not random enough.

> Given:
  * One WebSocket traffic pcapng file
  * 12 HTTPs traffic pcapng files

> Answer
* ***Step1: Find the public key***     
For one pcap file:   
Find the Certificate under Handshake Protocol and save as der file.   
Extract the RSA public key from der file and save it in a pem file.   
`openssl x509 -inform der -in cert.der -pubkey -noout > pub.pem`   
Get the module and the exponent.   
`openssl rsa -in pub.pem -pubin -modulus –noout`   
Extract public key from all pcap files.
* ***Step2: Find the gcd of all public key***  
>> **Why use gcd to break RSA?**    
**Case 1:** Four different primes: a, b, c, d.    
n1 = a × b   
n2 = c × d     
gcd(n1, n2)=1, n1 and n2 must be relatively prime to each other -> hard to know a,b,c,d   
**Case 2:** Three different primes: a, b, c.   
gcd(n1, n2)=b -> easy to know a,b,c ( a = n1 / b and c = n2 / b)   

Find the gcd of https3 file and https8 file is not equal to 1.
* ***Step3: Try to get private key from .pcapng file***   
>> Public key: (N,e)   
Private key: (N,d)   
N=p*q   
d%e=1 (mod(p-1)(q-1))   
Known: **N**, **e**   
Use get_private.py to get **p**, **q** and know private key **d** from http3.pcapng and http8.pcapng.
* ***Step4: Import the private key into .pcapng file***   
In http3.pcapng, we can get the data in zip format.
* ***Step5: Get the password in websocket.pcapng***   
Password: : )o()O(AliceForBob

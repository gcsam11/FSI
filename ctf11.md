# RSA

## Analysis

    As usual, we start off by analysing what we're given! So first let's look at what we're told!

    First thing we notice is that we must decipher a flag that's been encrypted using plain RSA. Problem is is that this was encrypted using values we can easily guess! These values are the primes **p**, which is close to 2 ^ 512, and **q**, which is close to 2 ^ 513.

    Let's now take our focused to how these the flag is encrypted! Looking at _challenge.py_, we see that it:

    - Reads the file _'/flags/flag.txt'_, which contains the flag we're looking for:

    ```python
    FLAG_FILE = '/flags/flag.txt'

    with open(FLAG_FILE, 'r') as fd:
	un_flag = fd.read()
    ```

    - Gets the parameters which are used for the RSA encryption, encrypts the flag and prints the public parameters and the ciphertext resulting from it!

    ```python
    print("Public parameters -- \ne: ", e, "\nn: ", n)
    print("ciphertext:", hexlify(enc(un_flag.encode(), e, n)).decode())
    sys.st
    ```
    
    We must note the following  functions:

    - Encrypter function, that given a plaintext message, the exponent _e_ and the product _n_, encrypts the message:

    ```python
    def enc(x, e, n):
    int_x = int.from_bytes(x, "little")
    y = pow(int_x,e,n)
    return hexlify(y.to_bytes(256, 'little'))
    ```

    - Decrypted function, that given an encrypted message, _d_ and the product _n_, decrypts the message.

    ```python
    def dec(y, d, n):
    int_y = int.from_bytes(unhexlify(y), "little")
    x = pow(int_y,d,n)
    return x.to_bytes(256, 'little')
    ```


## Exploiting the variability

    Analysing the code, what have we learnt? If we figure out **p** and **q** we can easily decrypt the ciphertext and get the flag. But how do we go about figuring out those primes?

    As this is very big numbers and as prime numbers are not easy to computate in this range, we are advised to use the **Miller-Rabin algorithm**. Looking online, we can easily find an implementation of it:

    ```python
    import random

    def miller_rabin(n, k):
    if n == 2:
        return True

    if n % 2 == 0:
        return False

    r, s = 0, n - 1
    while s % 2 == 0:
        r += 1
        s //= 2
    for _ in range(k):
        a = random.randrange(2, n - 1)
        x = pow(a, s, n)
        if x == 1 or x == n - 1:
            continue
        for _ in range(r - 1):
            x = pow(x, 2, n)
            if x == n - 1:
                break
        else:
            return False
            
    return True
    ```

    Now we can pick up this algorithm and easily get the next prime number with the following function:

    ```python
    def next_prime(num):
    num += 1 if num % 2 == 0 else 2

    while not miller_rabin(num, 40):
        num += 2
    
    return num
    ```

    It is also noteworthy that if we run _nc ctf-fsi.fe.up.pt 6004_, we given the public keys and the ciphertext:

    ```python
    # Public parameters
    e = 65537 
    n =  359538626972463181545861038157804946723595395788461314546860162315465351611001926265416954644815072042240227759742786715317579537628833244985694861279002376076148965114221950891522196203308822438723897396235881555224606126074332672093175387623354263464285679980671440667191096301871260912544465300286011668027
    ciphertext = "3364303637653531383539353832363338633136306664303636613430316564313666633962653239656338633764356164303337323834666664353135623137313732373339333332326363376361336534316139623731353263633163366139636161303737613835303435326362633836383663343039323930373834396265303337393131363336353632626263376237633831616162626563613232393035383139353663666663346534613830623635376130353133386338373838343039656263396232333463396163653662633363323664393437663439393637653361346464323365616266326465646130383464663035646339353230313030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030303030"
    
    ```


    Now we must simply compute the primes and use them to decrypt the message:

    ```python
    aux = a  = next_prime(2 ** 512)

b = next_prime(2 ** 513)

while True:
	while a * b < n:
		a = next_prime(a)
		continue
		
	if a * b == n:
		break

	b = next_prime(b)
	a = aux 

decKey = pow(e, -1,(a - 1) * (b - 1))
flag = dec(unhexlify(ciphertext), decKey, n).decode()
print(flag)
    ``` 

    Running this, we are able to get the flag, which is **flag{aea6730d12020536eeb40fe4a39063db}**!

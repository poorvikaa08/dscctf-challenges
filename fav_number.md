## FAVOURITE NUMBER:-

## Challenge Description

The challenge presented us with a guessing game hosted at favno.ctf.dscjssstuniv.in on port 9100. Upon connecting to the server using:
ncat favno.ctf.dscjssstuniv.in 9100

we received the following prompt:
```
Enter a number
```

```
My number's lost in 10^100 numbers... Help plz
```

This indicated that we had to guess a secret number within the range of  to .
Additionally, when we guessed certain numbers, we received hints such as:

•	"Too short" (indicating the guessed number was too small)

•	"Too long" (indicating the guessed number was too large)

•	A correct guess would reveal the flag.

Given these responses, we decided to use binary search to efficiently guess the number within the given range.

1)Start with an initial range: 
    We assumed the number was between 1050  and 10100.

2)Implement a binary search approach:
    • Start with the midpoint of the range.
    • Submit the guess and read the server’s response.
    • Adjust the search range based on whether the response says "too short" or "too long."
    • Repeat until we receive the correct response.

3)Handle reconnections: 
    The server sometimes disconnected, so we implemented a reconnection mechanism.

4)Optimize performance: 
    We added slight delays to prevent overwhelming the server.



## SCRIPT:-
```
from pwn import *
import time

def try_number(conn, num):
    try:
        conn.sendline(str(num).encode())
        response = conn.recvline(timeout=5).decode().strip()
        print(f"\nTried: {num}")
        print(f"Response: {response}")
        return response
    except EOFError:
        print("Connection closed by server")
        return "EOF"
    except Exception as e:
        print(f"Error: {e}")
        return None

def binary_search():
    # Starting with the number we saw in the screenshot
    left = 63963360780752101675578227747018040203072495023743153760807392592969653508644 * 10**40
    right = 63963360780752101675578227747018040203072495023743153760807392592969653508645 * 10**40

    conn = None

    try:
        while left <= right:
            # Create connection if we don't have one
            if conn is None:
                conn = remote('favno.ctf.dscjssstuniv.in', 9100)
                time.sleep(1)  # Wait after connection

            mid = (left + right) // 2
            response = try_number(conn, mid)

            # Handle connection issues
            if response == "EOF" or not response:
                print("Reconnecting...")
                try:
                    conn.close()
                except:
                    pass
                conn = None
                time.sleep(2)  # Wait before reconnecting
                continue

            # Skip help messages
            if "help" in response.lower() or "lost" in response.lower():
                continue

            if "too small" in response.lower():
                left = mid + 1
            elif "too big" in response.lower() or "too large" in response.lower():
                right = mid - 1
            elif "correct" in response.lower() or "flag" in response.lower():
                print(f"Found the number: {mid}")
                return mid

            print(f"Current range: [{left}, {right}]")
#            time.sleep(1)  # Wait between attempts

    except Exception as e:
        print(f"Fatal error: {e}")
    finally:
        if conn:
            try:
                conn.close()
            except:
                pass

    return None

try:
    print("Starting binary search...")
    result = binary_search()
    if result:
        print(f"The correct number is: {result}")
    else:
        print("Failed to find the number")
except Exception as e:
    print(f"An error occurred: {e}")

```

It revealed the flag: 
```
dscctf{y0u_4r3_4_sm4rt_arent_you}
```


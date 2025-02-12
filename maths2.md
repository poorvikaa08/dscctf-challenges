# MATHS2 

We were given a network-based challenge where we had to solve 100 math-related problems in a time-constrained environment. The problems included calculating the greatest common divisor (GCD), least common multiple (LCM), and the greatest prime factor (GPF) of given numbers. The challenge was hosted at `addition.ctf.dscjssstuniv.in` on port `9019`.

Upon connecting to the server using `nc addition.ctf.dscjssstuniv.in 9019`, we were presented with:
```
Welcome to maths 2! Time to setup the game..
Current round: 1 of 100
Give me the greatest common divisor of 47 and 127: 
```
## Solution Approach

 To automate solving these problems quickly, we wrote a Python script using `pwntools` to:
   - Connect to the challenge server.
   - Read and parse the math-related question.
   - Identify the operation (GCD, LCM, or GPF) from the question.
   - Compute the answer using appropriate Python functions.
   - Send the answer back to the server.
   - Repeat this process until all 100 rounds were completed.

## The Python Script

```
from pwn import *
import math

def connect_to_server(host, port):
    try:
        return remote(host, port)
    except Exception as e:
        print(f"Connection failed: {e}")
        exit(1)

def solve_gcd(a, b):
    return math.gcd(a, b)

def solve_lcm(a, b):
    return abs(a * b) // math.gcd(a, b)

def greatest_prime_factor(n):
    largest = 1
    while n % 2 == 0:
        largest = 2
        n = n // 2

    for i in range(3, int(math.sqrt(n)) + 1, 2):
        while n % i == 0:
            largest = i
            n = n // i

    if n > 2:
        largest = n

    return largest

def parse_question(question):
    try:
        question = question.replace(":", "").strip()
        print(f"Parsing question: {question}")

        words = question.split()
        numbers = []
        for word in words:
            clean_word = ''.join(c for c in word if c.isdigit())
            if clean_word:
                numbers.append(int(clean_word))

        print(f"Found numbers: {numbers}")

        if "greatest common divisor" in question.lower():
            return ("gcd", numbers)
        elif "least common multiple" in question.lower():
            return ("lcm", numbers)
        elif "greatest prime factor" in question.lower():
            return ("prime", numbers)

        return None
    except Exception as e:
        print(f"Failed to parse question: {e}")
        return None

def main():
    HOST = 'addition.ctf.dscjssstuniv.in'
    PORT = 9019

    conn = connect_to_server(HOST, PORT)

    try:
        conn.recvuntil(b"Time to step up the game...\n")

        for i in range(100):
            round_info = conn.recvline().decode()
            print(f"\n{round_info}")

            question = conn.recvuntil(b": ").decode()
            print(f"Question: {question}")

            result = parse_question(question)
            if result:
                op_type, numbers = result

                if op_type == "gcd" and len(numbers) >= 2:
                    answer = solve_gcd(numbers[0], numbers[1])
                    print(f"GCD of {numbers[0]} and {numbers[1]} = {answer}")
                elif op_type == "lcm" and len(numbers) >= 2:
                    answer = solve_lcm(numbers[0], numbers[1])
                    print(f"LCM of {numbers[0]} and {numbers[1]} = {answer}")
                elif op_type == "prime" and len(numbers) >= 1:
                    answer = greatest_prime_factor(numbers[0])
                    print(f"Greatest prime factor of {numbers[0]} = {answer}")
                else:
                    print("Invalid number of arguments")
                    continue

                print(f"Sending answer: {answer}")
                conn.sendline(str(answer).encode())
            else:
                print("Failed to parse question")
                break

            try:
                response = conn.recvline().decode()
                print(f"Response: {response}")
            except:
                pass

        # After 100 questions, keep receiving and printing any additional data
        print("\nChecking for additional server output:")
        while True:
            try:
                data = conn.recv(timeout=2).decode()
                if data:
                    print(data)
                else:
                    break
            except:
                break

    except Exception as e:
        print(f"An error occurred: {e}")

    finally:
        conn.close()

main()

```

We executed the script and it successfully solved all 100 problems within the time limit.

## Note

It depends a lot on the speed of the internet. It only had ran 12 cases when connected to weak internet. Your internet connection needs to be on steroids to run all 100 cases.

**FLAG**: `dscctf{maths2_prolly_fun_lollz!!!}`


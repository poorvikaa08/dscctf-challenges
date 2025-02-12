# Conditions

## Challenge Description

The challenge provides a function that validates the length of a username before granting access to the flag. The function follows this logic:
```
if len(username) >= 40:
    return "Username is too long!"
elif len(username.upper()) <= 50:
    return "Username is too short!"
else:
    return flag
```

Understanding the Constraints
1.	If the original length of username is 40 or more, it is rejected as "too long."
2.	If the uppercased version of username has a length of 50 or less, it is rejected as "too short."
3.	The only way to receive the flag is
    if: len(username)<40\text{len(username)} < 40len(username)<40 and len(username.upper())>50\text{len(username.upper())} > 50len(username.upper())>50

This means the username must expand beyond 50 characters after being converted to uppercase.


## Exploiting the Bug

The key observation is that certain Unicode characters, like "ß" (sharp S in German), expand when converted to uppercase:

• "ß" → "SS", increasing its length from 1 to 2.   
• By using 30 "ß" characters, we meet the conditions:  
• len(username) = 30, which is < 40 (passes the first check).   
• len(username.upper()) = 30 × 2 = 60, which is > 50 (bypasses the second check).   
• As a result, the function reaches return flag, granting access.


## Final Payload
ßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßßß

## Conclusion
This challenge exploits Unicode length expansion to bypass input validation, demonstrating how improper handling of Unicode transformations can lead to security vulnerabilities. Proper length checks should account for Unicode normalization to prevent such exploits.

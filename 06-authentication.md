# authentication

## Authentication with Passwords
Authentication is the process of verifying who a user is. If you don't have a secure authentication system, your back-end systems will be open to attack!

Imagine if I could make an HTTP request to the YouTube API and upload a video to your channel. YouTube's authentication system prevents this from happening by verifying that I am who I say I am.

### Passwords
Passwords are a common way to authenticate users. You know how they work: When a user signs up for a new account, they choose a password. When they log in, they enter their password again. The server will then compare the password they entered with the password that was stored in the database.

There are 2 really important things to consider when storing passwords:

1. **Storing passwords in plain text is awful**. If someone gets access to your database, they will be able to see all of your users' passwords. If you store passwords in plain text, you are giving away your users' passwords to anyone who gets access to your database.
2. **Password strength matters**. If you allow users to choose weak passwords, they will be more likely to reuse the same password on other websites. If someone gets access to your database, they will be able to log in to your users' other accounts.

We won't be writing code to validate password strength in this course, but you get the idea: you can enforce rules in your HTTP handlers to make sure passwords are of a certain length and complexity.

### Hashing 
On the other hand, we will be writing code to store passwords in a way that prevents them from being read by anyone who gets access to your database. This is called hashing. Hashing is a one-way function. It takes a string as input and produces a string as output. The output string is called a hash.

We'll cover how hashing works in-depth in a later course. For now, just know that hashing is a way to store passwords in a way that prevents them from being read by anyone who gets access to your database, but still allows us to compare passwords when a user logs in.

## Password Review
Passwords should be strong. It's a really bad idea for users to reuse the same passwords across sites. if someone figures out their password for one site, they can try it on other sites. If they get lucky, they can log in to and compromise many of their accounts.

### Passwords Should be Strong
The most important factor for the strength of a password is its entropy. Entropy is a measure of how many possible combinations of characters there are in a string. To put it simply:
- The longer the password the better (the best measure of password strength)
- Special characters and capitals should always be allowed
- Special characters and capitals aren't as important as length

Argon2id is a great choice. SHA-256 and MD5 are not.

## Types of Authentication
Here are a few of the most common authentication methods you'll see in the wild: 
1. Password + ID (username, email, etc.)
2. 3rd Party Authentication ("Sign in with Google", "Sign in with GitHub", etc.)
3. Magic Links
4. API Keys

## JWTs
There are several different ways to handle authentication. We'll use [JWTs](https://blog.boot.dev/cryptography/hmac-and-macs-in-jwts/) in this course. They're a popular choice for APIs that are consumed by web applications and mobile apps.
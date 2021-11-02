# How-To create hashed SSH password

```text
openssl passwd -1 -salt <8 random character for your choice> <the password you want>
```

```text
openssl passwd -1 -salt 'random-phrase-here' 'your-password-here
```


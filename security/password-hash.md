# Passwrod Hash

### bcrypt

Referneces:

- [Does bcrypt have a maximum password length?](https://security.stackexchange.com/questions/39849/does-bcrypt-have-a-maximum-password-length)
- [Pre-hash password before applying bcrypt to avoid restricting password length](https://security.stackexchange.com/questions/6623/pre-hash-password-before-applying-bcrypt-to-avoid-restricting-password-length)
- [usenix: Eksblowfish Algorithm](https://www.usenix.org/legacy/events/usenix99/provos/provos_html/node4.html)

> the key argument is a secret encryption key, which can be a user-chosen password of up to 56 bytes (including a terminating zero byte when the key is an ASCII string).

**Note:** There's a problem with allowing a client to use a unlimited password: it introduces a denial of service attack by someone submitting a multi-gigabyte password.

It's common to pre-hash a user's password with something like SHA2-256.

**Note:** Beware of unsalted pre-hashing. use a salted pre-hash. for example:

```
hash = bcrypt(sha256(salt, password));
```
